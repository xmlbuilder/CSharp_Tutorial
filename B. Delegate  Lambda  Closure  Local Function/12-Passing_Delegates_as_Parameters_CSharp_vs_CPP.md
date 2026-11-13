## 📘 Passing Delegates as Parameters (C#) - C++ 유사 패턴

### 📌 의도

- **콜백(Callback) 주입**: 호출자가 에러 처리/정책을 함수로 넘겨서, **동작을 주입**(Dependency Injection)하는 패턴.
- **관심사 분리**: `DoSomething`은 *무엇을 할지*만, 구체적인 에러 처리 방식은 **외부에서 전달**.
- **테스트 용이성**: 다른 핸들러를 넣어 동작을 쉽게 검증 가능.

------------------------------------------------------------------------

### 📌 1) C# 예제 (질문 코드)

``` csharp
class FuncAsParameters
{
    public void Run()
    {
        DoSomething(ErrorHandler1);
        //
        DoSomething(ErrorHandler2);
        // Error Message
    }

    public bool ErrorHandler1(string message) => true;   // 오류를 "소비"했다고 신호
    public bool ErrorHandler2(string message) => false;  // 오류를 "소비하지 못함"

    public void DoSomething(Func<string, bool> errorHandler)
    {
        if (!errorHandler("Some error Occurred"))
        {
            Console.WriteLine("Error Message");
        }
    }
}
```

#### 🔹 동작
- `ErrorHandler1`을 넘기면 `true` → 내부 `if (!true)`는 false → 출력 없음.
- `ErrorHandler2`를 넘기면 `false` → 내부 `if(!false)`는 true → **"Error Message"** 출력.

> 핵심: **`Func<string,bool>`** 시그니처로 "메시지를 입력받아 처리 성공
> 여부(bool)"를 반환하도록 **프로토콜** 을 정한 것.

### 📌 2) C++ 유사 구현

- C++에선 `std::function<bool(const std::string&)>`이나 템플릿 매개변수로 **콜백** 을 받습니다.

#### 🔹 (A) `std::function` 버전

``` cpp
#include <functional>
#include <iostream>
#include <string>

struct FuncAsParameters {
    static bool ErrorHandler1(const std::string& message) {
        return true; // 오류를 소비
    }
    static bool ErrorHandler2(const std::string& message) {
        return false; // 소비하지 못함
    }

    void DoSomething(const std::function<bool(const std::string&)>& errorHandler) {
        if (!errorHandler("Some error Occurred")) {
            std::cout << "Error Message\n";
        }
    }

    void Run() {
        DoSomething(ErrorHandler1);
        DoSomething(ErrorHandler2); // "Error Message"
    }
};

int main() {
    FuncAsParameters f;
    f.Run();
}
```

#### 🔹출력 
```
    Error Message
```
#### 🔹 (B) 템플릿(제네릭) 버전

``` cpp
#include <iostream>
#include <string>

struct FuncAsParametersFast {
    template <typename ErrorHandler>
    void DoSomething(ErrorHandler&& errorHandler) {
        if (!std::forward<ErrorHandler>(errorHandler)("Some error Occurred")) {
            std::cout << "Error Message\n";
        }
    }
};
```

- `std::function`의 **타입 소거 비용** 없이 **인라인 최적화** 가능.
- 사용 시 함수 포인터, 람다, 펑터 모두 자연스럽게 전달됨.

#### 🔹 (C) 다양한 전달 예 (C++)

``` cpp
bool EH1(const std::string&) { return true; }
bool EH2(const std::string&) { return false; }

int main() {
    FuncAsParametersFast f;

    // 1) 함수 포인터
    f.DoSomething(&EH1);
    f.DoSomething(&EH2); // Error Message

    // 2) 람다 - 캡처 없음
    f.DoSomething([](const std::string&){ return true; });

    // 3) 람다 - 상태 의존(캡처 있음)
    bool handled = false;
    f.DoSomething([&](const std::string& msg){
        handled = (msg.size() > 0);
        return handled; // true면 내부 출력 안 됨
    });
}
```


### 📌 3) 빠른 매핑 표


| 개념                   |  C#                            |  C++ |
|------------------------|--------------------------------|-------|
| 콜백 타입              |  `Func<string,bool>`           |  `std::function<bool(const std::string&)>` 또는 템플릿 매개변수 |
| 메서드 그룹/정적 함수   | `DoSomething(ErrorHandler1)`   | `DoSomething(&ErrorHandler1)` (암시적 변환) |
| 람다                   |  `s => true/false`             | `[](const std::string& s){ ... }` |
| 반환 프로토콜           | `bool` (true=소비 완료)        | 동일 (true=소비 완료) |
| 성능                   |  델리게이트 호출                | `std::function`은 약간의 오버헤드 템플릿은 0-cost |


---

