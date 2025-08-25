# ⚙️ C#의 checked / unchecked 개념

| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 키워드                   | `checked`                                                            |
| 목적                     | 정수 산술 연산 또는 변환에서 **오버플로우 검사**를 수행              |
| 동작                     | 오버플로우 발생 시 `System.OverflowException`을 **런타임에 throw**함 |
| 적용 대상                | `int`, `uint`, `long`, `short` 등 **정수형 산술 연산 및 변환**       |
| 사용 방식                | 블록(`checked { ... }`) 또는 식(`checked(expression)`)               |
| 기본 설정                | 프로젝트 설정 또는 컴파일러 옵션에 따라 기본 검사 여부 결정           |
| 상수 식에서의 동작       | 오버플로우 발생 시 **컴파일 오류** 발생                              |
| 예외 처리                | `try-catch` 블록으로 `OverflowException`을 처리 가능                  |
| 반대 키워드              | `unchecked` — 오버플로우를 무시하고 wrap-around 처리                  |
| 대표 사용 예시           | `checked { int x = a + b; }` 또는 `int y = checked(a + b);`           |


## ✅ 특징 요약

- 상수 식에서 오버플로우 발생 시 → 컴파일 오류
- 런타임 식에서는 checked 블록 내에서만 예외 발생
- 기본 설정은 컴파일러 옵션 또는 프로젝트 설정에 따라 다름 (checked가 기본일 수도, 아닐 수도 있음)

## 🧪 예시

```csharp
uint a = uint.MaxValue;

unchecked
{
    Console.WriteLine(a + 3); // 출력: 2 (오버플로우 무시)
}

try
{
    checked
    {
        Console.WriteLine(a + 3); // 예외 발생
    }
}
catch (OverflowException e)
{
    Console.WriteLine(e.Message); // 출력: Arithmetic operation resulted in an overflow.
}
```

## 🔍 C++, Java에서의 대안
### 🧱 C++

| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 기본 오버플로우 처리      | 오버플로우 발생 시 **wrap-around** 처리 (예외 발생하지 않음)         |
| 예외 발생 여부           | ❌ 기본적으로 없음. 오버플로우는 silent하게 처리됨                   |
| 안전한 연산 구현 방식     | 수동 검사 또는 외부 라이브러리 사용 필요                             |
| 수동 검사 방법           | `std::numeric_limits`를 활용한 범위 검사                             |
| 예외 처리 대안           | `throw std::overflow_error` 등으로 직접 예외 발생 가능               |
| 외부 라이브러리 활용      | `SafeInt`, `Boost.NumericConversion`, `Boost.CheckedInt` 등 사용 가능 |
| C++20 구조체 초기화       | `.field = value` 형식으로 명시적 초기화 가능 (오버플로우와 직접 관련은 없음) |
| 컴파일러 경고/옵션       | 일부 컴파일러에서 오버플로우 경고 옵션 제공 (`-ftrapv`, `-fsanitize`) |


예시: 수동 오버플로우 검사
```cpp
#include <limits>
#include <stdexcept>

int SafeAdd(int a, int b) {
    if (b > 0 && a > std::numeric_limits<int>::max() - b)
        throw std::overflow_error("Overflow detected");
    return a + b;
}
```


### ☕ Java

| 항목                     | Java에서의 처리 방식                                                  |
|--------------------------|----------------------------------------------------------------------|
| 기본 오버플로우 처리      | 오버플로우 발생 시 **wrap-around** 처리 (예외 발생하지 않음)         |
| 예외 발생 여부           | ❌ 기본적으로 없음. 오버플로우는 silent하게 처리됨                   |
| 안전한 연산 구현 방식     | `Math.addExact()`, `Math.subtractExact()` 등 사용                     |
| 수동 검사 방법           | `if` 조건문과 `Integer.MAX_VALUE`, `Integer.MIN_VALUE` 활용 가능     |
| 예외 처리 대안           | `ArithmeticException`을 `Exact` 메서드에서 자동으로 throw            |
| 외부 라이브러리 활용      | Guava, Apache Commons Lang 등에서 유틸 제공 가능                     |
| 부동소수점 오버플로우     | `Infinity`, `NaN` 등으로 표현되며 예외는 발생하지 않음               |
| 컴파일러 경고/옵션       | ❌ 없음. 오버플로우에 대한 경고는 기본적으로 제공되지 않음           |


예시: Java의 안전한 덧셈

```java
int a = Integer.MAX_VALUE;
try {
    int result = Math.addExact(a, 1); // 예외 발생
} catch (ArithmeticException e) {
    System.out.println("Overflow detected: " + e.getMessage());
}
```


## 🧠 언어별 비교 표
| 언어   | 기본 오버플로우 처리 | 예외 발생 여부 | 안전한 연산 대안                      |
|--------|----------------------|----------------|--------------------------------------|
| C#     | 선택적 (`checked`/`unchecked`) | `OverflowException` | `checked` 블록 또는 `checked` 식     |
| C++    | wrap-around 발생     | ❌ 없음         | 수동 검사 또는 라이브러리 사용       |
| Java   | wrap-around 발생     | `ArithmeticException` (일부 메서드만) | `Math.addExact()`, `Math.multiplyExact()` |

---


