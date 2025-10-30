# 📘 C# Delegate & C++ `std::function` 비교 매뉴얼

## 1. 개념 비교 표
| 개념       | C# `delegate`                                 | C++ `std::function`                                 |
|------------|-----------------------------------------------|-----------------------------------------------------|
| **선언 방식** | `delegate void MyDelegate(string msg);`       | `std::function<void(std::string)>`                  |
| **함수 등록** | `MyDelegate d = SomeMethod;`                  | `std::function<void(std::string)> f = SomeFunction;`|
| **실행**     | `d("hi");`                                     | `f("hi");`                                           |
| **람다 지원**| 지원됨                                         | 지원됨                                               |
| **복수 메서드 호출** | 가능 (Multicast Delegate)                   | 불가 (하나의 함수만 가짐)                            |
| **대상 메서드 종류** | 인스턴스, 정적, 람다 등                       | 함수 포인터, 멤버 함수, 람다 등                      |
| **주 용도**   | 콜백, 이벤트, UI 바인딩                         | 콜백, 커맨드 패턴, 지연 실행 등                      |

------------------------------------------------------------------------

## 2. 왜 중요한가?

| 항목                            | 설명                                                             |
|---------------------------------|------------------------------------------------------------------|
| **상태 보존**                   | 내부에 멤버 변수를 두고 상태 기반 동작 가능                      |
| **람다 이해 기반**              | 람다는 사실상 익명 functor                                       |
| **`std::function` 대상**        | 함수/함수포인터뿐 아니라 functor도 받을 수 있음                  |
| **delegate와 Functor의 유사성** | 특정 인스턴스를 기억한 후 메서드를 호출한다는 점에서 유사         |

## 3. C# Delegate 기본 설명

- **역할**
    - 하나 또는 여러 메서드를 **대리 호출**하는 객체
    - **메서드 포인터 역할** (C/C++ 함수 포인터 유사)
    - 접근 가능한 메서드만 호출 가능 (인스턴스/정적)

- **문법**
    ``` csharp
    delegate 반환형 Delegate이름(매개변수);
    ```

- **주요 용도**

    - 콜백(callback)
    - 이벤트(event)
    - 다형적 메서드 호출 패턴 지원

- **람다/이벤트와 관계**

    - 람다식은 델리게이트를 간결하게 표현
    - 이벤트는 델리게이트 기반으로 구현됨

## 4. C# Delegate 사용 예제

```csharp
using System;

delegate void DelegateType(string str);
```
```csharp
class A
{
    public void Print(string str) => Console.WriteLine($"Print: {str}");
}
```
```csharp
class Program
{
    static void Main(string[] args)
    {
        A test = new A();

        // 방법 1: 명시적 new
        DelegateType del1 = new DelegateType(test.Print);
        del1("TEST1");

        // 방법 2: new 생략
        DelegateType del2 = test.Print;
        del2("TEST2");

        // 방법 3: 람다식
        DelegateType del3 = (msg) => Console.WriteLine("Lambda: " + msg);
        del3("TEST3");

        // 방법 4: 정적 메서드
        DelegateType del4 = StaticPrint;
        del4("TEST4");
    }

    static void StaticPrint(string msg) => Console.WriteLine("Static: " + msg);
}
```

## 5. C++ `std::function` 사용 예제

```cpp
#include <iostream>
#include <functional>

class MyClass {
public:
    void Print(const std::string& msg) {
        std::cout << "C++: " << msg << std::endl;
    }
};
```
```cpp
int main() {
    MyClass obj;
    std::function<void(std::string)> func = [&obj](std::string msg) {
        obj.Print(msg);
    };
    func("Hello");
}
```

## 6. Functor (C++ 함수 객체)

- **정의**: 함수처럼 동작하는 객체  
    (`operator()` 연산자를 오버로드한 클래스)
- **특징**
    - 함수 포인터처럼 호출 가능
    - 내부 상태(state) 보존 가능

```cpp
#include <iostream>
#include <string>

struct Functor {
    void operator()(const std::string& msg) const {
        std::cout << "Functor: " << msg << std::endl;
    }
};
```
```cpp
int main() {
    Functor f;
    f("Hello"); // 함수처럼 호출
}
```


## 7. Delegate와 Functor의 유사성

- **공통점**
    - 객체에 바인딩된 함수 호출 표현 가능
    - 상태를 유지할 수 있음
    - 람다와 호환 가능
- **차이점**
    -   C++ Functor는 클래스 기반
    -   C# Delegate는 런타임 레벨에서 메서드 참조 + 인스턴스 기억

``` csharp
delegate void MyDelegate(string msg);

class FunctorLike
{
    public string Prefix;
    public void Print(string msg) => Console.WriteLine($"{Prefix}: {msg}");
}

var obj = new FunctorLike { Prefix = "C#" };
MyDelegate del = obj.Print;
del("Hello");  // C#: Hello
```

## 8. 결론

- **C# Delegate**
    이벤트, UI 바인딩, 멀티캐스트 콜백에 적합  
- **C++ std::function / Functor**
    범용적인 함수 포인터 대체, 커맨드 패턴, 상태 보존 연산자에 적합

- ➡️ **C# delegate는 이벤트 중심, C++ std::function은 함수 추상화 중심**

---

