# C# 코드 핵심 요약

```csharp
public class Child : SomeBaseClass
{
    public Child() : base() { } // 부모 생성자 호출

    public override void SomeMessage()
    {
        base.SomeMessage();     // 부모 메서드 호출
        Console.WriteLine("Child Basic Message");
    }
}
```

- base() → 부모 클래스 생성자 호출
- base.SomeMessage() → 부모 클래스의 메서드 호출
- override → 부모의 virtual 메서드를 재정의

## 전체 코드
```csharp
namespace JSONTest
{
    class Program
    {
        public class SomeBaseClass
        {
            public SomeBaseClass()
            {
                Console.WriteLine("Some Basic Creator");
            }

            public virtual void SomeMessage()
            {
                Console.WriteLine("SomeMessage");
            }
        }

        public class Child : SomeBaseClass
        {
            public Child() :
                base()
            {
                
            }

            public override void SomeMessage()
            {
                base.SomeMessage();
                Console.WriteLine("Child Basic Message");
            }
        }
        public static void Main(String[] args)
        {

            Program.Child child = new Child();
            child.SomeMessage();
        }
    }
}
```

## 🔁 C#, Java, C++ 비교 요약표 (Markdown)
### 🔍 상속 시 부모 호출 방식 비교

| 언어   | 부모 생성자 호출       | 부모 메서드 호출         | 키워드       | 특징 및 주의사항                          |
|--------|-------------------------|----------------------------|--------------|-------------------------------------------|
| C#     | `base()`                | `base.Method()`            | `base`       | 생성자와 메서드 모두 `base`로 접근         |
| Java   | `super()`               | `super.method()`           | `super`      | 생성자 첫 줄에서만 `super()` 호출 가능     |
| C++    | `Parent()`              | `Parent::method()`         | 클래스 이름  | 생성자에서 `Parent()` 직접 호출, 메서드는 `::` 사용 |



## ☕ Java 예시
```java
class SomeBaseClass {
    public SomeBaseClass() {
        System.out.println("Some Basic Creator");
    }

    public void someMessage() {
        System.out.println("SomeMessage");
    }
}

class Child extends SomeBaseClass {
    public Child() {
        super(); // 부모 생성자 호출 (첫 줄에서만 가능)
    }

    @Override
    public void someMessage() {
        super.someMessage(); // 부모 메서드 호출
        System.out.println("Child Basic Message");
    }
}
```


## 💻 C++ 예시
```cpp
class SomeBaseClass {
public:
    SomeBaseClass() {
        std::cout << "Some Basic Creator" << std::endl;
    }

    virtual void SomeMessage() {
        std::cout << "SomeMessage" << std::endl;
    }
};

class Child : public SomeBaseClass {
public:
    Child() : SomeBaseClass() {} // 부모 생성자 호출

    void SomeMessage() override {
        SomeBaseClass::SomeMessage(); // 부모 메서드 호출
        std::cout << "Child Basic Message" << std::endl;
    }
};
```


## ✨ 요약
- C#은 base 키워드 하나로 생성자와 메서드 모두 접근
- Java는 super()는 생성자 첫 줄에서만, super.method()는 자유롭게 사용
- C++은 부모 클래스 이름으로 직접 호출 (Parent() 또는 Parent::method())

---


