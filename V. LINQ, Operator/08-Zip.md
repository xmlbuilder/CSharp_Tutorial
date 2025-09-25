# Zip
 C#의 Zip 메서드는 두 시퀀스의 요소를 병합하는 방법을 지정하는 함수입니다. 
 각 시퀀스의 동일한 인덱스에 있는 요소들을 하나씩 짝지어 새로운 컬렉션을 만드는 데 사용.

## 🔗 C# 예제 (기준)
```csharp
int[] numbers = {3, 5, 7};
string[] words = {"three", "five", "seven", "ignored"};

var zip = numbers.Zip(words, (n, w) => n + " = " + w);
foreach (var item in zip)
{
    Console.WriteLine(item);
}
// 출력:
// 3 = three
// 5 = five
// 7 = seven
```


## 🧩 C++ 예제
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

int main() {
    std::vector<int> numbers = {3, 5, 7};
    std::vector<std::string> words = {"three", "five", "seven", "ignored"};

    size_t count = std::min(numbers.size(), words.size());
    for (size_t i = 0; i < count; ++i) {
        std::cout << numbers[i] << " = " << words[i] << std::endl;
    }

    return 0;
}
```


📝 설명: std::min을 사용해 두 시퀀스 중 짧은 길이에 맞춰 병합합니다.

## ☕ Java 예제
```java
import java.util.*;

public class ZipExample {
    public static void main(String[] args) {
        int[] numbers = {3, 5, 7};
        String[] words = {"three", "five", "seven", "ignored"};

        int count = Math.min(numbers.length, words.length);
        List<String> zipped = new ArrayList<>();

        for (int i = 0; i < count; i++) {
            zipped.add(numbers[i] + " = " + words[i]);
        }

        zipped.forEach(System.out::println);
    }
}
```

📝 설명: List<String>에 병합된 결과를 저장하며, forEach로 출력합니다.

## 🐍 Python 예제
```python
numbers = [3, 5, 7]
words = ["three", "five", "seven", "ignored"]

zipped = [f"{n} = {w}" for n, w in zip(numbers, words)]
for item in zipped:
    print(item)
```

📝 설명: zip() 함수는 자동으로 짧은 쪽에 맞춰 병합하며, 리스트 컴프리헨션으로 간결하게 표현합니다.

## ✅ 출력 결과 (공통)
```
3 = three
5 = five
7 = seven
```

---

# Aggregate

Aggregate는 시퀀스의 모든 요소를 하나의 결과로 누적 처리하는 함수. 
마치 fold, reduce 같은 개념인데, 각 요소를 차례대로 처리하면서 결과를 축적해 나갑니다.

## 🧠 C# Aggregate 예제
```csharp
int[] numbers = {3, 5, 7};

int result = numbers.Aggregate((acc, n) => acc + n);
Console.WriteLine(result); // 출력: 15
```

📝 설명:
- acc는 누적값 (accumulator),
- n은 현재 요소
- acc + n을 반복해서 전체 합을 계산합니다.

## 🧩 C++ 유사 구현
```cpp
#include <iostream>
#include <vector>
#include <numeric>

int main() {
    std::vector<int> numbers = {3, 5, 7};

    int result = std::accumulate(numbers.begin(), numbers.end(), 0);
    std::cout << result << std::endl; // 출력: 15

    return 0;
}
```


📝 std::accumulate는 C++에서 Aggregate와 같은 역할을 합니다.

## ☕ Java 예제
```java
import java.util.Arrays;

public class AggregateExample {
    public static void main(String[] args) {
        int[] numbers = {3, 5, 7};

        int result = Arrays.stream(numbers).reduce(0, Integer::sum);
        System.out.println(result); // 출력: 15
    }
}
```


📝 reduce는 Java의 Stream API에서 누적 처리에 사용됩니다.

## 🐍 Python 예제
```python
from functools import reduce

numbers = [3, 5, 7]
result = reduce(lambda acc, n: acc + n, numbers)
print(result)  # 출력: 15
```


📝 reduce는 Python에서 Aggregate와 동일한 기능을 합니다.

✅ 핵심 요약
| 언어   | 함수명        | 설명                     |
|--------|---------------|--------------------------|
| C#     | `Aggregate`   | 시퀀스 요소를 누적 처리 |
| C++    | `accumulate`  | 범위의 합 또는 누적 계산 |
| Java   | `reduce`      | Stream에서 누적 처리     |
| Python | `reduce`      | 함수형 누적 처리         |
---




