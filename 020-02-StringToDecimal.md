
# String To Decimal
Convert.ToString(value, base)를 사용해 10진수 → 2진수, 8진수, 16진수 변환을 보여주는 기능


## 🧵 C# (기존 예제)
```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        int number = 15;

        Console.WriteLine(Convert.ToString(number, 2));  // 1111 (Binary)
        Console.WriteLine(Convert.ToString(number, 8));  // 17   (Octal)
        Console.WriteLine(Convert.ToString(number, 16)); // f    (Hexadecimal)
    }
}
```

## 🧵 C++
C++에서는 std::bitset 또는 std::stringstream과 std::oct, std::hex를 사용합니다.
```cpp
#include <iostream>
#include <bitset>
#include <sstream>
using namespace std;

int main() {
    int number = 15;

    // Binary
    cout << bitset<8>(number) << endl; // 00001111 (8비트 예시)

    // Octal
    stringstream ss1;
    ss1 << oct << number;
    cout << ss1.str() << endl; // 17

    // Hexadecimal
    stringstream ss2;
    ss2 << hex << number;
    cout << ss2.str() << endl; // f
}
```

💡 bitset<8>의 <8>은 출력 비트 수를 의미하며, 필요에 따라 늘릴 수 있습니다.

## 🧵 Java
Java는 Integer.toBinaryString, toOctalString, toHexString 메서드를 제공합니다.
```java
public class Main {
    public static void main(String[] args) {
        int number = 15;

        System.out.println(Integer.toBinaryString(number)); // 1111
        System.out.println(Integer.toOctalString(number));  // 17
        System.out.println(Integer.toHexString(number));    // f
    }
}
``


## 🧵 Python
Python은 bin(), oct(), hex() 내장 함수를 사용합니다.
```python
number = 15

print(bin(number)[2:])  # 1111
print(oct(number)[2:])  # 17
print(hex(number)[2:])  # f
```

💡 [2:]는 접두사(0b, 0o, 0x)를 제거하기 위함입니다.
   bin(number)는 예를 들어 15를 넣으면 "0b1111" 같은 문자열을 반환하죠. 여기서 "0b"는 2진수임을 나타내는 접두사입니다.
- [start:end] : 문자열(또는 리스트 등 시퀀스)의 일부 구간을 잘라내는 문법
- start 인덱스부터 end 인덱스 직전까지 잘라냄
- start나 end를 생략하면 처음부터 또는 끝까지를 의미
[2:]는 인덱스 2부터 끝까지를 의미합니다.


## 📊 비교 요약

| 언어     | 2진수 변환                     | 8진수 변환                        | 16진수 변환                        |
|----------|--------------------------------|------------------------------------|-------------------------------------|
| **C#**   | `Convert.ToString(n, 2)`       | `Convert.ToString(n, 8)`           | `Convert.ToString(n, 16)`           |
| **C++**  | `bitset<8>(n)`                 | `stringstream << oct << n`         | `stringstream << hex << n`          |
| **Java** | `Integer.toBinaryString(n)`    | `Integer.toOctalString(n)`         | `Integer.toHexString(n)`            |
| **Python**| `bin(n)[2:]`                  | `oct(n)[2:]`                       | `hex(n)[2:]`                         |

---


## 🧵 역변환 개념
- 2진수 → 10진수 : 각 자릿수를 2의 거듭제곱으로 환산 후 합산
- 8진수 → 10진수 : 각 자릿수를 8의 거듭제곱으로 환산 후 합산
- 16진수 → 10진수 : 각 자릿수를 16의 거듭제곱으로 환산 후 합산
- 대부분의 언어는 내장 함수나 표준 라이브러리로 간단히 처리 가능

## 📊 역변환 비교 요약
| 언어     | 2진수 → 10진수 변환                  | 8진수 → 10진수 변환                  | 16진수 → 10진수 변환                  |
|----------|--------------------------------------|---------------------------------------|----------------------------------------|
| **C#**   | `Convert.ToInt32("1111", 2)`         | `Convert.ToInt32("17", 8)`            | `Convert.ToInt32("f", 16)`             |
| **C++**  | `stoi("1111", nullptr, 2)`           | `stoi("17", nullptr, 8)`              | `stoi("f", nullptr, 16)`               |
| **Java** | `Integer.parseInt("1111", 2)`        | `Integer.parseInt("17", 8)`           | `Integer.parseInt("f", 16)`            |
| **Python**| `int("1111", 2)`                    | `int("17", 8)`                        | `int("f", 16)`                         |


## 🧩 언어별 예제

### 🔹 C#
```csharp
Console.WriteLine(Convert.ToInt32("1111", 2));  // 15
Console.WriteLine(Convert.ToInt32("17", 8));    // 15
Console.WriteLine(Convert.ToInt32("f", 16));    // 15
```

### 🔹 C++
```cpp
#include <iostream>
using namespace std;

int main() {
    cout << stoi("1111", nullptr, 2) << endl; // 15
    cout << stoi("17", nullptr, 8) << endl;   // 15
    cout << stoi("f", nullptr, 16) << endl;   // 15
}
```

### 🔹 Java
```java
System.out.println(Integer.parseInt("1111", 2)); // 15
System.out.println(Integer.parseInt("17", 8));   // 15
System.out.println(Integer.parseInt("f", 16));   // 15
```

### 🔹 Python
```python
print(int("1111", 2))  # 15
print(int("17", 8))    # 15
print(int("f", 16))    # 15
```
---



