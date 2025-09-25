# Padding a string to a fixed length
Fortran에서 입력 형식이 엄격하게 맞아야 하는 것처럼, 다른 언어에서도 **패딩(Padding)**은 출력 정렬, 고정 폭 포맷, 데이터 정렬 등에 매우 중요합니다.   
특히 보고서 출력, 로그 포맷, 테이블 렌더링 등에 자주 사용되죠. 

##  🧵 C# Padding & Formatting
C#은 string.PadLeft, PadRight 메서드와 서식 문자열(string interpolation)을 통해 매우 직관적으로 패딩을 처리합니다.
### 🔹 문자열 패딩
```csharp
string s = "Foo";
s.PadLeft(5);       // " Foo"
s.PadRight(6, '+'); // "Foo+++"
```

### 🔹 숫자 포맷팅
```csharp
var number = 42;
$"{number,5:f3}"       // "42.000" (총 5칸, 소수점 3자리)
$"{number,20:n3}"      // "              42.000"
$"{number,-20:n}"      // "42.00               "
```

- {value,alignment:format} 구조
- alignment가 양수면 오른쪽 정렬, 음수면 왼쪽 정렬
- f3, n3 등은 소수점 자리수 및 천 단위 구분자 포함 여부


## 🧵 C++ Padding & Formatting
C++에서는 std::setw, std::setfill, std::left, std::right 등을 사용하여 iostream 기반 출력에서 패딩을 처리합니다.
### 🔹 문자열 및 숫자 패딩
```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    string s = "Foo";
    cout << setw(5) << s << endl;               // "  Foo"
    cout << left << setw(6) << setfill('+') << s << endl; // "Foo+++"

    double number = 42;
    cout << setw(10) << fixed << setprecision(3) << number << endl; // "    42.000"
    cout << left << setw(10) << number << endl;                     // "42.000    "
}
```

- setw(n)은 출력 폭 지정
- setfill(char)는 빈 공간 채우기
- left, right는 정렬 방향 지정
- setprecision(n)은 소수점 자리수




## 🧵 Java Padding & Formatting
Java는 String.format, Formatter, 또는 System.out.printf를 사용하여 패딩과 포맷을 처리합니다.
### 🔹 문자열 및 숫자 패딩
```java
public class Main {
    public static void main(String[] args) {
        String s = "Foo";
        System.out.printf("%5s\n", s);           // "  Foo"
        System.out.printf("%-6s\n", s);          // "Foo   "

        double number = 42;
        System.out.printf("%10.3f\n", number);   // "    42.000"
        System.out.printf("%-10.2f\n", number);  // "42.00     "
    }
}
```

- %ns는 문자열 폭 지정
- %n.mf는 숫자 폭과 소수점 자리수 지정
- -는 왼쪽 정렬

## 🧵 Python Padding & Formatting
Python은 str.ljust, rjust, center와 f-string, format()을 통해 유연하게 패딩을 처리합니다.

### 🔹 문자열 패딩
```python
s = "Foo"
print(s.rjust(5))           # "  Foo"
print(s.ljust(6, '+'))      # "Foo+++"
```

### 🔹 숫자 포맷팅
```python
number = 42
print(f"{number:10.3f}")    # "    42.000"
print(f"{number:<10.2f}")   # "42.00     "
```

- :<10.2f → 왼쪽 정렬, 폭 10, 소수점 2자리
- :>10.3f → 오른쪽 정렬, 폭 10, 소수점 3자리
- :^10 → 가운데 정렬


## 📊 문자열 및 숫자 패딩 비교 요약

| 언어     | 문자열 패딩 방식                     | 숫자 포맷 방식                         | 정렬 방향 제어 | 비고 |
|----------|--------------------------------------|----------------------------------------|----------------|------|
| **C#**   | `PadLeft`, `PadRight`                | `$"{value,align:format}"`              | `,align`       | 직관적 |
| **C++**  | `setw`, `setfill`, `left/right`      | `fixed`, `setprecision`                | `left/right`   | 강력함 |
| **Java** | `printf`, `String.format`            | `%.nf`, `%ns`, `%-ns`                  | `-` 기호       | 포맷 문자열 기반 |
| **Python**| `rjust`, `ljust`, `f-string`         | `f"{value:width.precision}"`           | `<`, `>`, `^`  | 유연함 |

---


