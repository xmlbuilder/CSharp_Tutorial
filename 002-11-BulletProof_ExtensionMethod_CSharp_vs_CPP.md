# C# Extension Method 기반 Bullet-Proofing 패턴 ↔ C++ 구현

## 목적

-   **공통 에러 처리/로깅**을 한 군데로 모아 **업무 코드의
    잡음(noise)**을 줄이고,
-   정상/오류 흐름을 **명확하게 분리**하려는 유틸리티 패턴입니다.
-   C#에선 **확장 메서드(Extension Method)**로 예외 로깅 API를
    자연스럽게 붙이고,
-   실행 래퍼 `Proof(Action code, Action<Exception> error)`로
    **"실행-예외처리"** 보일러플레이트를 제거합니다.

------------------------------------------------------------------------

## 1) C# 구성요소 분석

### (1) 실행 래퍼: `Bullet.Proof`

``` csharp
public static void Proof(Action code, Action<Exception> error)
{
    try
    {
        code();
    }
    catch (Exception ex)
    {
        ex.Log("BP2200-ERR-Unexpected Error");
        error(ex);
    }
}
```

-   `code` : 실제 비즈니스 로직(예외 발생 가능)
-   `error`: 예외 후 **콜백**(상태 업데이트, 사용자 메시지 변환 등)
-   내부에서 `ex.Log(...)`로 **일관된 로깅 규약**을 강제

### (2) 확장 메서드: `Exception.Log(...)`

``` csharp
public static void Log(this Exception error, string messageID)
{
    Console.WriteLine(messageID);
    Console.WriteLine(error.Message);
    Console.WriteLine(error.StackTrace);
    Console.WriteLine("");
}
```

-   `this Exception` 시그니처로 **확장 메서드** 구현 → `ex.Log(...)`처럼
    인스턴스 메서드처럼 사용
-   메시지 ID 규격(`BP2200-...`)과 스택 트레이스를 통일된 포맷으로 출력

### (3) 사용 패턴

``` csharp
string result = string.Empty;
Bullet.Proof(
    () => {
        result = "DST5900-INF-NO Exception in this code";
        throw new Exception("MESSAGE");
    },
    error => {
        result = "DTS6200-ERR-An Execption happend look at console log";
        if (error.Message.Contains("SomeMarker"))
            result = "DST6500-ERR-Some marker was founded in the execption";
    }
);
```

-   정상 흐름은 `code`에서, 오류 시나리오는 `error` 콜백에서 처리 →
    **흐름 분기 명확**

> 참고: 지금 구현은 **반환값 없는 `Action` 전용**입니다. 필요하면
> `Func<T>` 버전을 추가해 결과를 반환하도록 확장하세요.

------------------------------------------------------------------------

## 2) C# 개선 아이디어 (선택)

### (A) 반환값 지원 (`Func<T>`)

``` csharp
public static T Proof<T>(Func<T> code, Func<Exception, T> error)
{
    try { return code(); }
    catch (Exception ex)
    {
        ex.Log("BP2200-ERR-Unexpected Error");
        return error(ex);
    }
}
```

### (B) 메시지 ID/로거 주입

``` csharp
public static void Proof(Action code, Action<Exception> onError, string messageId,
                         Action<string>? log = null)
{
    try { code(); }
    catch (Exception ex)
    {
        (log ?? Console.WriteLine)?.Invoke($"{messageId}\n{ex}");
        onError(ex);
    }
}
```

------------------------------------------------------------------------

## 3) C++로의 대응 구현

C++에는 **확장 메서드** 개념이 없습니다. 대신, - **네임스페이스의 자유
함수**로 로깅을 제공하고, - **실행 래퍼(`Proof`)**는 템플릿/람다로
구성합니다.

### (1) C++ 유틸: `bullet::proof` & `bullet::log`

``` cpp
#include <exception>
#include <functional>
#include <iostream>
#include <string>

namespace bullet {

inline void log(const std::exception& e, const std::string& messageId) {
    std::cout << messageId << "\n";
    std::cout << e.what() << "\n";
    // C++ 표준엔 스택트레이스가 없으므로, 필요 시 외부 라이브러리 사용 권장.
    std::cout << "\n";
}

template <typename FCode, typename FErr>
void proof(FCode&& code, FErr&& on_error, const std::string& messageId = "BP2200-ERR-Unexpected Error")
{
    try {
        std::forward<FCode>(code)();
    } catch (const std::exception& ex) {
        log(ex, messageId);
        std::forward<FErr>(on_error)(ex);
    }
}

} // namespace bullet
```

-   C#의 `ex.Log(...)`를 **`bullet::log(ex, "...")`**로 대체
-   `proof`는 C#의 `Bullet.Proof`와 동일 개념: **실행-예외-로깅-후처리**

### (2) 사용 예 (C++)

``` cpp
#include <iostream>
#include <string>

int main() {
    std::string result;
    bullet::proof(
        [&] { // code
            result = "DST5900-INF-NO Exception in this code";
            throw std::runtime_error("MESSAGE");
        },
        [&](const std::exception& e) { // error
            result = "DTS6200-ERR-An Execption happend look at console log";
            if (std::string(e.what()).find("SomeMarker") != std::string::npos) {
                result = "DST6500-ERR-Some marker was founded in the execption";
            }
        }
    );

    std::cout << result << "\n";
}
```

**출력 예시(개념상):**

    BP2200-ERR-Unexpected Error
    MESSAGE

    DTS6200-ERR-An Execption happend look at console log

> 주의: C++ 표준은 스택 트레이스 표준 API가 없습니다. (C++23
> `std::stacktrace`가 일부 구현에 존재)\
> 실무에선 `std::stacktrace`(지원 구현), Boost.Stacktrace, 플랫폼 API를
> 사용해 보강하세요.

### (3) 반환값 지원 버전

``` cpp
namespace bullet {
template <typename FCode, typename FErr>
auto proof_ret(FCode&& code, FErr&& on_error, const std::string& messageId = "BP2200-ERR-Unexpected Error")
    -> decltype(code())
{
    try { return code(); }
    catch (const std::exception& ex) {
        log(ex, messageId);
        return on_error(ex);
    }
}
} // namespace bullet
```

------------------------------------------------------------------------

## 4) 요약

-   **C#**: `Exception` 확장 메서드 + `Proof` 래퍼로 **로깅 규약**과
    **오류 분기**를 표준화.
-   **C++**: 확장 메서드는 없으므로 **네임스페이스 자유함수**로 대체,
    `proof` 템플릿으로 동일한 흐름 구성.
-   필요 시 **반환형 지원**, **로거 주입**, **메시지 ID 정책** 등을 양
    언어 모두에서 확장 가능.
