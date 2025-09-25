# SafeDelegate(SafeInvoke) 패턴 정리

## 1. 의도와 배경

-   **멀티캐스트 delegate**는 여러 핸들러를 체인으로 호출합니다.
-   기본 동작에서는 **앞 핸들러가 예외를 던지면 즉시 중단**하여 뒤
    핸들러는 실행되지 않습니다.
-   **SafeInvoke** 패턴은:
    -   모든 핸들러를 실행 (`try/catch`)
    -   발생한 예외는 수집
    -   마지막에 `AggregateException`으로 한 번에 보고\
        → "가능한 건 다 실행하고, 에러는 모아서 처리"

------------------------------------------------------------------------

## 2. 현재 코드 동작 요약

``` csharp
public static void SafeInvoke(this Delegate del, params object[] args)
{
    var exceptions = new List<Exception>();
    foreach (var handler in del.GetInvocationList())
    {
        try {
            handler.Method.Invoke(handler.Target, args);
        }
        catch (Exception ex) {
            exceptions.Add(ex);
        }
    }
    if (exceptions.Any())
        throw new AggregateException(exceptions);
}
```

-   `GetInvocationList()`로 핸들러 배열 가져옴
-   리플렉션으로 호출 → 비용이 큼
-   예외를 모아 `AggregateException`으로 던짐

------------------------------------------------------------------------

## 3. 개선 포인트

1.  **리플렉션 제거**
    -   델리게이트 타입이 고정이면 캐스팅 후 직접 호출
2.  **널/빈 처리**
    -   null 또는 빈 델리게이트일 경우 조용히 반환
3.  **스냅샷 호출**
    -   `var list = del.GetInvocationList();` 후 배열 고정 → 멀티스레드
        안전
4.  **에러 전파 정책 선택**
    -   끝에 묶어 던지기 or 즉시 로깅
5.  **Action/Func/async 지원**
    -   반환값 수집/무시 선택 가능\
    -   `Func<Task>`에 대한 `SafeInvokeAsync` 별도 제공

------------------------------------------------------------------------

## 4. 개선된 C# 예시

### Action 버전

``` csharp
public static void SafeInvoke(this Action del)
{
    if (del is null) return;
    var list = del.GetInvocationList().Cast<Action>().ToArray();

    List<Exception>? errors = null;
    foreach (var h in list)
    {
        try { h(); }
        catch (Exception ex) { (errors ??= new List<Exception>()).Add(ex); }
    }
    if (errors is { Count: > 0 }) throw new AggregateException(errors);
}
```

### Func 버전 (반환값 수집)

``` csharp
public static IReadOnlyList<TResult> SafeInvoke<TResult>(this Func<TResult> del)
{
    if (del is null) return Array.Empty<TResult>();
    var list = del.GetInvocationList().Cast<Func<TResult>>().ToArray();

    var results = new List<TResult>();
    List<Exception>? errors = null;

    foreach (var h in list)
    {
        try { results.Add(h()); }
        catch (Exception ex) { (errors ??= new List<Exception>()).Add(ex); }
    }
    if (errors is { Count: > 0 }) throw new AggregateException(errors);
    return results;
}
```

### Async 버전

``` csharp
public static async Task SafeInvokeAsync(this Func<Task> del)
{
    if (del is null) return;
    var list = del.GetInvocationList().Cast<Func<Task>>().ToArray();

    List<Exception>? errors = null;
    foreach (var h in list)
    {
        try { await h(); }
        catch (Exception ex) { (errors ??= new List<Exception>()).Add(ex); }
    }
    if (errors is { Count: > 0 }) throw new AggregateException(errors);
}
```

------------------------------------------------------------------------

## 5. C++ 유사 구현

C++에는 멀티캐스트 delegate가 없으므로 **핸들러 컨테이너**를 두고 순회
실행.

``` cpp
#include <functional>
#include <iostream>
#include <vector>
#include <exception>

struct AggregateError : std::exception {
    std::vector<std::exception_ptr> errs;
    const char* what() const noexcept override { return "aggregate errors"; }
};

template <typename F, typename... Args>
void SafeInvoke(const std::vector<F>& handlers, Args&&... args)
{
    std::vector<std::exception_ptr> errs;
    for (auto& h : handlers) {
        try { h(std::forward<Args>(args)...); }
        catch (...) { errs.push_back(std::current_exception()); }
    }
    if (!errs.empty()) {
        AggregateError ae; ae.errs = std::move(errs);
        throw ae;
    }
}

int main() {
    std::vector<std::function<void(const std::string&)>> hs;
    hs.emplace_back([](auto& s){ std::cout << "H1: " << s << "\n"; });
    hs.emplace_back([](auto&){ throw std::runtime_error("H2 failed"); });
    hs.emplace_back([](auto& s){ std::cout << "H3: " << s << "\n"; });

    try {
        SafeInvoke(hs, std::string("hello"));
    } catch (const AggregateError& ae) {
        std::cerr << "Some handlers failed (" << ae.errs.size() << ")\n";
    }
}
```

------------------------------------------------------------------------

## 6. 요약

-   **SafeInvoke** = 모든 핸들러 실행 후 예외를 모아 보고하는 안전 실행
    패턴
-   운영 환경에서 "에러로 인해 나머지 핸들러가 무시되지 않도록" 하는
    목적
-   C#은 멀티캐스트 delegate 확장에서 구현, C++은 콜백 컨테이너로 구현
-   개선 시 리플렉션 제거, async 대응, 정책 선택을 고려하는 것이 좋음
