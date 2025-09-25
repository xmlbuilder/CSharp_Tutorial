# 🔁 C#의 Reverse()와 Join()

## ✅ Reverse()
- 기능: 컬렉션의 순서를 반대로 뒤집음
- 형식: collection.Reverse() → IEnumerable 반환 (지연 실행)
- 예시:
```csharp
var first = list.Reverse().Take(2);      // 끝에서 두 개: 6, 5
var reverseLast = list.Take(2).Reverse(); // 앞에서 두 개 뒤집기: 2, 1
```

## ✅ Join()
- 기능: 컬렉션의 요소들을 문자열로 연결
- 형식: String.Join(separator, collection)
- 예시:
```csharp
Console.WriteLine(String.Join(", ", first)); // "6, 5"
```

## 소스
```csharp
class Program
{
    static int Main(string[] args)
    {
        List<int> list = new List<int>() {1, 2, 3, 4, 5, 6};
        var first = list.Reverse<int>().Take(2);

        var reverseLast = list.Take(2).Reverse();

        Console.WriteLine(String.Join(", ", first));
        //6, 5

        Console.WriteLine(String.Join(", ", reverseLast));
        //2, 1

        return 0;
    }
}
```


## 🌐 다른 언어에서의 유사 기능
### 🧊 C++ (C++20 기준)
#### 🔁 Reverse
- std::ranges::reverse_view 또는 std::reverse 사용
```cpp
#include <ranges>
auto reversed = list | std::views::reverse;
```

- 또는 직접 std::reverse(list.begin(), list.end())로 변경
#### 🔗 Join
- C에는 join이 내장되어 있지 않지만, ostringstream 또는 ranges::join_with (C23) 사용 가능
```cpp
std::ostringstream oss;
for (size_t i = 0; i < list.size(); ++i) {
    if (i > 0) oss << ", ";
    oss << list[i];
}
std::string result = oss.str();
```



### ☕ Java
#### 🔁 Reverse
- Collections.reverse()로 리스트를 뒤집음 (in-place)
```java
Collections.reverse(list); // 변경됨
```

- 또는 stream()으로 처리
```java
List<Integer> reversed = IntStream.range(0, list.size())
    .mapToObj(i -> list.get(list.size() - 1 - i))
    .collect(Collectors.toList());
```

####  🔗 Join
- String.join() 또는 Collectors.joining()
```java
String result = list.stream()
    .map(String::valueOf)
    .collect(Collectors.joining(", "));
```


### 🐍 Python
#### 🔁 Reverse
- reversed() 함수 또는 슬라이싱 사용
```python
first = list[::-1][:2]       # [6, 5]
reverse_last = list[:2][::-1] # [2, 1]
```

#### 🔗 Join
- str.join() 사용 (문자열 리스트 필요)
```python
print(", ".join(map(str, first)))        # "6, 5"
print(", ".join(map(str, reverse_last))) # "2, 1"
```


## 📊 요약 비교 (Markdown 표)
| 언어   | Reverse 방식                         | Join 방식                            | 특징                                 |
|--------|--------------------------------------|--------------------------------------|--------------------------------------|
| C#     | `Reverse()` (LINQ, 지연 실행)        | `String.Join(separator, collection)` | 직관적, LINQ와 호환                  |
| C++    | `std::views::reverse`, `std::reverse`| `ostringstream`, `ranges::join_with` | 고성능, 최신 문법 필요               |
| Java   | `Collections.reverse()`, `stream()`  | `Collectors.joining()`               | 함수형 스타일, in-place 가능          |
| Python | `reversed()`, 슬라이싱 (`[::-1]`)     | `str.join(map(str, list))`           | 간결한 문법, 유연한 처리              |


이런 기능은 실무에서 UI 리스트 처리, 로그 출력, 데이터 역순 정렬, CSV 생성 등 다양한 곳에서 활용됩니다.

---
