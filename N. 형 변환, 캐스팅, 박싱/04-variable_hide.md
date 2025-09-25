# 🧠 변수는 상속되지 않는다?
C#에서 **필드(변수)는 상속되지만, 이름이 같으면 숨겨진다(hiding)**는 개념이 더 정확합니다. 
즉:
- 자식 클래스가 부모 클래스와 같은 이름의 필드를 선언하면,
- 부모 클래스의 필드는 숨겨지고, 자식 클래스의 필드가 우선 사용됩니다.
- 하지만 base 키워드를 사용하면 부모 클래스의 필드에 접근할 수 있습니다.

## 🔍 예제 분석
```csharp
public class A
{
    protected string name = "Hello";
}

public class B : A
{
    string name = "World";
}
```



- B 클래스는 A로부터 name 필드를 상속받았지만,
- B에서 동일한 이름의 name 필드를 새로 정의했기 때문에,
- B 내부에서는 "World"가 사용되고,
- base.name을 통해서만 "Hello"에 접근할 수 있습니다.

## 📌 핵심 개념 요약 (Markdown 표)
| 개념             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 변수 상속         | 부모 클래스의 필드는 자식 클래스에 상속됨 (접근 제한자에 따라 다름)         |
| 변수 숨김 (Hiding) | 자식 클래스에서 같은 이름의 필드를 선언하면 부모의 필드는 숨겨짐             |
| base 키워드       | 숨겨진 부모 클래스의 필드나 메서드에 접근할 때 사용                         |
| 변수 재정의 불가   | 메서드처럼 `override`로 변수(필드)를 재정의할 수는 없음                    |


## 전체 소스
```csharp
namespace Sample4
{
    public class A
    {
        int number;
        
        protected string name = "Hello";
        public A(int num)
        {
            number = num;
        }
        public void PrintA()
        {
            Console.WriteLine(number);
            Console.WriteLine(name); // 선언된 클래스를 따라 간다.
        }
    }

    
// 변수는 상속 안됨
// 각각 사용이 가능함.

    public class B : A
    {
        string name = "World";
        public B(int num) : base(num)
        {
        }

        public void PrintB()
        {
            Console.WriteLine("{0} {1}", base.name, name); 
        }
    }
 
    class Program
    {
        static void Main(string[] args)
        {
            B Test = new B(1);
            Test.PrintA();
            //1
            //Hello
            
            Test.PrintB(); //Hello World
        }
    }
}

```


## 🧪 출력 결과 해석
```
Test.PrintA(); // 1, Hello
Test.PrintB(); // Hello World
```

- PrintA()는 부모 클래스 A의 name 필드를 사용 → "Hello"
- PrintB()는 base.name과 자식 클래스 name을 각각 출력 → "Hello World"


