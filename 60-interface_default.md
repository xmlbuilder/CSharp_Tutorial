# 🧠 핵심 개념: C# 인터페이스의 Default 구현

## 🔹 기존 인터페이스의 특징
- 순수 계약: 메서드 시그니처만 정의
- 구현은 반드시 클래스에서 제공
## 🔹 C# 8.0 이후 변화
- 인터페이스에 기본 구현(default implementation) 가능
- 클래스에서 구현하지 않아도 인터페이스 타입으로 호출 가능

```cpp
interface IA
{
    void M() { Console.WriteLine("IA.M"); }
}

class C : IA { }

IA i = new C();
i.M(); // IA.M
```


##  구현 코드

```csharp
//인터페이스의 구체적 메소드

interface IA
{
    void M() { Console.WriteLine("IA.M"); }
}

class C : IA
{
    
}

class Program
{
    static int Main(string[] args)
    {
        IA i = new C();
        i.M(); 
        // prints "IA.M"
        
        //new C().M();
        // error: class 'C' does not contain a member 'M'

        return 0;
    }
}

//인터페이스 재 정의
//이해를 못 하겠다.

interface IA
{
    public void M() { Console.WriteLine("IA.M"); }
}

interface IB : IA
{
    void IA.M() { Console.WriteLine("IB.M"); } // explicitly named
}

interface IC : IA
{
    void M() { Console.WriteLine("IC.M"); } // implicitly named
}

internal class A : IA
{
    public void M() { Console.WriteLine("A.M"); }
}

internal class B : IB
{
    public void M() { Console.WriteLine("B.M"); } // explicitly named
}

internal class C : IC
{
    public void M() { Console.WriteLine("C.M"); } // implicitly named
}

class Program
{
    static int Main(string[] args)
    {
        var a = new A();
        var b = new B();
        var c = new C();

        a.M();
        //A.M

        b.M();
        //B.M

        c.M();
        //C.M

        
        ((IA) a).M();
        //A.M

        ((IB) b).M();
        //B.M

        ((IC) c).M();
        //C.M
        
        return 0;
    }
}

//재 추상화
interface IA
{
    void M() { WriteLine("IA.M"); }
}


interface IB : IA
{
    abstract void IA.M();
}

class C : IB { } // error: class 'C' does not implement 'IA.M'.

```


- C 클래스에는 M()이 없지만, IA의 기본 구현이 호출됨
- new C().M();은 컴파일 에러 → 클래스에는 M()이 없기 때문

## 🔍 인터페이스 재정의와 충돌 구조
### 🔸 명시적 재정의 (void IA.M())
- 인터페이스 상속 시, 부모 인터페이스의 메서드를 명시적으로 재정의 가능
- 호출 경로는 여전히 인터페이스 타입을 통해야 함
### 🔸 암시적 재정의 (void M())
- 자식 인터페이스에서 같은 이름의 메서드를 암시적으로 정의하면, 기존 구현을 덮어쓰기처럼 보이지만 실제로는 별개의 구현
### 🔸 재추상화 (abstract void IA.M();)
- 부모 인터페이스에 기본 구현이 있어도, 자식 인터페이스에서 다시 추상화 가능
- 이 경우, 클래스는 반드시 해당 메서드를 구현해야 함

## ⚠️ 실무에서의 혼란 포인트
| 상황                                                   | 결과                          | 설명                                                                 |
|--------------------------------------------------------|-------------------------------|----------------------------------------------------------------------|
| 클래스가 인터페이스 구현만 하고 메서드 구현 안 함      | 컴파일 OK                     | 인터페이스에 default 구현이 있으면 클래스에서 구현하지 않아도 됨     |
| 클래스에서 직접 호출 (`new C().M()`)                   | 컴파일 에러                   | 클래스에 해당 메서드가 없기 때문. 인터페이스 타입으로만 호출 가능     |
| 인터페이스를 상속한 인터페이스에서 `void IA.M()` 재정의 | 인터페이스 타입으로 호출 필요 | 명시적 구현이므로 `((IA)obj).M()`처럼 호출해야 함                     |
| 인터페이스에서 default 구현 후 자식 인터페이스에서 `abstract`로 재추상화 | 클래스에서 반드시 구현 필요   | 부모의 default 구현은 무시되고, 자식 인터페이스에서 추상화됨         |
| 클래스에서 인터페이스 메서드를 override 없이 구현       | 인터페이스 타입으로 호출 시 default 구현 사용 | 클래스에 구현이 없으면 인터페이스의 기본 구현이 호출됨 |
| 동일한 메서드 이름을 가진 여러 인터페이스를 구현        | 충돌 가능성 있음              | 명시적 구현 또는 인터페이스 타입 캐스팅으로 해결해야 함              |



