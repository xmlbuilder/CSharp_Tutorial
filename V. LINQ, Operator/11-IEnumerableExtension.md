# IEnumerable<T>를 확장하여 LINQ
 C# 코드 예제는 IEnumerable<T>를 확장하여 LINQ 스타일의 Batch 기능을 구현


## 🧠 C# 코드 설명: Batch 확장 메서드
```csharp
namespace GrammarTest
{
    public static class LinqExtensions
    {
        public static IEnumerable<List<T>> Batch<T>(this IEnumerable<T> source, int batchSize)
        {
            var batch = new List<T>();
            foreach (var item in source)
            {
                batch.Add(item);
                if (batch.Count == batchSize)
                {
                    yield return batch;
                    batch = new List<T>();
                }
            }

            if (batch.Count > 0)
            {
                yield return batch;
            }
        }
    }

    public class Program
    {
        public static void Main(String[] args)
        {
            List<int> query = Enumerable.Range(0, 100).ToList();
            Console.WriteLine(query.Count);
            //100
            
            var queryList = query.Batch(10);
            foreach (var items in queryList)
            {
                Console.WriteLine("======================");   
                foreach (var item in items)
                {
                    Console.Write(item + " ");
                }
                Console.WriteLine();
            }
            /*
            ======================
            0 1 2 3 4 5 6 7 8 9 
            ======================
            10 11 12 13 14 15 16 17 18 19 
            ======================
            20 21 22 23 24 25 26 27 28 29 
            ======================
            30 31 32 33 34 35 36 37 38 39 
            ======================
            40 41 42 43 44 45 46 47 48 49 
            ======================
            50 51 52 53 54 55 56 57 58 59
            ======================
            60 61 62 63 64 65 66 67 68 69
            ======================
            70 71 72 73 74 75 76 77 78 79
            ======================
            80 81 82 83 84 85 86 87 88 89
            ======================
            90 91 92 93 94 95 96 97 98 99
             */
        }
    }
}

```


핵심 아이디어
- IEnumerable<T>를 확장하여 Batch<T>라는 메서드를 추가함으로써, 원본 컬렉션을 일정 크기의 묶음(List<T>)으로 나누어 처리할 수 있게 합니다.
- yield return을 사용하여 **지연 실행(lazy evaluation)**을 구현함으로써 메모리 효율성과 성능을 높입니다.
작동 방식
public static IEnumerable<List<T>> Batch<T>(this IEnumerable<T> source, int batchSize)


- source: 원본 컬렉션
- batchSize: 묶음 크기
흐름
- foreach 루프를 통해 원소를 하나씩 batch 리스트에 추가
- batch.Count == batchSize가 되면 yield return으로 반환하고 새로운 리스트 생성
- 마지막에 남은 원소가 있다면 한 번 더 yield return

## 🌍 다른 언어에서의 유사 구현
### 🐍 Python
Python은 itertools와 제너레이터를 활용해 유사한 기능을 쉽게 구현할 수 있습니다.
```python
from itertools import islice

def batch(iterable, batch_size):
    it = iter(iterable)
    while True:
        chunk = list(islice(it, batch_size))
        if not chunk:
            break
        yield chunk

# 사용 예시
for group in batch(range(100), 10):
    print(group)
```

- islice는 반복자에서 일정 개수만큼 슬라이스
- yield를 사용해 C#과 유사한 지연 실행 구현

### ☕ Java
Java 8부터는 Stream API를 통해 함수형 스타일로 처리할 수 있지만, Batch 기능은 직접 구현해야 합니다.
```java
public static <T> List<List<T>> batch(List<T> source, int batchSize) {
    List<List<T>> result = new ArrayList<>();
    for (int i = 0; i < source.size(); i += batchSize) {
        result.add(source.subList(i, Math.min(i + batchSize, source.size())));
    }
    return result;
}


- subList를 사용해 리스트를 일정 크기로 나눔
- Stream으로 래핑하면 더 함수형 스타일로 변형 가능
```

### 💠 C++
C++에서는 std::vector와 반복자(iterator)를 활용해 구현할 수 있습니다.
```cpp
#include <vector>
#include <iostream>

template<typename T>
std::vector<std::vector<T>> batch(const std::vector<T>& input, size_t batchSize) {
    std::vector<std::vector<T>> result;
    for (size_t i = 0; i < input.size(); i += batchSize) {
        auto start = input.begin() + i;
        auto end = (i + batchSize < input.size()) ? start + batchSize : input.end();
        result.emplace_back(start, end);
    }
    return result;
}
```

- emplace_back(start, end)로 부분 벡터 생성
- C++20부터는 ranges를 활용한 더 함수형 스타일도 가능

## ✨ 언어별 Batch 기능 비교

| 언어   | 핵심 기술 요소               | 지연 실행 지원 | 표준 라이브러리 지원 | 구현 난이도 |
|--------|------------------------------|----------------|----------------------|-------------|
| C#     | `IEnumerable`, `yield`       | ✅             | ❌ (직접 구현 필요)  | 쉬움        |
| Python | `islice`, 제너레이터         | ✅             | ❌ (직접 구현 필요)  | 쉬움        |
| Java   | `subList`, `Stream`          | ❌             | ❌ (직접 구현 필요)  | 보통        |
| C++    | `vector`, 반복자             | ❌             | ❌ (직접 구현 필요)  | 보통        |

---



