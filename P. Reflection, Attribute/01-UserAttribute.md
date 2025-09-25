# Attribute
C#에서 보여주신 Attribute는 메타데이터를 클래스나 메서드 등에 부여하고 런타임에 리플렉션으로 조회할 수 있는 강력한 기능.

## C#
```csharp
namespace GrammarTest
{
    [System.AttributeUsage(System.AttributeTargets.Class | System.AttributeTargets.Method, 
        AllowMultiple = true)]
        
    //All, Assembly, Class, Constructor, Delegator, Enum, Event, Field, GenericParameter, 
    //Interface, Method, Parameter, Property, ReturnValue, Struct
    class History : System.Attribute
    {
        private string programmer;
        public double Version { get; set; }
        public string Changes { get; set; }
        public History(string programmer)
        {
            this.programmer = programmer;
            Version = 1.0;
            Changes = "First release";
        }
        public string Programmer
        {
            get { return programmer;  }
        }
    }
    
    [History("jhjeong", Version = 1.0, Changes = "2022-03-27 Created")]
    [History("jhjeong", Version = 1.1, Changes = "2022-03-28 Modified")]
    [History("jhjeong", Version = 1.2, Changes = "2022-03-29 Modified")]
    [History("jhjeong", Version = 1.3, Changes = "2022-03-30 Modified")]
    class MyClass
    {
        public void Func()
        {
            Console.WriteLine(nameof(Func));
        }
    }
    
    public class Program
    {
       public static void Main(String[] args)
        {
            Type type = typeof(MyClass);
            Attribute[] attributes = Attribute.GetCustomAttributes(type);
            Console.WriteLine(attributes.Length);
            //4
            foreach (var attribute in attributes)
            {
                var h = attribute as History;
                if (h != null)
                {
                    Console.WriteLine($"{h?.Programmer} - {h?.Version} - {h?.Changes}");
                }
                /*
                jhjeong - 1 - 2022-03-27 Created
                jhjeong - 1.1 - 2022-03-28 Modified
                jhjeong - 1.2 - 2022-03-29 Modified
                jhjeong - 1.3 - 2022-03-30 Modified
                 */
            }
            
        }
    }
}

```

## ✅ Java: Custom Annotation
Java에서는 @interface 키워드를 사용해 Annotation을 정의할 수 있습니다. 
C#의 Attribute와 거의 동일한 역할을 하며, RetentionPolicy.RUNTIME을 설정하면 런타임에 리플렉션으로 조회할 수 있습니다.
```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@interface History {
    String programmer();
    double version();
    String changes();
}

@History(programmer = "jhjeong", version = 1.0, changes = "2022-03-27 Created")
@History(programmer = "jhjeong", version = 1.1, changes = "2022-03-28 Modified")
class MyClass {
    public void func() {
        System.out.println("func");
    }
}

```

주의: Java에서는 @Repeatable을 사용해야 동일한 Annotation을 여러 번 붙일 수 있습니다.
```java
@Repeatable(Histories.class)
@interface History { ... }

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Histories {
    History[] value();
}
```

그리고 리플렉션으로 조회할 수 있습니다:
```java
History[] histories = MyClass.class.getAnnotationsByType(History.class);
for (History h : histories) {
    System.out.println(h.programmer() + " - " + h.version() + " - " + h.changes());
}
```


## 🐍 Python: Custom Decorator + Metadata
Python은 @decorator를 통해 유사한 기능을 구현할 수 있지만, 정식 Attribute 시스템은 없습니다. 
대신 클래스에 메타데이터를 동적으로 추가하거나 데코레이터로 래핑하는 방식으로 처리합니다.
```python
def history(programmer, version, changes):
    def decorator(cls):
        if not hasattr(cls, '__history__'):
            cls.__history__ = []
        cls.__history__.append({
            'programmer': programmer,
            'version': version,
            'changes': changes
        })
        return cls
    return decorator

@history("jhjeong", 1.0, "2022-03-27 Created")
@history("jhjeong", 1.1, "2022-03-28 Modified")
class MyClass:
    def func(self):
        print("func")

# 조회
for h in MyClass.__history__:
    print(f"{h['programmer']} - {h['version']} - {h['changes']}")
```


Python은 매우 유연해서 이런 방식으로도 충분히 Attribute처럼 사용할 수 있어요.

## ❌ C++: 전통적으로 지원되지 않음
C++은 메타데이터를 런타임에 조회하는 기능이 없습니다. 일부 컴파일러 확장이나 외부 라이브러리(e.g. Boost, Qt의 Q_PROPERTY)를 통해 유사한 기능을 흉내낼 수는 있지만, C#이나 Java처럼 표준적인 Attribute 시스템은 없습니다.
C++20부터는 [[attributes]] 문법이 도입되었지만, 이는 컴파일러 힌트용으로 런타임 리플렉션에는 사용할 수 없습니다.
```cpp
[[deprecated("Use newFunc instead")]]
void oldFunc() { }
```


## ✨ 요약 비교
| 언어   | Attribute 지원 | 런타임 리플렉션 | 반복 사용 가능 | 구현 방식             |
|--------|----------------|------------------|----------------|------------------------|
| C#     | ✅              | ✅                | ✅              | `System.Attribute` 상속 |
| Java   | ✅              | ✅                | ✅ (`@Repeatable`) | `@interface` + 리플렉션 |
| Python | ✅ (유사)       | ✅                | ✅              | 데코레이터 + 메타데이터 |
| C++    | ❌              | ❌                | ❌              | 일부 컴파일러 확장만 가능 |


---


