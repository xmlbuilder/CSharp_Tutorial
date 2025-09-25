# 🧠 추상 클래스란?

- 공통 기능은 구현하고, 특정 기능은 하위 클래스에 강제 구현시키는 구조
- 인스턴스 생성 불가 (new 불가)
- 상속 전용: 다형성 기반 설계에 사용
- 추상 메서드는 반드시 하위 클래스에서 구현해야 함
- 일부 구현 + 일부 강제 구현이 가능 → 인터페이스보다 유연함

## 🔍 언어별 추상 클래스 비교
| 언어     | 추상 클래스 정의 방식                     | 특징                                                                 |
|----------|--------------------------------------------|----------------------------------------------------------------------|
| C#       | `abstract class`, `abstract method`        | `virtual`로 기본 구현 가능, `override` 필수                          |
| Java     | `abstract class`, `abstract method`        | 다중 상속 불가, 인터페이스와 병행 사용 많음                         |
| C++      | `virtual void f() = 0;`                    | 하나라도 `= 0`이면 추상 클래스, 전부면 인터페이스처럼 사용 가능     |
| Python   | `abc.ABC`, `@abstractmethod` decorator     | 런타임 추상 확인, 다중 상속 가능, 유연한 구조                        |


C에서 “= 0”이 하나라도 있으면 추상 클래스, 전부면 인터페이스처럼 보지만
**진짜 인터페이스 개념은 언어마다 다르고, C은 약한 편**이에요.


## ✨ 왜 필요한가? 실무에서의 필요성
| 필요성                         | 설명                                                                 |
|--------------------------------|----------------------------------------------------------------------|
| 공통 기능 재사용               | 상위 클래스에서 공통 로직 구현 → 하위 클래스에서 중복 제거           |
| 강제 구현                      | 특정 메서드는 반드시 하위에서 구현하도록 강제                        |
| 다형성                         | 상위 타입으로 하위 객체를 다룰 수 있어 유연한 설계 가능              |
| 템플릿 메서드 패턴 구현 가능   | 일부 로직은 상위에서, 핵심 로직은 하위에서 구현                      |
| 인터페이스보다 유연한 구조 제공 | 상태(필드), 구현 메서드, 접근 제어자 등 다양한 기능 포함 가능         |


## 💡 코드 요약
```csharp
public abstract class A
{
    int number = 3;
    public abstract void Print(); // 하위에서 반드시 구현
    public virtual void PrintNumber() => Console.WriteLine(number); // 선택적 override
}

public class B : A
{
    public override void Print() => Console.WriteLine("B Print");
}
```

## 전체 코드
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


- Print()는 강제 구현
- PrintNumber()는 기본 구현 제공
- A Test3 = Test; → 다형성 활용

## 🧩 추상 클래스 vs 인터페이스 요약
| 항목             | 추상 클래스                         | 인터페이스                         |
|------------------|--------------------------------------|-------------------------------------|
| 인스턴스 생성    | 불가                                 | 불가                                |
| 필드 포함        | 가능                                 | C# 8.0 이후 일부 가능               |
| 메서드 구현      | 일부 가능 (`virtual`, `abstract`)    | C# 8.0 이후 default 구현 가능       |
| 다중 상속        | ❌ 불가 (C#, Java)                   | ✅ 가능                              |
| 역할 분리        | 단일 역할 중심                       | 다중 역할 조합 가능                 |
| 목적             | 공통 기능 + 강제 구현                | 계약 정의 중심                      |
| 유연성           | 제한적 (단일 상속)                   | 높음 (다중 인터페이스 구현 가능)   |




## ⚠️ 실무에서 유발되는 문제
- 추상 클래스를 상속받은 클래스는 다른 추상 클래스와 기능을 공유하기 어려움
- 인터페이스는 여러 개를 구현할 수 있어 역할 기반 설계에 적합
- 특히 C#과 Java에서는 이 제약이 구조적 유연성 부족으로 이어질 수 있음
- 반면 C++은 다중 상속이 가능하지만, 다이아몬드 상속 문제 등으로 더 큰 복잡성을 유발
