# 📐 문자열 정렬 및 공백 패딩 비교: C# vs Java vs C++
## ✅ C# (string.Format, StringBuilder, interpolation)
```csharp
string left = string.Format("LEFT: string: ->{0,-5}<- int: ->{1,-5}<-", "abc", 123);
string right = string.Format("RIGHT: string: ->{0,5}<- int: ->{1,5}<-", "abc", 123);
Console.WriteLine(left);
Console.WriteLine(right);
```

- {0,-5}: 왼쪽 정렬, 5칸
- {0,5}: 오른쪽 정렬, 5칸
- 공백으로 자동 패딩
- C# 6.0 이후에는 $"..." 보간 문자열에서도 사용 가능
```csharp
Console.WriteLine($"LEFT: ->{"abc",-5}<-");
Console.WriteLine($"RIGHT: ->{"abc",5}<-");
```
## 출력 결과
```
LEFT: string: ->abc <- int: ->123 <-
RIGHT: string: -> abc<- int: -> 123<-
```

## ☕ Java (String.format, Formatter)
```java
String left = String.format("LEFT: string: ->%-5s<- int: ->%-5d<-", "abc", 123);
String right = String.format("RIGHT: string: ->%5s<- int: ->%5d<-", "abc", 123);
System.out.println(left);
System.out.println(right);
```

- %-5s: 왼쪽 정렬, 5칸
- %5s: 오른쪽 정렬, 5칸
- String.format은 C 스타일 포맷팅을 그대로 계승

## 💻 C++ (std::setw, std::left, std::right, std::setfill)
```cpp
#include <iostream>
#include <iomanip>

std::cout << "LEFT: string: ->" << std::left << std::setw(5) << "abc"
          << "<- int: ->" << std::left << std::setw(5) << 123 << "<-" << std::endl;

std::cout << "RIGHT: string: ->" << std::right << std::setw(5) << "abc"
          << "<- int: ->" << std::right << std::setw(5) << 123 << "<-" << std::endl;

```
- std::setw(n): 너비 지정
- std::left, std::right: 정렬 방향
- 기본 패딩은 공백, std::setfill('0') 등으로 변경 가능

📊 기능 비교 요약 (Markdown 표)
| 기능                     | C# (`string.Format`)         | Java (`String.format`)       | C++ (`iomanip`)                  |
|--------------------------|------------------------------|-------------------------------|----------------------------------|
| 왼쪽 정렬                | `{0,-5}` 또는 `$"{val,-5}"`  | `%-5s`, `%-5d`                | `std::left` + `std::setw(5)`     |
| 오른쪽 정렬              | `{0,5}` 또는 `$"{val,5}"`    | `%5s`, `%5d`                  | `std::right` + `std::setw(5)`    |
| 공백 패딩                | 자동                         | 자동                          | 기본 공백, `setfill()`로 변경 가능 |
| 숫자/문자 모두 지원      | ✅                            | ✅                            | ✅                                |
| 포트란 스타일 고정 폭    | 매우 적합                    | 매우 적합                     | 매우 적합                         |



## 🧠 실무 팁 (특히 포트란 연동 시)
- 고정 폭 필드가 요구되는 경우, 반드시 정렬 + 패딩을 명시적으로 설정하세요.
- C++에서는 std::ostringstream와 함께 setw, left/right를 조합하면 포트란 입력 파일 생성에 매우 유용합니다.
- Java와 C#은 포맷 문자열을 템플릿처럼 관리하면 유지보수에 강합니다.

---

## 🧾 예시 데이터
```
- GRID* 카드:
GRID*   1                               1.27000e-01     0.00000e+00     *        
*       0.00000e+00

- CTRIA3 카드:
CTRIA3  7298    1       3261    3554    3347
```


### ✅ C# 구현
```csharp
using System;

class Program
{
    static void Main()
    {
        string line1 = string.Format("{0,-8}{1,-8}{2,16}{3,16}{4,-8}",
            "GRID*", "1", "1.27000e-01", "0.00000e+00", "*");
        string line2 = string.Format("{0,-8}{1,16}", "*", "0.00000e+00");

        string ctria3 = string.Format("{0,-8}{1,-8}{2,-8}{3,-8}{4,-8}{5,-8}",
            "CTRIA3", "7298", "1", "3261", "3554", "3347");

        Console.WriteLine(line1);
        Console.WriteLine(line2);
        Console.WriteLine(ctria3);
    }
}

```

### ☕ Java 구현
```java

public class Main {
    public static void main(String[] args) {
        String line1 = String.format("%-8s%-8s%16s%16s%-8s",
            "GRID*", "1", "1.27000e-01", "0.00000e+00", "*");
        String line2 = String.format("%-8s%16s", "*", "0.00000e+00");

        String ctria3 = String.format("%-8s%-8s%-8s%-8s%-8s%-8s",
            "CTRIA3", "7298", "1", "3261", "3554", "3347");

        System.out.println(line1);
        System.out.println(line2);
        System.out.println(ctria3);
    }
}
```


### 💻 C++ 구현
```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    cout << left << setw(8) << "GRID*"
         << setw(8) << "1"
         << right << setw(16) << "1.27000e-01"
         << setw(16) << "0.00000e+00"
         << left << setw(8) << "*" << endl;

    cout << left << setw(8) << "*"
         << right << setw(16) << "0.00000e+00" << endl;

    cout << left << setw(8) << "CTRIA3"
         << setw(8) << "7298"
         << setw(8) << "1"
         << setw(8) << "3261"
         << setw(8) << "3554"
         << setw(8) << "3347" << endl;

    return 0;
}
```


### 📌 포맷 요약
| 카드     | 필드 폭               | 설명                             |
|----------|------------------------|----------------------------------|
| `GRID*`  | 8, 8, 16, 16, 8 / 8,16 | 2줄 구성, 좌우 정렬 혼합         |
| `CTRIA3` | 8 x 6                  | 단일 줄, 모든 필드 왼쪽 정렬     |

---

