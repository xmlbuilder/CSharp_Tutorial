# 📘 C# Delegate / Func / Action 정리

C#에서 **Delegate**, **Func**, **Action**은 이벤트 처리, 비동기 프로세스 처리, 콜백 설계 등에서 필수적인 요소입니다.  
  → 공통적으로 **메서드를 객체처럼 다룰 수 있도록** 해주며, 코드 재사용성과 유연성을 크게 높여줍니다.

## 1. Delegate

- **정의**: 메서드를 참조하는 객체. 메서드 시그니처(매개변수, 반환형)와 동일해야 함.
- **특징**
  - 명시적으로 정의해야 함
  - 이벤트 처리, 콜백, 메서드를 매개변수로 전달할 때 유용
- **사용 예제**

```csharp
using System;

public delegate float MyDelegate(int a, int b);

public class MyClass
{
    public static float Divide(int a, int b)
    {
        return (float)a / b;
    }
}
```
```csharp
public class Program
{
    public static void Main()
    {
        MyDelegate del = MyClass.Divide;

        float ret = del(10, 5);
        Console.WriteLine("return: " + ret); // return: 2
    }
}
```

## 2. `Func`

- **정의**: 반환 타입이 `void`가 아닌 메서드를 표현하는 **제네릭 델리게이트**  
- **형식**: `Func<T1, T2, ..., TResult>`  
  - 마지막 타입 매개변수 `TResult`는 **반환형**  
  - 앞의 타입 매개변수들은 **입력 매개변수**

- **사용 예제**

```csharp
using System;

public class MyClass
{
    public static int GetNumber()
    {
        return 42;
    }

    public static string ToString(int number)
    {
        return "Number: " + number;
    }
}
```
```csharp
public class Program
{
    public static void Main()
    {
        Func<int> numberFunc = MyClass.GetNumber;
        int number = numberFunc();
        Console.WriteLine("Number: " + number); // Number: 42

        Func<int, string> toStringFunc = MyClass.ToString;
        string result = toStringFunc(42);
        Console.WriteLine(result); // Number: 42
    }
}
```

## 3. `Action`

- **정의**: 반환 타입이 `void`인 메서드를 표현하는 **제네릭 델리게이트**
- **형식**: `Action<T1, T2, ...>` (입력 매개변수만 존재)
- **특징**
  - `Func`와 달리 반환값이 없음
  - 사용자 정의 Delegate 대신 코드 간소화 가능

- **사용 예제**

```csharp
using System;

namespace GrammarTest
{
    public class Program
    {
        public class Greeter
        {
            public void WriteInstance()
            {
                Console.WriteLine("Instance");
            }

            public static void WriteStatic()
            {
                Console.WriteLine("Static");
            }
        }
        
        public static void Main(String[] args)
        {
            Action action1 = () => Console.WriteLine("Hello delegates");
            Action action2 = () => Console.WriteLine("Hello delegates");

            Action action1Again = action1;

            Console.WriteLine(action1.Equals(action1Again)); // True
            Console.WriteLine(action1.Equals(action2));      // False

            Action @static = Greeter.WriteStatic;
            @static(); // Static
        }
    }
}
```

## 4. C++에서의 유사 구현

C++에서는 **`std::function` + 함수 포인터/람다/Functor**를 이용하여 C# Delegate와 유사하게 동작할 수 있습니다.

```cpp
#include <iostream>
#include <functional>

class MyClass {
public:
    static float Divide(int a, int b) {
        return static_cast<float>(a) / b;
    }
};

int main() {
    std::function<float(int, int)> customDelegator;

    customDelegator = MyClass::Divide;
    float result = customDelegator(10, 5);

    std::cout << result << std::endl; // 2
    return 0;
}
```

## ✅ 요약 비교

| 항목 | Delegate | Func | Action | C++ std::function |
|------|----------|------|--------|-------------------|
| 반환 | 정의 필요 (임의) | 지정된 제네릭 매개변수 마지막이 반환 | 항상 `void` | 지정 가능 |
| 입력 | 정의된 시그니처 | 제네릭 타입 매개변수 (최대 16개) | 제네릭 타입 매개변수 (최대 16개) | 자유롭게 지정 |
| 선언 | 명시적 정의 필요 | 기본 제공 제네릭 | 기본 제공 제네릭 | `std::function<R(Args...)>` |
| 주요 용도 | 이벤트, 콜백 | 값 반환하는 메서드 표현 | void 메서드 표현 | 범용 함수 객체 저장소 |

- 👉 Delegate는 **기본 개념**, Func/Action은 **편의 제공 제네릭 Delegate**  
- 👉 C++은 **`std::function` + 람다/함수포인터/Functor**로 동일 개념을 지원 

---
