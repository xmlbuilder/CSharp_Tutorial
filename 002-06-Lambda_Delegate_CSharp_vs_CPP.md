# C# 람다식(delegate 연결)과 C++ 람다 비교

## 1. C# 람다와 delegate

-   **delegate**는 메서드를 참조할 수 있는 형식.
-   **람다식**은 `=>` 연산자를 사용해 간단히 익명 메서드를 정의.
-   선언 방식:
    -   `delegate` 키워드로 직접 익명 함수 생성
    -   `=>` 람다 연산자로 식 본문 / 문 블록 정의

### C# 예제

``` csharp
using System;

delegate int IntOp(int lhs, int rhs);

class Program
{
    public static void Main(String[] args)
    {
        // 1) delegate 키워드로 익명 함수
        IntOp add = delegate(int lhs, int rhs)
        {
            return lhs + rhs;
        };
        
        // 2) 람다식 - 블록 본문
        IntOp sub = (lhs, rhs) =>
        {
            return lhs - rhs;
        };
        
        // 3) 람다식 - 식 본문
        IntOp mul = (lhs, rhs) => lhs * rhs;
        
        Console.WriteLine($"2 + 3 = {add(2, 3)}");  // 5
        Console.WriteLine($"2 * 3 = {mul(2, 3)}");  // 6
        Console.WriteLine($"2 - 3 = {sub(2, 3)}");  // -1
    }
}
```

------------------------------------------------------------------------

## 2. C++의 람다 표현식

-   C++도 `[](...) { ... }` 문법으로 익명 함수를 정의.
-   반환형은 `-> type`으로 명시 가능 (대부분 `auto` 추론됨).
-   `std::function`으로 delegate처럼 타입을 정의할 수 있음.

### C++ 대응 예제

``` cpp
#include <iostream>
#include <functional>

int main() {
    // delegate 역할: std::function<int(int,int)>
    std::function<int(int,int)> add = [](int lhs, int rhs) {
        return lhs + rhs;
    };

    std::function<int(int,int)> sub = [](int lhs, int rhs) {
        return lhs - rhs;
    };

    std::function<int(int,int)> mul = [](int lhs, int rhs) -> int {
        return lhs * rhs;
    };

    std::cout << "2 + 3 = " << add(2, 3) << std::endl; // 5
    std::cout << "2 * 3 = " << mul(2, 3) << std::endl; // 6
    std::cout << "2 - 3 = " << sub(2, 3) << std::endl; // -1
}
```

------------------------------------------------------------------------

## 3. 비교 정리

  ------------------------------------------------------------------------------------
  항목                         C#                 C++
  ---------------------------- ------------------ ------------------------------------
  기본 단위                    `delegate` 타입    `std::function` (또는 auto 람다)

  문법                         `(args) => expr`   `[capture](args) -> type { body }`

  반환형                       명시하지 않아도 됨 대부분 추론되며 필요 시 `-> type`
                               (자동 추론)        

  멀티캐스트                   지원 (`+=`로 여러  직접 컨테이너(vector 등)에 람다 넣어
                               메서드 연결)       호출

  주요 용도                    이벤트 핸들링,     STL 알고리즘(`std::for_each`,
                               비동기 콜백, LINQ  `std::sort` 등)
  ------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 4. 핵심 요약

-   **C#**: `delegate`와 `람다식`은 동일한 델리게이트 타입에 할당 가능.
    멀티캐스트(`+=`) 가능.\
-   **C++**: `std::function`과 람다로 유사하게 구현 가능. STL 알고리즘과
    잘 결합.\
-   두 언어 모두 **익명 함수**를 직접 만들고, 변수에 저장해 호출할 수
    있음.
