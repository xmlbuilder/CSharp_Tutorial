# Reflection Method/CreateInstance
C# 예제는 **리플렉션(Reflection)**을 활용해 런타임에 메서드를 호출하고 객체를 생성하는 방법.

## 🧠 C# 리플렉션 핵심 요약
```csharp
using System.Numerics;

namespace  GrammarTest
{
    public class Program
    {
        public class MyClass
        {
            public MyClass(int i, int j, string str)
            {
                Console.WriteLine($"{i}, {j} : {str}");
            }
        }
        
        public static void Main(String[] args)
        {
            
            var theString = "hello";
            var method1 = theString.GetType().GetMethod("Substring", new[] {typeof(int), typeof(int)});
            var result1 = method1.Invoke(theString, new object[] {0, 4});
            Console.WriteLine(result1);
            //hell
            
            var method2 = typeof(Math).GetMethod("Exp");
            var result2 = method2.Invoke(null, new object[] {2.0});
            Console.WriteLine(result2);
            //7.38905609893065
            
            Type type = typeof(BigInteger);
            object? result = Activator.CreateInstance(type, 123);
            Console.WriteLine(result);
            //123
            
            var myStructure = Activator.CreateInstance(typeof(MyClass), new object [] {1, 2, "sample"});
            //1, 2 : sample
            
        }
    }
}
```

| 기능                           | 설명                                      |
|--------------------------------|-------------------------------------------|
| `GetType().GetMethod(...)`     | 런타임에 특정 메서드 정보를 가져옴         |
| `MethodInfo.Invoke(...)`       | 가져온 메서드를 인스턴스 또는 static으로 실행 |
| `Activator.CreateInstance(...)`| 런타임에 객체를 생성함                     |

예시
```csharp
var method = typeof(Math).GetMethod("Exp");
var result = method.Invoke(null, new object[] { 2.0 }); // 7.389...
```


## ☕ Java: Reflection API
Java는 java.lang.reflect 패키지를 통해 C#과 거의 동일한 기능을 제공합니다.
### 🔧 메서드 호출
```java
import java.lang.reflect.Method;

String str = "hello";
Method method = String.class.getMethod("substring", int.class, int.class);
String result = (String) method.invoke(str, 0, 4);
System.out.println(result); // hell
```

## 🏗️ 객체 생성
```java
import java.lang.reflect.Constructor;

class MyClass {
    public MyClass(int i, int j, String str) {
        System.out.println(i + ", " + j + " : " + str);
    }
}

Constructor<MyClass> ctor = MyClass.class.getConstructor(int.class, int.class, String.class);
MyClass obj = ctor.newInstance(1, 2, "sample");
```

Java는 예외 처리가 필수입니다: NoSuchMethodException, InvocationTargetException, IllegalAccessException 등


## 🐍 Python: getattr, __import__, type
Python은 매우 유연해서 리플렉션이 자연스럽게 사용됩니다.

### 🔧 메서드 호출
```pythoin
the_string = "hello"
method = getattr(the_string, "find")
result = method("e")
print(result)  # 1
```


### 🏗️ 객체 생성
```python
class MyClass:
    def __init__(self, i, j, str_):
        print(f"{i}, {j} : {str_}")

cls = globals()["MyClass"]
obj = cls(1, 2, "sample")
```

Python은 getattr, setattr, type, __import__ 등을 통해 매우 동적이고 직관적인 리플렉션을 제공합니다.


## 💠 C++: 제한적 지원 (RTTI)
C++은 전통적으로 리플렉션을 지원하지 않지만, **RTTI (Run-Time Type Information)**를 통해 일부 기능을 흉내낼 수 있습니다.

### 🔍 타입 정보 확인
```cpp
#include <typeinfo>
#include <iostream>

class MyClass {
public:
    virtual void func() {}
};

int main() {
    MyClass obj;
    std::cout << typeid(obj).name() << std::endl;
}
```

### ❌ 메서드 호출 및 객체 생성
- C++에서는 invoke, CreateInstance 같은 기능은 **직접 구현하거나 외부 라이브러리(예: Boost.Hana, Qt MetaObject)**를 사용해야 합니다
- C++20의 [[attributes]]는 컴파일러 힌트용이며 런타임 리플렉션에는 사용 불가

## ✨ 요약 비교
| 언어   | 메서드 호출 | 객체 생성 | 런타임 타입 조회 | 표준 리플렉션 지원 |
|--------|--------------|------------|------------------|---------------------|
| C#     | ✅ `Invoke`   | ✅ `Activator` | ✅ `GetType()`     | ✅                   |
| Java   | ✅ `Method.invoke` | ✅ `Constructor.newInstance` | ✅ `getClass()` | ✅                   |
| Python | ✅ `getattr()` | ✅ `type()` or `globals()` | ✅ `type()`        | ✅                   |
| C++    | ❌ 직접 구현 필요 | ❌ 직접 구현 필요 | ✅ `typeid`         | ❌ (RTTI만 지원)     |

---


# ✅ C# 테스트 자동화 샘플
## 🎯 목표
- 테스트 대상 클래스의 메서드를 자동으로 실행
- [TestCase]라는 커스텀 Attribute를 붙여 테스트 케이스 정의
- 결과를 콘솔에 출력

## 📦 코드 예제
```csharp
using System;
using System.Reflection;

namespace TestAutomationSample
{
    [AttributeUsage(AttributeTargets.Method)]
    public class TestCaseAttribute : Attribute
    {
        public object[] Parameters { get; }
        public TestCaseAttribute(params object[] parameters)
        {
            Parameters = parameters;
        }
    }

    public class Calculator
    {
        [TestCase(2, 3)]
        [TestCase(10, 5)]
        public int Add(int a, int b) => a + b;

        [TestCase(10, 2)]
        public int Divide(int a, int b) => a / b;
    }

    public class Program
    {
        public static void Main()
        {
            var type = typeof(Calculator);
            var instance = Activator.CreateInstance(type);

            foreach (var method in type.GetMethods(BindingFlags.Public | BindingFlags.Instance))
            {
                var testCases = method.GetCustomAttributes(typeof(TestCaseAttribute), false);
                foreach (TestCaseAttribute test in testCases)
                {
                    var result = method.Invoke(instance, test.Parameters);
                    Console.WriteLine($"{method.Name}({string.Join(", ", test.Parameters)}) = {result}");
                }
            }
        }
    }
}
```


## 🧪 출력 예시
Add(2, 3) = 5  
Add(10, 5) = 15  
Divide(10, 2) = 5



## ☕ Java 버전
Java에서는 @interface로 커스텀 어노테이션을 만들고 리플렉션으로 테스트를 실행할 수 있습니다.
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface TestCase {
    int a();
    int b();
}

class Calculator {
    @TestCase(a = 2, b = 3)
    public int add(int a, int b) {
        return a + b;
    }
}

public class TestRunner {
    public static void main(String[] args) throws Exception {
        Calculator calc = new Calculator();
        for (Method method : Calculator.class.getDeclaredMethods()) {
            if (method.isAnnotationPresent(TestCase.class)) {
                TestCase tc = method.getAnnotation(TestCase.class);
                Object result = method.invoke(calc, tc.a(), tc.b());
                System.out.println(method.getName() + "(" + tc.a() + ", " + tc.b() + ") = " + result);
            }
        }
    }
}

```

## 🐍 Python 버전
Python은 데코레이터와 메타데이터를 활용해 매우 간단하게 구현할 수 있습니다.
```python
test_registry = []

def test_case(*args):
    def decorator(func):
        test_registry.append((func, args))
        return func
    return decorator

class Calculator:
    @test_case(2, 3)
    @test_case(10, 5)
    def add(self, a, b):
        return a + b

calc = Calculator()
for func, args in test_registry:
    result = func(calc, *args)
    print(f"{func.__name__}{args} = {result}")

```


## ✨ 테스트 자동화 요약 비교

| 언어   | 테스트 정의 방식       | 메서드 호출 방식         | 객체 생성 및 실행 방식         |
|--------|------------------------|---------------------------|-------------------------------|
| C#     | `[TestCase]`           | `MethodInfo.Invoke`       | `Activator + Attribute`       |
| Java   | `@TestCase`            | `Method.invoke()`         | `Annotation + Reflection`     |
| Python | `@test_case(...)`      | `getattr()`               | `데코레이터 + 리스트`         |

---
