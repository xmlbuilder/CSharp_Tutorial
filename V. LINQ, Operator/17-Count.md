# Count
이 기능은 C++, Java, Python에서도 각각의 방식으로 구현되며, 모두 조건 기반 필터링 + 개수 계산이라는 공통된 목적을 갖고 있어요.

## ✅ C#의 Count() 기능
- 기능: 조건에 맞는 요소의 개수를 반환
- 형식:
- collection.Count(x => 조건)
- collection.Where(x => 조건).Count()
- 예시:
```csharp
var count = numbers.Count(n => n > 0); // 0보다 큰 수의 개수
Enumerable.Range(1, 1000).Count(n => n % 13 == 0); // 13의 배수 개수
```


## 🧊 C++에서의 Count 기능
### 🔹 방법 1: std::count_if
```cpp
#include <vector>
#include <algorithm>

std::vector<int> numbers = {9, 7, 5, 4, 2, 5, 4, 0, 4, 1, 0, 4};
int count = std::count_if(numbers.begin(), numbers.end(), [](int x) {
    return x > 0;
}); // 결과: 10
```

### 🔹 방법 2: ranges::views::filter + ranges::distance (C++20)
```cpp
#include <ranges>

auto filtered = numbers | std::views::filter([](int x) { return x % 13 == 0; });
int count = std::ranges::distance(filtered); // 13의 배수 개수
```


## ☕ Java에서의 Count 기능
### 🔹 방법: stream().filter().count()
```java
List<Integer> numbers = Arrays.asList(9, 7, 5, 4, 2, 5, 4, 0, 4, 1, 0, 4);
long count = numbers.stream()
                    .filter(n -> n > 0)
                    .count(); // 결과: 10
```

### 🔹 범위 기반 예시
```java
long count = IntStream.rangeClosed(1, 1000)
                      .filter(n -> n % 13 == 0)
                      .count(); // 결과: 76
```


## 🐍 Python에서의 Count 기능
### 🔹 방법 1: 리스트 컴프리헨션 + len()
```python
numbers = [9, 7, 5, 4, 2, 5, 4, 0, 4, 1, 0, 4]
count = len([x for x in numbers if x > 0])  # 결과: 10
```

### 🔹 방법 2: sum() + 조건식
```python
count = sum(1 for x in numbers if x > 0)  # 결과: 10
```

### 🔹 범위 기반 예시
```python
count = sum(1 for x in range(1, 1001) if x % 13 == 0)  # 결과: 76
```



## 📊 요약 비교 (Markdown 표)
| 언어   | 조건 기반 개수 계산 방식                    | 특징 및 메모                          |
|--------|---------------------------------------------|---------------------------------------|
| C#     | `Count(x => 조건)` 또는 `Where(...).Count()`| LINQ 기반, 매우 직관적                |
| C++    | `std::count_if(begin, end, 조건)`           | 고성능 STL 함수, C++20에선 ranges도 가능 |
| Java   | `stream().filter(...).count()`              | 함수형 스타일, long 반환              |
| Python | `len([...])` 또는 `sum(1 for x in ...)`     | 간결하고 직관적, 리스트 컴프리헨션 활용 |


이런 Count 기능은 실무에서 로그 분석, 조건 통계, 필터링된 결과 수 확인, 유효성 검증 등에 자주 활용돼요.
---

