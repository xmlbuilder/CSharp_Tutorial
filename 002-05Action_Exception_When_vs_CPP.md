# C# `Action` 예외 처리(when 필터) 정리 + C++ 유사 패턴

## 핵심 요약

-   `catch (...) when (expr)`의 **when 필터**는 *예외를 잡기 전에*
    `expr`을 평가해서 **true일 때만** 해당 `catch` 블록을 실행합니다.
-   **스택 언와인딩 전에** 필터가 평가되므로, 필터 평가 중 부작용(예:
    상태 변경, 추가 예외 발생)은 피하세요.
-   여러 `catch`가 있을 때 **위에서부터** 검사하며, 각 `catch`의 when
    조건이 false면 *그 블록은 건너뛰고* 다음 `catch`로 넘어갑니다.
-   `Action`(특히 멀티캐스트)의 경우, **첫 번째 델리게이트가 예외를
    던지면 이후 델리게이트 실행은 중단**됩니다.

------------------------------------------------------------------------

## 1) C# `when` 필터의 의미와 동작

``` csharp
try
{
    action.Invoke();
}
catch (Exception ex) when (ex.Message.Contains("when"))
{
    Console.WriteLine("Caught an exception with when");
}
catch (Exception ex)
{
    Console.WriteLine("Caught an exception without when");
}
```

-   `when`의 `expr`은 **bool**로 평가되어야 하며, `true`면 해당
    `catch`가 실행됩니다.
-   `expr`이 `false`면 이 `catch`는 *건너뛰고* 다음 `catch` 후보로
    넘어갑니다.
-   필터는 **언와인딩 전**에 평가되므로(예외가 "잡히기 전에" 판단),
    디버깅/로깅에 유리하고, 조건 불일치 시 *원본 스택을 보존*한 채로 더
    바깥으로 전파됩니다.
-   `when` vs `catch 후 if`의 차이:
    -   `when`은 **잡기 전에** 판단 → 스택 보존/전파 결정에 유리
    -   `catch + if`는 **잡은 뒤** 판단 → 조건 불일치 시 `throw;`로
        재던지면 *스택 프레임이 한 번 바뀐 것처럼 보일* 수 있음

> 주의: 필터 내부에서 **부작용**(상태 변경, I/O, 또 다른 예외) 발생은
> 피하세요. 필터는 "순수 판정"에 가깝게 쓰는 것이 안전합니다.

------------------------------------------------------------------------

## 2) `Action`(멀티캐스트)에서의 예외 처리

``` csharp
Action chain = null!;
chain += () => Console.WriteLine("A");
chain += () => throw new InvalidOperationException("B failed");
chain += () => Console.WriteLine("C"); // 실행되지 않음

try
{
    chain(); // Invoke와 동일
}
catch (InvalidOperationException ex) when (ex.Message.Contains("failed"))
{
    Console.WriteLine("Filtered by when: " + ex.Message);
}
```

-   멀티캐스트 델리게이트에서 **앞의 구독자**가 예외를 던지면 **즉시
    중단**되어 뒤의 구독자는 호출되지 않습니다.
-   "모두 호출 후 예외 수집"이 필요하면, **개별 invoke try/catch**로
    감싸고 예외를 모읍니다:

``` csharp
var handlers = chain.GetInvocationList()
                    .Cast<Action>();

var errors = new List<Exception>();
foreach (var h in handlers)
{
    try { h(); }
    catch (Exception e) { errors.Add(e); }
}

if (errors.Count > 0)
{
    // 필요 시 AggregateException 등으로 묶어서 처리
    throw new AggregateException(errors);
}
```

------------------------------------------------------------------------

## 3) C++에서의 유사 패턴

표준 C++에는 C#의 **예외 필터(`when`)**와 동일한 언어 기능이 없습니다.\
유사한 동작은 보통 **catch에서 조건 검사 후 재던지기**로 구현합니다.

### (1) `std::function<void()>` + 조건부 처리

``` cpp
#include <functional>
#include <iostream>
#include <stdexcept>
#include <string>

int main() {
    std::function<void()> action = [] {
        throw std::runtime_error("message for exception with when");
    };

    try {
        action();
    } catch (const std::exception& ex) {
        if (std::string(ex.what()).find("when") != std::string::npos) {
            std::cout << "Caught an exception with condition (C++)\n";
            // 처리 완료 (소비)
        } else {
            throw; // 조건 불일치 → 원본 예외 재전파
        }
    }
}
```

> 차이점: C++은 **이미 catch로 들어온 뒤** 조건 판단을 합니다.\
> C#의 `when`은 **catch 이전**(필터 단계)에서 판단합니다. 따라서 C++에선
> 스택 언와인딩 타이밍이 다를 수 있습니다.

### (2) 멀티캐스트 유사 패턴(여러 핸들러 호출 + 예외 수집)

``` cpp
#include <functional>
#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>

int main() {
    std::vector<std::function<void()>> handlers;
    handlers.emplace_back([] { std::cout << "A\n"; });
    handlers.emplace_back([] { throw std::runtime_error("B failed"); });
    handlers.emplace_back([] { std::cout << "C\n"; }); // 실행되게 하려면 개별 try 필요

    std::vector<std::exception_ptr> errors;

    for (auto& h : handlers) {
        try { h(); }
        catch (...) { errors.push_back(std::current_exception()); }
    }

    if (!errors.empty()) {
        // 필요하면 첫 번째 예외만 재던지거나, 모두 로깅
        try { std::rethrow_exception(errors.front()); }
        catch (const std::exception& ex) {
            std::cout << "First error: " << ex.what() << "\n";
        }
    }
}
```

------------------------------------------------------------------------

## 4) 보일러플레이트 줄이는 C++ 헬퍼

C# `when`의 "조건부 처리 후 전파"를 흉내 내는 헬퍼:

``` cpp
template <typename Pred, typename Handler>
bool catch_if(const std::exception& ex, Pred pred, Handler handle) {
    if (pred(ex)) { handle(ex); return true; }
    return false;
}

try {
    action();
} catch (const std::exception& ex) {
    if (catch_if(ex,
                 [](auto& e){ return std::string(e.what()).find("when") != std::string::npos; },
                 [](auto& e){ std::cout << "Handled: " << e.what() << "\n"; })) {
        // handled
    } else {
        throw; // rethrow if not matched
    }
}
```

------------------------------------------------------------------------

## 5) 실무 팁

-   **C#**
    -   `when` 필터는 **조건 분기 + 스택 보존**이 강점.\
        로깅/메트릭/특정 패턴 매칭 시 유용.
    -   필터에서 **외부 상태 변경/예외 발생 금지**(안정성 저하).
-   **C++**
    -   언어 차이로 **완전 동일**하게 만들 수는 없습니다.\
        대신 catch 내부에서 **조건 검사 후 재전파** 패턴을 표준화하세요.
    -   여러 콜백 실행 보장 + 예외 수집이 필요하면 **개별 try**로 감싸고
        `exception_ptr`를 사용해 모으세요.
