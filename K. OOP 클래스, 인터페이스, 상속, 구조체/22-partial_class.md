

# 🧩 Partial Class란?
Partial class는 하나의 클래스를 여러 파일에 나눠서 정의할 수 있게 해주는 C#의 기능입니다.  
컴파일 시점에 이 모든 정의가 하나로 합쳐져서 하나의 클래스로 동작합니다.

## ✨ 주요 특징
- partial 키워드를 사용하여 클래스, 구조체, 인터페이스, 메서드를 분할 가능
- 컴파일러가 자동으로 병합하므로 개발자는 분할된 파일을 자유롭게 관리 가능
- Visual Studio의 디자이너 코드와 사용자 코드가 분리되어 GUI 코드 관리에 유리
- 협업 시 병합 충돌 방지 및 유지보수성 향상

## 🧠 왜 사용할까?
| 목적               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| GUI 코드 분리       | 디자이너가 생성한 코드와 사용자 정의 코드를 분리하여 유지보수성을 높임         |
| 협업 효율성         | 여러 개발자가 하나의 클래스를 동시에 작업할 수 있어 병합 충돌을 줄일 수 있음     |
| 가독성 향상         | 클래스가 너무 클 경우 기능별로 파일을 나눠 관리하면 코드 구조가 명확해짐         |
| 부분 메서드 지원    | `partial void` 메서드는 구현이 없으면 컴파일러가 제거하여 성능 최적화 가능       |
| 자동 생성 코드 통합 | 툴이나 프레임워크가 생성한 코드를 사용자 코드와 자연스럽게 통합할 수 있음       |



## 🔍 예제 코드 해설
```csharp
public partial class ParticleClass : BaseClass
{
    public void ExampleMethod() { ... }
    internal partial void PartialMethod(); // 선언만
}

public partial class ParticleClass
{
    public void ExampleMethod2() { ... }
    internal partial void PartialMethod() { ... } // 구현
}
```

- ParticleClass는 두 파일에 나뉘어 정의됨
- PartialMethod()는 partial void로 선언되었고, 구현이 존재하므로 호출됨
- 만약 구현이 없었다면, 컴파일러가 해당 호출 자체를 제거함 → 성능 최적화

## 전체 코드
```csharp

namespace TestCode
{
    public interface BaseClass
    {
        
    }
    
    public partial class ParticleClass : BaseClass
    {
        
        public void ExampleMethod()
        {
            Console.WriteLine("ExampleMethod");
        }
        
        internal partial void PartialMethod();
    }

    public partial class ParticleClass
    {
        public void ExampleMethod2()
        {
            Console.WriteLine("ExampleMethod2");
        }
        internal partial void PartialMethod()
        {
            Console.WriteLine("Partial method called.");
        }
    }
    
    class Program
    {
        public static void Main(String[] args)
        {
            ParticleClass particleClass = new ParticleClass();
            particleClass.ExampleMethod();
            particleClass.ExampleMethod2();
            particleClass.PartialMethod();
            /*
             ExampleMethod
             ExampleMethod2
             Partial method called.
             */
        }
    }
}

```

## 🖥️ GUI와의 관계
WinForms나 WPF 프로젝트에서 자주 보게 되는 구조:
```csharp
// Form1.Designer.cs
partial class Form1
{
    private void InitializeComponent() { ... }
}

// Form1.cs
partial class Form1 : Form
{
    public Form1() { InitializeComponent(); }
}
```

이렇게 하면 디자이너가 자동 생성한 코드를 건드리지 않고도 사용자 정의 로직을 추가할 수 있어 안정성과 유지보수성이 크게 향상됩니다.
