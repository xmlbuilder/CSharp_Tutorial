# C# Delegate 사용 패턴 ↔ C++ 대응 예제

아래는 질문에 주신 **C# delegate 사용 방법**(메서드 그룹, 익명 메서드,
람다, 고차 함수 인자로 전달)을 **C++**에서 `std::function`/람다로 **동일
패턴**으로 구현한 예제입니다.

------------------------------------------------------------------------

## 1) C# 원본 (요약)

``` csharp
using System;

namespace Test
{
    class Program
    {
        public delegate bool DelegateInvoker(string input);
        
        public static bool WriteToConsole(string input)
        {
            Console.WriteLine($"WriteToConsole {input}");
            return true;
        }

        public static void InvokeDelegator(DelegateInvoker func)
        {
            var ret = func("hello World!");
            Console.WriteLine($"> delegator returned {ret}");
        }
        
        public static void Main(String[] args)
        {
            // 1) 메서드 그룹
            InvokeDelegator(WriteToConsole);

            // 2) 익명 메서드 (delegate 키워드)
            DelegateInvoker di = delegate(string input)
            {
                Console.WriteLine($"di: {input}");
                return true;
            };
            InvokeDelegator(di);

            // 3) 람다
            InvokeDelegator(input => false);
        }
    }
}
```

**예상 출력**

    WriteToConsole hello World!
    > delegator returned True
    di: hello World!
    > delegator returned True
    > delegator returned False

------------------------------------------------------------------------

## 2) C++ 대응: `std::function<bool(const std::string&)>` + 람다

-   C#의 `delegate bool(string)`에 대응해 C++에선 보통
    **`std::function<bool(const std::string&)>`** 를 사용합니다.
-   C#의 **메서드 그룹 / 익명 메서드 / 람다**는 C++의 **함수 포인터 /
    람다(캡처 없음/있음) / functor**로 매핑됩니다.

``` cpp
#include <functional>
#include <iostream>
#include <string>

// 1) C#의 public static bool WriteToConsole(string)
bool WriteToConsole(const std::string& input) {
    std::cout << "WriteToConsole " << input << "\n";
    return true;
}

// 2) C#의 InvokeDelegator(DelegateInvoker func)
void InvokeDelegator(const std::function<bool(const std::string&)>& func) {
    bool ret = func("hello World!");
    std::cout << "> delegator returned " << (ret ? "True" : "False") << "\n";
}

int main() {
    // --- 1) 메서드 그룹: free function 전달 (함수 포인터 → std::function으로 변환) ---
    InvokeDelegator(WriteToConsole);

    // --- 2) 익명 메서드: C++에선 "람다"로 표현 (캡처 없음) ---
    std::function<bool(const std::string&)> di = [](const std::string& input) {
        std::cout << "di: " << input << "\n";
        return true;
    };
    InvokeDelegator(di);

    // --- 3) 람다 직접 전달 ---
    InvokeDelegator([](const std::string& /*input*/) {
        return false;
    });

    return 0;
}
```

**예상 출력**

    WriteToConsole hello World!
    > delegator returned True
    di: hello World!
    > delegator returned True
    > delegator returned False

------------------------------------------------------------------------

## 3) (옵션) 상태 캡처가 필요한 경우

C#의 클로저처럼 상태를 캡처하여 동작을 바꾸고 싶다면 C++ 람다의 **캡처
목록**을 사용합니다.

``` cpp
#include <functional>
#include <iostream>
#include <string>

void InvokeDelegator(const std::function<bool(const std::string&)>& func) {
    bool ret = func("hello World!");
    std::cout << "> delegator returned " << (ret ? "True" : "False") << "\n";
}

int main() {
    int threshold = 5;

    // 값 캡처 (스냅샷)
    InvokeDelegator([threshold](const std::string& s) {
        return static_cast<int>(s.size()) > threshold;
    });

    // 참조 캡처 (호출 시점 상태 반영)
    InvokeDelegator([&threshold](const std::string& s) {
        return static_cast<int>(s.size()) > threshold;
    });

    return 0;
}
```

> **주의**: C++에서 **참조 캡처**는 변수 수명에 주의해야 합니다(스코프
> 종료 후 참조 금지).\
> 장기간 저장/비동기 호출 시엔 `std::shared_ptr`를 값 캡처해 안전하게
> 공유하는 패턴이 좋습니다.

------------------------------------------------------------------------

## 4) 빠른 매핑 표

  C#                                    C++
  ------------------------------------- -------------------------------------------
  `delegate bool(string)`               `std::function<bool(const std::string&)>`
  메서드 그룹 전달                      함수 포인터/정적 함수 전달
  `delegate(...) { ... }` 익명 메서드   람다 `[](...) { ... }`
  람다 `input => expr`                  람다 `[capture](args) -> ret { body }`
  고차함수(델리게이트 인자)             `std::function` 인자
  멀티캐스트 `+=`                       벡터에 람다 push 후 순회 호출

------------------------------------------------------------------------

## 5) 성능 참고

-   `std::function`은 **타입 소거**로 약간의 오버헤드가 생길 수
    있습니다.\
    핫패스에서는 템플릿으로 **일반화 함수**를 사용해 *auto 람다*를 직접
    받는 것이 더 빠를 수 있습니다.

``` cpp
template <typename F>
void InvokeDelegatorFast(F&& f) {
    bool ret = std::forward<F>(f)("hello World!");
    std::cout << "> delegator returned " << (ret ? "True" : "False") << "\n";
}
```

------------------------------------------------------------------------

위 C++ 예제를 그대로 빌드하면 C# 출력과 동일한 형태로 동작합니다.
