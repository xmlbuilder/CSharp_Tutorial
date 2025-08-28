## ✅ C#의 Distinct() 기능
- 기능: 컬렉션에서 중복된 요소를 제거하고 고유한 값만 반환
- 형식: collection.Distinct()
- 커스터마이징: Distinct(IEqualityComparer<T>)로 사용자 정의 비교 가능
- 예시:

```csharp
var numbers = new List<int>() {19, 17, 15, 24, 12, 25, 14, 20, 12, 28, 19, 30, 4};
var results = numbers.Distinct().ToArray(); // 중복 제거
```

## 🧊 C++에서의 중복 제거
### 🔹 방법 1: std::set 사용
```cpp
#include <vector>
#include <set>

std::vector<int> numbers = {19, 17, 15, 24, 12, 25, 14, 20, 12, 28, 19, 30, 4};
std::set<int> unique(numbers.begin(), numbers.end()); // 자동 중복 제거
```

###🔹 방법 2: std::sort + std::unique
```cpp
std::sort(numbers.begin(), numbers.end());
auto last = std::unique(numbers.begin(), numbers.end());
numbers.erase(last, numbers.end());
```

- std::unique는 연속된 중복만 제거하므로 정렬이 필요함

## ☕ Java에서의 중복 제거
### 🔹 방법 1: Set 사용
```java
List<Integer> numbers = Arrays.asList(19, 17, 15, 24, 12, 25, 14, 20, 12, 28, 19, 30, 4);
Set<Integer> unique = new LinkedHashSet<>(numbers); // 순서 유지하며 중복 제거
```

### 🔹 방법 2: stream().distinct()
```java
List<Integer> distinct = numbers.stream()
                                .distinct()
                                .collect(Collectors.toList());
```


## 🐍 Python에서의 중복 제거
### 🔹 방법 1: set() 사용
```python
numbers = [19, 17, 15, 24, 12, 25, 14, 20, 12, 28, 19, 30, 4]
unique = list(set(numbers))  # 중복 제거, 순서는 보장되지 않음
```

### 🔹 방법 2: dict.fromkeys()로 순서 유지
```python
unique = list(dict.fromkeys(numbers))  # 중복 제거 + 순서 유지
```


## 📊 요약 비교 (Markdown 표)
| 언어   | 중복 제거 방식                        | 순서 유지 | 특징 및 메모                          |
|--------|---------------------------------------|------------|---------------------------------------|
| C#     | `Distinct()`                         | ✅         | LINQ 기반, 커스텀 비교자 사용 가능     |
| C++    | `std::set` / `std::unique`           | ❌ / ✅     | `set`은 자동 정렬, `unique`는 정렬 필요 |
| Java   | `stream().distinct()` / `Set`        | ✅         | `LinkedHashSet`으로 순서 유지 가능     |
| Python | `set()` / `dict.fromkeys()`          | ❌ / ✅     | 매우 간결, 순서 유지는 `dict` 방식 사용 |



이런 중복 제거 기능은 실무에서 로그 정리, 사용자 목록 정제, 태그 필터링, 중복 레코드 제거 등에 자주 활용돼요.

---
