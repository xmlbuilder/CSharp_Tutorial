# 🧾 C# `virtual` & `override` 개념 요약

| 키워드       | 설명                                                                 |
|--------------|----------------------------------------------------------------------|
| `virtual`    | 부모 클래스에서 메서드를 오버라이딩 가능하게 선언함                   |
| `override`   | 자식 클래스에서 부모의 `virtual` 메서드를 재정의함                    |
| 동작 방식     | 런타임 시 실제 객체 타입에 따라 메서드가 호출됨 (다형성, 동적 바인딩)   |
| 기본 조건     | 부모 메서드가 `virtual`이어야 자식에서 `override` 가능                 |
| 숨기기 방식   | `override` 대신 `new` 키워드를 쓰면 부모 메서드를 숨길 수 있음          |
| 사용 예시     | `public virtual void Print()` → `public override void Print()`         |
| 주의 사항     | `override` 없이 동일 시그니처 메서드 정의 시 경고 발생 (`new` 권장)     |

## ✔️ 실행 흐름 예시
A obj = new B();
obj.Print(); // "B Print" 출력됨 → 동적 바인딩


## 소스 코드
```csharp

namespace Sample
{
    public class A
    {
        public virtual void Print()
        {
            Console.WriteLine("A Print");
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
            Test.Print(); //B Print
        }
 
    }
}
```


## 🔁 C#, C++, Java 비교 요약표 (Markdown)

| 언어   | 부모 메서드 선언 방식         | 자식 메서드 오버라이딩 방식     | 동적 바인딩 지원 | 기타 특징                          |
|--------|-------------------------------|----------------------------------|------------------|-------------------------------------|
| C#     | `public virtual void Print()` | `public override void Print()`   | O                | `new` 키워드로 숨기기 가능           |
| C++    | `virtual void Print()`        | `void Print() override`          | O                | `virtual` + `override` 조합 권장     |
| Java   | `public void print()`         | `@Override public void print()`  | O                | 모든 메서드는 기본적으로 virtual-like |


## ☕ Java와의 차이점
- Java는 virtual 키워드가 없습니다. 모든 인스턴스 메서드는 기본적으로 오버라이딩 가능.
- final 키워드를 붙이면 오버라이딩을 금지할 수 있습니다.
- @Override는 컴파일러에게 오버라이딩임을 명시하는 어노테이션일 뿐, 기능적 키워드는 아닙니다.

## 💡 C++과의 유사점
- C++도 virtual 키워드로 다형성을 구현하고, override 키워드로 오버라이딩을 명시합니다.
- C++에서는 virtual을 생략해도 오버라이딩은 되지만, override를 붙이면 실수 방지에 도움됩니다.
- C++에서는 virtual 소멸자도 중요합니다 (virtual ~Class()).
