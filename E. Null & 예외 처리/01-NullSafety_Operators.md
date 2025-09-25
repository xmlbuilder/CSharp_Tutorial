# `??`, `?.`, `?[]` 연산자 (C#)와 Java / C++ 대응

## 1. C# `??` (null 병합 연산자)

-   **설명**\
    왼쪽 피연산자가 `null`이 아니면 그대로 반환, `null`이면 오른쪽 값을
    반환.\
    오른쪽은 필요할 때만 평가되므로 **단락 평가**를 지원.

``` csharp
string testString = null;
Console.WriteLine(testString ?? "not provided"); 
// "not provided"

int? a = null;
int? b = null;
Console.WriteLine(a ?? b ?? 0); 
// 0
```

------------------------------------------------------------------------

## 2. C# `?.` / `?[]` (null 조건부 연산자)

-   **설명**\
    객체가 `null`이면 `null`을 반환하고, 아니면 멤버/요소 접근 실행.\
    체인 평가 도중 하나라도 `null`이면 전체가 `null`로 반환.\
    이벤트 호출에도 사용 가능 (`PropertyChanged?.Invoke(...)`).

``` csharp
A?.B?.Do(C); 
// A==null → 전체 null
// A!=null, B==null → 전체 null
// A,B 둘 다 not null → Do(C) 실행
```

------------------------------------------------------------------------

## 3. Java에서의 대응

-   **`??` 대응**: Java에는 직접적인 null 병합 연산자는 없음. 대신
    **삼항 연산자(`?:`)**를 사용.

``` java
String testString = null;
System.out.println(testString != null ? testString : "not provided");
// "not provided"
```

-   **`?.` 대응**: Java 8부터 `Optional` 클래스로 비슷한 효과.

``` java
String value = Optional.ofNullable(testString).orElse("not provided");
```

또는 직접 `if`문 / `Objects.nonNull()` 등을 사용.

-   **`?[]` 대응**: Java에는 직접 문법은 없으며, 배열/리스트 접근 전에
    null 체크 필요.

``` java
String[] arr = null;
String x = (arr != null) ? arr[0] : null;
```

------------------------------------------------------------------------

## 4. C++에서의 대응

-   **`??` 대응**: 직접 연산자는 없음. 보통 삼항 연산자 또는 헬퍼 함수로
    구현.

``` cpp
std::string testString;
std::cout << (!testString.empty() ? testString : "not provided") << std::endl;
```

C++17 이상에서는 `std::optional`을 사용해 비슷한 동작 가능:

``` cpp
#include <optional>
#include <iostream>

std::optional<std::string> getMessage(bool ok) {
    if (ok) return "Hello";
    return std::nullopt;
}

int main() {
    auto msg = getMessage(false).value_or("Default");
    std::cout << msg << std::endl; // "Default"
}
```

-   **`?.` 대응**: C++에는 연산자 자체는 없음. 대신
    `if (ptr) ptr->member;` 형태 사용.\
    C++23에는 `expected` 타입, 서드파티 라이브러리(Boost.Optional,
    Outcome 등)로 체인 가능.

-   **`?[]` 대응**: 직접 지원 없음. 포인터나 컨테이너가
    null인지/empty인지 검사 후 접근해야 함.

``` cpp
std::vector<int>* arr = nullptr;
if (arr && !arr->empty()) {
    std::cout << (*arr)[0] << std::endl;
}
```

------------------------------------------------------------------------

## 5. 비교 정리

| 개념   | C#                        | Java                                        | C++                                |
|--------|---------------------------|---------------------------------------------|------------------------------------|
| Null 병합 | `a ?? b`                 | 삼항 연산자 `a != null ? a : b` <br> `Optional.ofNullable(a).orElse(b)` | `a.has_value() ? a.value() : b` (`std::optional`) |
| Null 조건부 | `a?.x`, `a?[i]`, `a?.Method()` | `Optional.map(...).orElse(...)` 또는 null 체크 | `if (ptr) ptr->x;` / `optional.map()` |
| 단락 평가  | 내장 (??, ?.)           | 삼항 연산자, Optional 체인                  | `std::optional` 체인, `if` 체크    |


------------------------------------------------------------------------

## 6. 요약

-   **C#**: `??`, `?.`, `?[]`로 null-safe 코드를 간결히 표현.
-   **Java**: 삼항 연산자, `Optional`로 비슷한 동작 구현.
-   **C++**: `std::optional`, `if(ptr)` 문법으로 대응.

➡️ **C#은 언어 차원에서 null 안전 연산자를 제공**,\
반면 Java와 C++은 **라이브러리(`Optional`/`std::optional`)나
조건문**으로 대체합니다.
