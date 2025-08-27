# ⚠️ #pragma warning disable 정리

## 📌 기본 문법
```csharp
#pragma warning disable [경고번호]
```

- 해당 경고를 무시하고 컴파일함
- 여러 개의 경고 번호를 쉼표로 구분해 한 줄에 작성 가능
- #pragma warning restore로 다시 활성화 가능

## 🧾 예시 경고 코드
| 경고 코드 | 설명                                                         |
|-----------|--------------------------------------------------------------|
| 162       | 도달할 수 없는 코드 (`Unreachable code detected`)            |
| 164       | 항상 참 또는 거짓인 조건 (`Expression is always true/false`) |
| 219       | 사용되지 않은 지역 변수 (`Unused local variable`)            |
| 414       | 사용되지 않은 필드 (`Field is assigned but its value is never used`) |
| 618       | 사용이 더 이상 권장되지 않는 API 사용 (`Obsolete member usage`) |
| 3021      | CLS 규격을 따르지 않는 멤버 (`Member is not CLS-compliant`)  |



🧪 사용 예시
```cpp
#pragma warning disable 219

public class Example
{
    public static void Main()
    {
        int unusedVariable = 42; // 경고 219 발생 → 무시됨
        Console.WriteLine("Hello, world!");
    }
}


#pragma warning disable 162

public class Example
{
    public static void Main()
    {
        if (false)
        {
            Console.WriteLine("Unreachable code"); // 경고 162 발생 → 무시됨
        }
    }
}
```


## ✅ 복원하기
```csharp
#pragma warning disable 219
int unused = 0;
#pragma warning restore 219
```

이렇게 하면 unused 변수에 대해서만 경고를 무시하고, 이후에는 다시 경고가 활성화됨.

