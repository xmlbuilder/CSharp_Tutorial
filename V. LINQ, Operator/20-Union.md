## ✅ C#의 Union() 기능
- 기능: 두 컬렉션의 합집합을 반환 (중복 제거)
- 형식: collection1.Union(collection2)
- 특징:
- 순서 유지 (첫 번째 컬렉션 기준)
- IEqualityComparer<T>로 커스텀 비교 가능
- 예시:

```csharp
int[] numbers1 = {1, 2, 3};
int[] numbers2 = {2, 3, 4, 5};

var allElement = numbers1.Union(numbers2); // 결과: {1, 2, 3, 4, 5}
Console.WriteLine(allElement.Count());     // 출력: 5
```

## 소스
```csharp
namespace GrammarTest
{
    public class Program
    {
       public static void Main(String[] args)
        {
            //두 시퀀스의 합집합을 구합니다.
            int[] numbers1 = {1, 2, 3};
            int[] numbers2 = {2, 3, 4, 5};

            var allElement = numbers1.Union(numbers2);

            Console.WriteLine(allElement.Count());
            //5
        }
    }

```


## 🧊 C++에서의 Union 기능
### 🔹 방법 1: std::set 기반 병합
```cpp
#include <vector>
#include <set>

std::vector<int> numbers1 = {1, 2, 3};
std::vector<int> numbers2 = {2, 3, 4, 5};

std::set<int> unionSet(numbers1.begin(), numbers1.end());
unionSet.insert(numbers2.begin(), numbers2.end()); // 중복 없이 병합
```

- std::set은 자동으로 중복 제거
- 순서는 보장되지 않음 (정렬됨)

## ☕ Java에서의 Union 기능
### 🔹 방법 1: Set + addAll()
```java
List<Integer> numbers1 = Arrays.asList(1, 2, 3);
List<Integer> numbers2 = Arrays.asList(2, 3, 4, 5);

Set<Integer> unionSet = new LinkedHashSet<>(numbers1);
unionSet.addAll(numbers2); // 중복 제거 + 순서 유지
```


### 🔹 방법 2: stream().distinct() (병합 후 필터링)
```java
List<Integer> union = Stream.concat(numbers1.stream(), numbers2.stream())
                            .distinct()
                            .collect(Collectors.toList());
```


## 🐍 Python에서의 Union 기능
### 🔹 방법 1: set.union()
```python
numbers1 = [1, 2, 3]
numbers2 = [2, 3, 4, 5]

union_set = set(numbers1).union(numbers2)  # 결과: {1, 2, 3, 4, 5}
```

### 🔹 방법 2: 리스트 병합 후 dict.fromkeys()로 중복 제거 + 순서 유지
```python
union = list(dict.fromkeys(numbers1 + numbers2))  # 결과: [1, 2, 3, 4, 5]
```


## 📊 요약 비교 (Markdown 표)
| 언어   | 합집합 방식                          | 중복 제거 | 순서 유지 | 특징 및 메모                          |
|--------|--------------------------------------|------------|------------|---------------------------------------|
| C#     | `Union(collection2)`                 | ✅         | ✅         | LINQ 기반, 커스텀 비교자 사용 가능     |
| C++    | `std::set` + `insert()`              | ✅         | ❌         | 자동 정렬, 명시적 병합 필요            |
| Java   | `Set.addAll()` / `Stream.distinct()` | ✅         | ✅ (`LinkedHashSet`) | 유연한 스트림 처리 가능         |
| Python | `set.union()` / `dict.fromkeys()`    | ✅         | ✅ (`dict 방식`) | 간결하고 직관적, 순서 제어 가능         |



이런 Union 기능은 실무에서 중복 없는 데이터 통합, 태그 병합, 사용자 목록 통합, 로그 병합 등에 자주 활용돼요.
---

