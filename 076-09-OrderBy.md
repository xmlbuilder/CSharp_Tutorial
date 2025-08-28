## ✅ C#의 OrderBy / OrderByDescending
- 기능: 컬렉션을 특정 기준으로 정렬
- 형식:
- OrderBy(x => x) → 오름차순
- OrderByDescending(x => x) → 내림차순
- 예시:
```csharp
var sorted = numbers.OrderBy(n => n).ToArray(); // 오름차순
var sortedDesc = numbers.OrderByDescending(n => n).ToArray(); // 내림차순
```

## 소스
```csharp
namespace GrammarTest
{
  
    public class Program
    {
       public static void Main(String[] args)
        {
            //정렬 연산자인 OrderBy는 당연히 출력 시퀀스를 기준에 따라 정렬하는 연산자입니다. 

            string[] words = { "cherry", "apple", "blueberry" };
            var sortedWords1 = words.OrderBy(w => w).ToArray();
            // sortedWords1 = { "apple", "blueberry", "cherry" };
            Console.WriteLine(string.Join(", ", sortedWords1));
            //apple, blueberry, cherry

            var sortedWords2 = words.OrderBy(w => w.Length).ToArray();
            Console.WriteLine(string.Join(", ", sortedWords2));
            //apple, cherry, blueberry

            double[] doubles = { 1.7, 2.3, 1.9, 4.1, 2.9 };
            var sortedDoubles1 = doubles.OrderByDescending(n=>n).ToArray();
            Console.WriteLine(string.Join(", ", sortedDoubles1));
            //4.1, 2.9, 2.3, 1.9, 1.7

            var numbers = new List<int>() {19, 17, 15, 24, 12, 25, 14, 20, 12, 28, 19, 30, 4};
            var sorted = numbers.OrderBy(n => n).ToArray();
            Console.WriteLine(String.Join(",", sorted)); 
            //4,12,12,14,15,17,19,19,20,24,25,28,30

            var sorted1 = numbers.OrderByDescending(n => n).ToArray();
            Console.WriteLine(String.Join(",", sorted1)); 
            //30,28,25,24,20,19,19,17,15,14,12,12,4
        }
    }
}

```

## 🧊 C++: std::sort + 람다
- 기능: std::sort()로 정렬, 람다로 기준 지정
- 형식: std::sort(begin, end, comparator)
- 예시:
```cpp
#include <vector>
#include <algorithm>
#include <string>

std::vector<std::string> words = {"cherry", "apple", "blueberry"};

// 기본 오름차순
std::sort(words.begin(), words.end());

// 길이 기준 오름차순
std::sort(words.begin(), words.end(), [](const std::string& a, const std::string& b) {
    return a.length() < b.length();
});

// 내림차순
std::sort(words.begin(), words.end(), std::greater<>());
```


## ☕ Java: Collections.sort() / List.sort() + Comparator
- 기능: Comparator를 사용해 기준 지정
- 형식: list.sort((a, b) -> ...)
- 예시:
```java
List<String> words = Arrays.asList("cherry", "apple", "blueberry");

// 기본 오름차순
Collections.sort(words);

// 길이 기준 오름차순
words.sort(Comparator.comparing(String::length));

// 내림차순
words.sort(Comparator.reverseOrder());
```


## 🐍 Python: sorted() / .sort() + key 함수
- 기능: key 인자를 통해 기준 지정
- 형식: sorted(list, key=..., reverse=...)
- 예시:
```python
words = ["cherry", "apple", "blueberry"]

# 기본 오름차순
sorted_words1 = sorted(words)

# 길이 기준 오름차순
sorted_words2 = sorted(words, key=len)

# 내림차순
sorted_words3 = sorted(words, reverse=True)
```


## 📊 요약 비교
| 언어   | 오름차순 정렬 방식            | 내림차순 정렬 방식               | 정렬 기준 지정 방식             | 특징 및 메모            |
|--------|------------------------|----------------------------|----------------------------|-----------------------------------|
| C#     | `OrderBy(x => x)`        | `OrderByDescending(x => x)`   | 람다식 (`x => x.Length` 등) | LINQ 기반, 다양한 컬렉션 지원    |
| C++    | `std::sort(begin, end)` | `std::sort(..., std::greater<>)`| 람다 또는 `std::greater<>` | 고성능, 커스텀 비교 함수 사용 가능 |
| Java   | `Collections.sort(list)`| `Comparator.reverseOrder()`| `Comparator.comparing(...)`| 객체 정렬에 최적화, 유연한 Comparator |
| Python | `sorted(list)`           | `sorted(..., reverse=True)`  | `key=...` (`key=len` 등)   | 간결하고 직관적인 문법            |


이런 정렬 기능은 단순 리스트뿐 아니라 객체 리스트에서도 매우 유용하게 쓰입니다.
예를 들어 사용자 목록을 이름, 나이, 가입일 기준으로 정렬하거나, 파일 목록을 크기나 확장자 기준으로 정렬할 때도 활용됨.

---
