# 🧠 추상 메서드란?
```csharp
public abstract class A
{
    public abstract void Print(); // 구현 없음 → 하위 클래스에서 반드시 구현
}
```

- 선언만 존재하고, 본문은 없음
- 하위 클래스에서 반드시 override 해야 함
- 추상 메서드를 포함한 클래스는 반드시 abstract로 선언
- 해당 클래스는 인스턴스화 불가 (new 불가)


## 전체 소스
```csharp
namespace Sample4
{
    public abstract class A
    {
        int number = 3;
        public abstract void Print();
        public virtual void PrintNumber()
        {
            Console.WriteLine(number);
        }
    }

    public class B : A
    {
        public override void Print()
        {
            Console.WriteLine("B Print");
        }
    }
 
    class Program
    {
        static void Main(string[] args)
        {
            B Test = new B();
            
            Test.Print(); 
            //B Print
            
            Test.PrintNumber(); 
            //3
            
            A Test3 = Test; 
            Test3.Print(); 
            //B Print
        }
    }
}
```

## ☕ Java에서도 동일한 구조
```java
abstract class A {
    abstract void print(); // 추상 메서드
}

class B extends A {
    @Override
    void print() {
        System.out.println("B Print");
    }
}

```
- abstract 키워드로 선언
- 하위 클래스에서 반드시 @Override로 구현
- new A()는 불가능 → 추상 클래스는 인스턴스화 불가

## 📌 C# 코드와 Java 비교
| 항목                     | C# 예시                          | Java 예시                          |
|--------------------------|----------------------------------|------------------------------------|
| 추상 클래스 선언         | `public abstract class A`        | `abstract class A`                 |
| 추상 메서드 선언         | `public abstract void Print();`  | `abstract void print();`           |
| 하위 클래스에서 구현     | `public override void Print()`   | `@Override void print()`           |
| 인스턴스 생성 여부       | `new A()` ❌                     | `new A()` ❌                       |
| 다형성 활용              | `A Test3 = Test;`                | `A test3 = new B();`               |


## ✨ 실무에서 추상 메서드의 역할
- 강제 구현: 하위 클래스가 반드시 특정 기능을 구현하도록 유도
- 다형성 기반 설계: 상위 타입으로 하위 객체를 다룰 수 있음
- 템플릿 메서드 패턴: 상위에서 흐름을 제어하고, 핵심 로직은 하위에서 구현
- 공통 기능 + 확장 가능성을 동시에 제공
