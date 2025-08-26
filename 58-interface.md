
# 📘언어별 인터페이스 개념 비교

인터페이스는 객체지향 프로그래밍에서 **계약(Contract)**을 정의하는 수단입니다.  인터페이스를 구현하는 클래스는 해당 계약에 명시된 멤버들을 반드시 구현해야 하며, 이를 통해 유지보수성과 확장성을 높일 수 있습니다.
하지만 언어마다 인터페이스를 정의하고 구현하는 방식은 다릅니다. 이 문서에서는 C#, Java, C++, Python의 인터페이스 개념과 예제를 비교합니다.

## C# 예제
```csharp
public interface ICanDoThis
{ 
    public void TheThingICanDo();
    public int SomeValueProperty{get;set;}
}
public class MyClass : ICanDoThis
{
    public void TheThingICanDo()
    {
    
    }
    public int SomeValueProperty{get;set;}
}
```

## 💡 cpp 예제
```cpp
//C++에는 interface 가 없습니다.
//interface 역할을 하기 위해서는 순수 가상 함수를 선언 합니다.

class ICanDoThis
{
public:
    virtual void TheThingICanDo() = 0;
    virtual void TheThingICanDo2() = 0;
};

class MyClass : public ICanDoThis
{
public:
    void TheThingICanDo() override
    {
    }
    void TheThingICanDo2() override
    {
    }
};
```

## 💡 Java 예제
```java
public interface ICanDoThis {
    void theThingICanDo();
    int SOME_CONSTANT = 42; // 속성은 상수만 가능
}

public class MyClass implements ICanDoThis {
    public void theThingICanDo() {
        System.out.println("Doing the thing!");
    }
}
```


| 언어       | 인터페이스 키워드       | 기본 구현 지원       | 속성 포함 여부       | 다중 구현 가능       | 특징 요약 |
|------------|--------------------------|----------------------|----------------------|----------------------|-----------|
| **C#**     | `interface`, `:`         | ✅ (C# 8.0 이상)      | ✅ (`get; set;`)      | ✅                   | 속성, 이벤트 포함 가능. 명시적 인터페이스 구현도 지원. |
| **Java**   | `interface`, `implements`| ✅ (Java 8 이상)      | ❌ (상수만 가능)      | ✅                   | default 메서드로 기본 구현 가능. 속성은 `final static` 상수만 허용. |
| **C++**    | 없음 (`class` + `= 0`)   | ❌                   | ❌                   | ✅                   | 추상 클래스로 인터페이스 역할 수행. 속성 포함 불가. |
| **Python** | `abc.ABC`, `@abstractmethod` | ⚠️ 제한적 지원   | ✅ (`@property`)      | ✅                   | duck typing 기반. `abc` 모듈로 명시적 인터페이스 구현 가능. |


## 🧪 Python에서 인터페이스처럼 구현하는 예시
```python
from abc import ABC, abstractmethod

class ICanDoThis(ABC):
    @abstractmethod
    def the_thing_i_can_do(self):
        pass

    @property
    @abstractmethod
    def some_value_property(self):
        pass

    @some_value_property.setter
    @abstractmethod
    def some_value_property(self, value):
        pass

class MyClass(ICanDoThis):
    def __init__(self):
        self._value = 0

    def the_thing_i_can_do(self):
        print("Doing the thing!")

    @property
    def some_value_property(self):
        return self._value

    @some_value_property.setter
    def some_value_property(self, value):
        self._value = value
```

- Python은 abc 모듈을 통해 인터페이스처럼 동작하게 만들 수 있지만, 강제성이 약하고 문법이 복잡해서 헷갈리기 쉬워요.
- 특히 속성(@property)까지 인터페이스로 정의하려면 데코레이터를 여러 개 써야 해서 가독성이 떨어지죠.

## ✨ 정리

- **C#**은 실무 친화적인 인터페이스 설계를 제공하며, 속성과 기본 구현까지 지원합니다.
- Java는 인터페이스 중심 설계를 강제하며, default 메서드로 유연성을 확보했습니다.
- **C++**은 인터페이스가 없지만 추상 클래스로 유사한 구조를 만들 수 있고, 다중 상속이 강력합니다.
- Python은 자유로운 구조 덕분에 인터페이스 개념이 느슨하지만, abc 모듈로 명시적 설계도 가능합니다.

---

