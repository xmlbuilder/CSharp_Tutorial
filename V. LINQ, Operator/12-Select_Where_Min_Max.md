# 🧠 C# 핵심 기능 설명
## ✅ Select
- 기능: 컬렉션의 각 요소를 변형하여 새로운 시퀀스를 생성
- 형식: Select((element, index) => ...)
- 예시: 인덱스를 활용해 문자열을 부분적으로 추출
```csharp
fruits.Select((fruit, index) => new { index, str = fruit.Substring(0, index) });
```


## ✅ Where
- 기능: 조건을 만족하는 요소만 필터링
- 형식: Where(predicate)
- 예시: 양수만 필터링 후 최소값 계산
```csharp
numbers.Where(n => n > 0).Min(); // 2
```


## ✅ Min / Max
- 기능: 조건을 만족하는 요소 중 최소/최대값 반환
- 형식: collection.Min(), collection.Max()
- 예시: 음수 중 최대값

```csharp
numbers.Where(n => n < 0).Max(); // -1
```


## 소스

```csharp

string[] fruits = { "apple", "banana", "mango", "orange",
                      "passionfruit", "grape" };
var query =
    fruits.Select((fruit, index) =>
                      new { index, str = fruit.Substring(0, index) });

foreach (var obj in query)
{
    Console.WriteLine("{0}", obj);
}
/*
 This code produces the following output:
 {index=0, str=}
 {index=1, str=b}
 {index=2, str=ma}
 {index=3, str=ora}
 {index=4, str=pass}
 {index=5, str=grape}
*/


// Where, Min/Max
// 제너릭에 제약을 줄 수 있는 키워드.
// LINQ를 사용하여 쿼리나 컬렉션을 필터링하는 데 사용된다.
// where 절은 데이터 소스에서 특정 조건을 충족하는 요소만 선택하는 데 사용된다.
// 이는 LINQ를 사용하여 데이터를 쿼리하고 조작하는 데 매우 유용하다

var numbers = new List<int>() {9, 7, -5, 4, 2, 5, 4, 2, -4, 8, -1, 6, 4};
var min = numbers.Where(n => n > 0)
    .Min(); 
Console.WriteLine(min);
//2

var max = numbers.Where(n => n < 0)
    .Max();
Console.WriteLine(max); 
//-1

var newNumber = numbers.Select(n => Math.Abs(n)).ToList();
Console.WriteLine(String.Join(",", newNumber));
//9, 7, 5, 4, 2, 5, 4, 2, 4, 8, 1, 6, 4

```


## 🌐 다른 언어에서의 유사 기능 비교
### 🧊 C++ (C++20 기준)
- std::ranges::views를 활용하면 LINQ 스타일 가능
- transform, filter, min_element, max_element 등 사용
```cpp
#include <ranges>
auto transformed = fruits | std::views::transform([i = 0](const std::string& s) mutable {
    return s.substr(0, i++);
});
```

- std::ranges::min_element로 최소값 검색 가능

### ☕ Java (Java 8 이상)
- Stream API로 map, filter, min, max 구현 가능
```java
List<String> fruits = Arrays.asList("apple", "banana", "mango", "orange", "passionfruit", "grape");

List<String> result = IntStream.range(0, fruits.size())
    .mapToObj(i -> fruits.get(i).substring(0, i))
    .collect(Collectors.toList());

int min = numbers.stream().filter(n -> n > 0).min(Integer::compare).get();
int max = numbers.stream().filter(n -> n < 0).max(Integer::compare).get();
```


### 🐍 Python
- 리스트 컴프리헨션, map, filter, min, max 사용
- enumerate()로 인덱스 접근 가능
```python
fruits = ["apple", "banana", "mango", "orange", "passionfruit", "grape"]
result = [fruit[:i] for i, fruit in enumerate(fruits)]

numbers = [9, 7, -5, 4, 2, 5, 4, 2, -4, 8, -1, 6, 4]
min_val = min(filter(lambda x: x > 0, numbers))  # 2
max_val = max(filter(lambda x: x < 0, numbers))  # -1
abs_numbers = list(map(abs, numbers))  # [9, 7, 5, 4, 2, 5, 4, 2, 4, 8, 1, 6, 4]
```


## 📊 요약 비교 (Markdown 표)
| 언어   | 변형 (`Select`) 방식              | 필터링 (`Where`) 방식        | 집계 (`Min/Max`) 방식                | 특징             |
|--------|--------------------|-------------------------|-----------------------------------|-------------------------------|
| C#     | `Select((item, index) => ...) | `Where(predicate)`   | `Min()`, `Max()`               | LINQ 기반, 지연 실행, 직관적 문법 |
| C++    | `std::views::transform`     | `std::views::filter` | `std::ranges::min_element` 등   | 고성능, 최신 문법, C++20 이상 필요 |
| Java   | `stream().map()` + `range()` | `stream().filter()` | `min()`, `max()` with comparator | 함수형 스타일, Optional 처리 필요 |
| Python | 리스트 컴프리헨션, `map()`    | `filter()`        | `min()`, `max()`              | 간결한 문법, 유연한 처리 |     

이런 기능들은 실무에서 데이터 전처리, 통계 분석, UI 필터링, 검색 기능 등에 자주 활용됩니다.

---


