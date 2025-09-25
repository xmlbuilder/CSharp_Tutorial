# ✅ C#의 IEnumerable<T> 장점
- foreach 루프 사용 가능
- LINQ 메서드 (Where, Select, Take, SkipWhile, OrderBy, 등) 사용 가능
- 지연 실행(Lazy Evaluation): 실제로 데이터를 요청할 때만 계산됨
- 다양한 데이터 소스를 동일한 방식으로 처리 가능 (배열, 리스트, DB, 파일 등)
```csharp
var result = numbers.Where(n => n > 0).Take(5);
```

## 소스
```csharp
class Program
{
    static int Main(string[] args)
    {
        
        var numbers = new List<int>() {9, 7, -5, -4, 2, 5, 4, 0, -4, 8, -1, 0, 4};
        var datas = numbers.Where(n => n > 0).Take(5);
        foreach (var data in datas)
        {
            Console.WriteLine(data);  //9 7 2 5 4
        }
        var datas2 = numbers.TakeWhile(n => n > 0);
        foreach (var data in datas2)
        {
            Console.WriteLine(data); //9 7 -> 조건을 만족하는 동안에만 요소를 구한다.
        }
        var datas3 = numbers.SkipWhile(n => n >= 0);
        foreach (var data in datas3)
        {
            Console.WriteLine(data); //-5, -4, 3, 5, 4, 0, -4, 8, -1, 0, 4 
                   // SkipWhile 메소드는 지정한 조건을 만족하는 동안에는 요소를 건너뛰는 메소드임.
            // 값이 0 이상인 동안에는 건너뛰고 그 이후에 나오는 요소를 구한다.
        } 
        return 0;
    }
}
```

# 🌐 다른 언어에서의 유사 기능 비교
## 🧊 C++: STL의 std::ranges 및 std::views (C++20 이상)
- std::ranges::views::filter, take, drop_while 등으로 지연 실행 가능
- range 기반 for 루프 사용 가능
```cpp
#include <ranges>
auto result = numbers | std::views::filter([](int n){ return n > 0; }) | std::views::take(5);
```

- 장점: 성능 최적화, 지연 실행, 함수형 스타일
- 단점: C++20 이상에서만 사용 가능, 문법이 다소 복잡

## ☕ Java: Stream API (Java 8 이상)
- List.stream()을 통해 스트림 생성
- filter, limit, skip, takeWhile, dropWhile 등 사용 가능
```java
List<Integer> result = numbers.stream()
    .filter(n -> n > 0)
    .limit(5)
    .collect(Collectors.toList());
```

- 장점: 함수형 프로그래밍 스타일, 병렬 처리 가능
- 단점: 한 번만 소비되는 스트림, 재사용 불가

## 🐍 Python: iterator, generator, itertools
- filter, takewhile, dropwhile 등은 itertools에서 제공
- 리스트 컴프리헨션이나 제너레이터 표현식으로도 가능
```python
from itertools import islice, takewhile, dropwhile

numbers = [9, 7, -5, -4, 2, 5, 4, 0, -4, 8, -1, 0, 4]
result = list(islice(filter(lambda x: x > 0, numbers), 5))
```

- 장점: 간결한 문법, 지연 실행, 메모리 효율
- 단점: 타입 안정성 부족, 복잡한 로직은 가독성 저하

## 📊 요약 비교 (Markdown 표)
| 언어   | 시퀀스 처리 방식             | 지연 실행 | 주요 메서드 예시                         | 특징                                  |
|--------|------------------------------|-----------|---------------------------------------|-----------------------------------|
| C#     | `IEnumerable<T>` + LINQ     | ✅      | `Where`, `Take`, `SkipWhile`             | 강력한 LINQ, 타입 안정성, 직관적 문법 |
| C++    | `std::ranges` + `views`     | ✅      | `filter`, `take`, `drop_while`           | 고성능, 최신 문법, 복잡한 설정        |
| Java   | `Stream API`                | ✅      | `filter`, `limit`, `takeWhile`, `skip`   | 병렬 처리 가능, 일회성 스트림         |
| Python | `itertools`, generator      | ✅      | `filter`, `islice`, `takewhile`, `dropwhile` | 간결, 유연, 동적 타입            |


이런 시퀀스 기반 처리 방식은 데이터 흐름을 제어하고, 성능을 최적화하며, 코드 가독성을 높이는 데 매우 유용합니다.

