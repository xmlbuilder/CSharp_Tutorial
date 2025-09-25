# 📦 Array vs List 간단 비교
| 항목            | Array (`T[]`)                          | List (`List<T>`)                          |
|-----------------|----------------------------------------|-------------------------------------------|
| 크기            | 고정 (생성 시 결정됨)                  | 동적 (추가/삭제 가능)                     |
| 선언 방식       | `int[] arr = new int[5];`              | `List<int> list = new List<int>();`       |
| 요소 접근       | 인덱스로 접근 (`arr[0]`)               | 인덱스로 접근 (`list[0]`)                 |
| 기능            | 기본적인 저장 및 접근만 가능           | `Add`, `Remove`, `Insert`, `Sort` 등 다양 |
| 성능            | 메모리 연속성으로 빠름                 | 기능이 많아 약간 느릴 수 있음             |
| 사용 용도       | 고정된 크기의 데이터 처리에 적합       | 유동적인 데이터 처리에 적합               |
| 초기화 예시     | `new int[] {1, 2, 3}`                  | `new List<int> {1, 2, 3}`                 |
| 변환 가능 여부  | `ToList()`로 List로 변환 가능           | `ToArray()`로 Array로 변환 가능           |


# 🧪 일정한 값으로 채우는 코드 정리

## ✅ 1. List<int>에 -1을 20개 채우기
```csharp
var numbers = Enumerable.Repeat(-1, 20).ToList(); // -1을 20개 채움
string s1 = String.Join(",", numbers);
Console.WriteLine(s1); // 출력: -1,-1,-1,...(총 20개)
```

- Enumerable.Repeat(value, count)는 동일한 값을 반복 생성
- .ToList()로 List로 변환
- String.Join(",", list)로 콤마로 연결된 문자열 생성

## ✅ 2. string[] 배열에 "Unknown"을 12개 채우기
```csharp
var strings = Enumerable.Repeat("Unknown", 12).ToArray(); // "Unknown"을 12개 채움
string s2 = String.Join(",", strings);
Console.WriteLine(s2); // 출력: Unknown,Unknown,...(총 12개)
```


- .ToArray()로 배열 생성
- String.Join으로 문자열 연결

## ✅ 3. 1부터 20까지의 숫자 배열 생성
```csharp
var array = Enumerable.Range(1, 20).ToArray(); // 1부터 20까지 숫자 생성
string s3 = String.Join(",", array);
Console.WriteLine(s3); // 출력: 1,2,3,...,20
```

- Enumerable.Range(start, count)는 연속된 숫자 생성
- .ToArray()로 배열로 변환

## 💡 요약
- Enumerable.Repeat: 같은 값을 반복해서 생성할 때 사용
- Enumerable.Range: 연속된 숫자 생성할 때 사용
- ToList() vs ToArray(): 결과를 List 또는 Array로 변환
- String.Join: 리스트나 배열을 문자열로 출력할 때 유용



# 🧊 C++: std::vector와 std::fill, std::iota
## 🔹 일정한 값으로 채우기 (-1을 20개)
```cpp
#include <iostream>
#include <vector>
#include <algorithm> // for std::fill

int main() {
    std::vector<int> numbers(20); // 크기 20의 벡터 생성
    std::fill(numbers.begin(), numbers.end(), -1); // 모든 요소를 -1로 채움

    for (int n : numbers)
        std::cout << n << ",";
}
```

## 🔹 연속된 값으로 채우기 (1부터 20까지)
```cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::iota

int main() {
    std::vector<int> sequence(20);
    std::iota(sequence.begin(), sequence.end(), 1); // 1부터 시작해서 채움

    for (int n : sequence)
        std::cout << n << ",";
}
```



# ☕ Java: Arrays.fill, IntStream.range
## 🔹 일정한 값으로 채우기 ("Unknown"을 12개)
```cpp
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        String[] strings = new String[12];
        Arrays.fill(strings, "Unknown"); // 모든 요소를 "Unknown"으로 채움

        System.out.println(String.join(",", strings));
    }
}
```

🔹 연속된 값으로 채우기 (1부터 20까지)
```java
import java.util.stream.IntStream;

public class Main {
    public static void main(String[] args) {
        int[] array = IntStream.rangeClosed(1, 20).toArray(); // 1부터 20까지

        String result = Arrays.toString(array).replaceAll("[\\[\\] ]", "");
        System.out.println(result); // 출력: 1,2,3,...,20
    }
}
```


📌 요약
| 언어   | 일정한 값 채우기                           | 연속된 값 채우기                          |
|--------|--------------------------------------------|-------------------------------------------|
| C#     | `Enumerable.Repeat(value, count)`          | `Enumerable.Range(start, count)`          |
| C++    | `std::fill(begin, end, value)`             | `std::iota(begin, end, start_value)`      |
| Java   | `Arrays.fill(array, value)`                | `IntStream.rangeClosed(start, end)`       |

---



