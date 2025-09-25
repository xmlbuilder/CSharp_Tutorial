## ✅ C#의 Where() 기능
- 기능: 조건에 맞는 요소만 필터링
- 형식: collection.Where(x => 조건)
- 예시:
```csharp
var list = new List<int> {1, 2, 3, 4, 5, 5, 6, 7, 8, 9};
var newList = list.Where(x => x != 5); // 5 제외
- 조인 예시:
var result = customers.Join(orders,
                            c => c.Id,
                            o => o.CustomerId,
                            (c, o) => new { c.Name, o.OrderDate });

```

## 🧊 C++에서의 필터링 및 조인
### 🔹 필터링: std::copy_if 또는 ranges::views::filter (C++20)
```cpp
#include <vector>
#include <algorithm>

std::vector<int> list = {1, 2, 3, 4, 5, 5, 6, 7, 8, 9};
std::vector<int> filtered;

std::copy_if(list.begin(), list.end(), std::back_inserter(filtered),
             [](int x) { return x != 5; });
```

- C++20부터는 ranges::views::filter로 더 간결하게 가능
## 🔹 조인: 직접 구현하거나 Boost 라이브러리 사용
- C++에는 LINQ 스타일의 조인이 없기 때문에 for 루프나 map 기반으로 수동 구현

## ☕ Java에서의 필터링 및 조인
### 🔹 필터링: stream().filter()
```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 5, 6, 7, 8, 9);
List<Integer> filtered = list.stream()
                             .filter(x -> x != 5)
                             .collect(Collectors.toList());
```

### 🔹 조인: 두 리스트를 flatMap() 또는 for 루프로 병합
```java
List<Customer> customers = ...;
List<Order> orders = ...;

List<Result> joined = customers.stream()
    .flatMap(c -> orders.stream()
        .filter(o -> o.getCustomerId() == c.getId())
        .map(o -> new Result(c.getName(), o.getOrderDate())))
    .collect(Collectors.toList());
```


## 🐍 Python에서의 필터링 및 조인
### 🔹 필터링: filter() 또는 리스트 컴프리헨션
```python
list = [1, 2, 3, 4, 5, 5, 6, 7, 8, 9]
filtered = [x for x in list if x != 5]
```

### 🔹 조인: for 루프 또는 pandas.merge() (데이터프레임 기반)
# 리스트 기반 조인
```python
joined = [(c['name'], o['date']) for c in customers for o in orders if c['id'] == o['customer_id']]
```

- pandas.merge()를 쓰면 SQL 스타일 조인도 가능

## 📊 요약 비교 (Markdown 표)
| 언어   | 필터링 방식                         | 조인 방식                              | 특징                                   |
|--------|-------------------------------------|----------------------------------------|----------------------------------------|
| C#     | `Where(x => 조건)`                 | `Join(..., ..., ..., resultSelector)` | LINQ 기반, SQL 스타일 조인 가능         |
| C++    | `std::copy_if` / `ranges::filter`  | 직접 구현 또는 Boost 사용              | 고성능, 명시적 제어 필요                |
| Java   | `stream().filter()`                | `flatMap()` + `filter()`               | 함수형 스타일, 객체 기반 조인 가능      |
| Python | 리스트 컴프리헨션 / `filter()`     

---
