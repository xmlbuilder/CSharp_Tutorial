# LINQ Let 변수 정의
LINQ 쿼리에서 let 키워드를 사용해 변수 정의하는 방식

## 📘 LINQ에서 변수 정의 (let 키워드)

### ✅ 목적
LINQ 쿼리 내에서 중간 계산 결과를 변수로 저장하여 가독성과 성능을 높이는 데 사용됩니다.
### ✅ 예제 (C#)
```csharp
int[] numbers = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};

var aboveAverage = from number in numbers
                   let average = numbers.Average()
                   let nSquared = Math.Pow(number, 2)
                   where nSquared > average
                   select number;

foreach (var number in aboveAverage)
{
    Console.WriteLine(number);
}
```

### 🔍 설명
- let average = numbers.Average() : 전체 평균을 계산해 변수로 저장
- let nSquared = Math.Pow(number, 2) : 각 숫자의 제곱을 저장
- where nSquared > average : 제곱값이 평균보다 큰 숫자만 필터링

## 🔄 다른 언어에서 유사한 구현
### 🧠 핵심 아이디어
LINQ의 let은 중간 계산을 변수로 저장하는 기능이므로, 다른 언어에서는 일반적인 변수 선언과 필터링 로직으로 대체할 수 있습니다.

## 🧾 C++ (with STL and lambda)
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
#include <numeric>
#include <cmath>

int main() {
    std::vector<int> numbers = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
    double average = std::accumulate(numbers.begin(), numbers.end(), 0.0) / numbers.size();

    for (int n : numbers) {
        double nSquared = std::pow(n, 2);
        if (nSquared > average) {
            std::cout << n << std::endl;
        }
    }

    return 0;
}
```


## ☕ Java (with Streams)
```java
import java.util.Arrays;

public class LinqLike {
    public static void main(String[] args) {
        int[] numbers = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
        double average = Arrays.stream(numbers).average().orElse(0);

        Arrays.stream(numbers)
              .filter(n -> Math.pow(n, 2) > average)
              .forEach(System.out::println);
    }
}
```



## 🐍 Python (with list comprehension)
```python
numbers = list(range(10))
average = sum(numbers) / len(numbers)

above_average = [n for n in numbers if n**2 > average]

for number in above_average:
    print(number)

```


## 💡 비교 요약

| 언어   | 평균 계산 방식             | 제곱 계산 방식     | 필터링 방식     |
|--------|----------------------------|--------------------|-----------------|
| C#     | `let average = ...`        | `let nSquared = ...` | `where`         |
| C++    | `std::accumulate(...)`     | `std::pow(...)`    | `if` 조건문     |
| Java   | `stream().average()`       | `Math.pow(...)`    | `filter()`      |
| Python | `sum() / len()`            | `**2`              | 리스트 컴프리헨션 |

---


