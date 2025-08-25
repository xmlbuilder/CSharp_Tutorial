# 🔗 #define과 [Conditional]의 연결
## 📌 핵심 개념
- #define SYMBOL로 심볼을 정의하면, 해당 심볼을 기준으로 조건부 컴파일이 가능
- [Conditional("SYMBOL")]을 메서드에 붙이면, 해당 심볼이 정의되어 있을 때만 메서드 호출이 컴파일에 포함됨
- 메서드 자체는 항상 존재하지만, 호출문이 컴파일러에 의해 제거됨

## 🧪 예시 설명
```csharp
#define EXAMPLE_A

using System;
using System.Diagnostics;

public class Program
{
    public static void Main(string[] args)
    {
        ExampleA(); // 호출됨
        ExampleB(); // 호출되지 않음
    }

    [Conditional("EXAMPLE_A")]
    static void ExampleA()
    {
        Console.WriteLine("Example A");
    }

    [Conditional("EXAMPLE_B")]
    static void ExampleB()
    {
        Console.WriteLine("Example B");
    }
}
```

- EXAMPLE_A가 정의되어 있으므로 ExampleA() 호출은 컴파일됨
- EXAMPLE_B는 정의되지 않았으므로 ExampleB() 호출은 컴파일에서 제거됨
- 문법 오류는 발생하지 않음 → 호출문이 아예 사라지기 때문

## 🧬 다중 조건 사용
```csharp
[Conditional("DEBUG"), Conditional("MESH_CASE_A")]
public static void MeshCaseA()
{
    Console.WriteLine("Sample");
}
```            

- DEBUG와 MESH_CASE_A 중 하나라도 정의되어 있으면 호출문이 컴파일됨
- 둘 다 정의되지 않으면 호출문은 제거됨

## ⚠️ 주의사항
| 항목             | 설명                                                                 |
|------------------|----------------------------------------------------------------------|
| 반환값 제한       | `void` 반환형만 사용 가능. 반환값이 있는 메서드에는 적용 불가         |
| 적용 대상         | 메서드에만 적용 가능. 클래스, 필드, 속성에는 사용할 수 없음           |
| 호출 제거 방식     | 해당 심볼이 정의되지 않으면 **호출문 자체가 컴파일에서 제거됨**        |
| 메서드 정의       | 메서드 정의는 항상 포함됨. 호출 여부만 조건부로 결정됨                |
| 오버로드 제한      | 오버로드된 메서드에는 `[Conditional]`을 사용할 수 없음                |
| 다중 조건 지원     | 여러 개의 심볼을 `,`로 나열하여 **OR 조건**으로 처리 가능              |
| 런타임 영향 없음   | 조건부 호출은 **컴파일 시점**에 결정되며, 런타임에는 영향 없음         |



## ✅ 활용 예시
- Debug.WriteLine()은 내부적으로 [Conditional("DEBUG")]이 붙어 있음
- 테스트용 로깅, 성능 측정, 개발 중 임시 기능 분기 등에 활용

---

