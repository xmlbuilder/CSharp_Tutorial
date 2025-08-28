

# 🔍 C#의 FirstOrDefault()와 FindIndex() 설명

## ✅ FirstOrDefault()
- 기능: 조건을 만족하는 첫 번째 요소를 반환. 없으면 기본값(null, 0, false 등)을 반환.
- 형식: collection.FirstOrDefault(predicate)
- 장점:
- 예외 없이 안전하게 첫 요소를 가져옴
- 조건을 람다식으로 지정 가능
- 빈 컬렉션에서도 예외 없이 처리됨

```csharp
var word = words.FirstOrDefault(x => x.Length == 4); // "over"
```


## ✅ FindIndex()
- 기능: 조건을 만족하는 첫 번째 요소의 인덱스를 반환. 없으면 -1 반환.
- 형식: collection.FindIndex(predicate)
- 장점:
- 인덱스 기반 처리에 유용
- 조건을 람다식으로 지정 가능
```csharp
var index = numbers.FindIndex(n => n > 0); // 0
```

## 소스
```csharp

class Program
{
    static int Main(string[] args)
    {
        var text = "The quick brown fox jumps over the lazy dog";
        var words = text.Split(" ");
        var word = words.FirstOrDefault(x => x.Length == 4);
        Console.WriteLine(word); 
        //over

        var numbers = new List<int>() {9, 7, -5, -4, 2, 5, 4, 0, -4, 8, -1, 0, 4};
        var index = numbers.FindIndex(n => n > 0);
        Console.WriteLine(index);
        //0

        return 0;
    }
}

```


## 🌐 다른 언어에서의 유사 기능 비교
### 🧊 C++: std::find_if + std::distance
- std::find_if는 조건을 만족하는 첫 번째 요소의 iterator를 반환
- 인덱스를 구하려면 std::distance를 사용
```cpp
#include <algorithm>
#include <vector>

auto it = std::find_if(numbers.begin(), numbers.end(), [](int n){ return n > 0; });
int index = std::distance(numbers.begin(), it); // 인덱스
```

- 없으면 it == numbers.end()로 확인

### ☕ Java: stream().filter().findFirst() + indexOf()
- findFirst()는 조건을 만족하는 첫 번째 요소를 Optional로 반환
- 인덱스를 구하려면 indexOf() 또는 루프 사용
```java
Optional<String> word = Arrays.stream(words)
    .filter(w -> w.length() == 4)
    .findFirst(); // Optional["over"]

int index = IntStream.range(0, numbers.size())
    .filter(i -> numbers.get(i) > 0)
    .findFirst()
    .orElse(-1); // 0
```



### 🐍 Python: next() + enumerate() + default
- next()와 filter() 또는 generator expression을 조합
- 인덱스는 enumerate()로 처리
```python
words = text.split(" ")
word = next((w for w in words if len(w) == 4), None)  # "over"

index = next((i for i, n in enumerate(numbers) if n > 0), -1)  # 0
```


- None이나 -1을 기본값으로 지정 가능

## 📊 요약 비교 (Markdown 표)
| 언어   | 첫 요소 찾기 방식                  | 인덱스 찾기 방식                 | 기본값 처리 | 특징                          |
|--------|-----------------------------------|----------------------------------|--------------|-------------------------------|
| C#     | `FirstOrDefault(predicate)`       | `FindIndex(predicate)`           | ✅            | 안전한 반환, LINQ와 호환       |
| C++    | `std::find_if`                    | `std::distance` + `find_if`      | ❌ (직접 처리) | 고성능, iterator 기반          |
| Java   | `stream().filter().findFirst()`   | `IntStream.range().filter()`     | ✅ (Optional) | 함수형 스타일, 예외 없음       |
| Python | `next(generator, default)`        | `next(enumerate(), default)`     | ✅            | 간결한 문법, 유연한 처리       |

---



