# C#의 TryParse 개념

| 특징 | 설명 |
|------|------| 
| 목적 | 문자열을 숫자 등으로 변환하면서 실패 시 예외 없이 false 반환 | 
| 반환값 | bool (성공 여부) | 
| 출력값 | out 파라미터로 결과 저장 | 
| 예외 발생 여부 | 없음 (안전하게 실패 처리) | 


## ✅ 예시
```csharp
int height;
if (int.TryParse("124", out height))
    Console.WriteLine(height);
else
    Console.WriteLine("Parsing Error");
```


## 🧩 C++에서의 대체 방식
C++에는 TryParse와 동일한 내장 함수는 없지만, **std::stringstream 또는 std::from_chars**를 사용하여 유사한 기능을 구현할 수 있습니다.

### ✅ std::stringstream 방식
```cpp
#include <iostream>
#include <sstream>

bool TryParseInt(const std::string& str, int& result) {
    std::stringstream ss(str);
    ss >> result;
    return !ss.fail() && ss.eof(); // 변환 성공 + 전체 문자열 사용
}

int main() {
    int height;
    if (TryParseInt("124", height))
        std::cout << height << std::endl;
    else
        std::cout << "Parsing Error" << std::endl;
}
```

### ✅ std::from_chars (C++17 이상)
```cpp
#include <charconv>
#include <iostream>

bool TryParseInt(const std::string& str, int& result) {
    auto [ptr, ec] = std::from_chars(str.data(), str.data() + str.size(), result);
    return ec == std::errc();
}
```

## 🧩 Java에서의 대체 방식

Java는 TryParse가 없지만, 예외 처리 기반으로 유사한 기능을 구현할 수 있습니다.
### ✅ try-catch 방식
```java
public class GrammarTest {
    public static boolean tryParseInt(String str, int[] out) {
        try {
            out[0] = Integer.parseInt(str.trim());
            return true;
        } catch (NumberFormatException e) {
            return false;
        }
    }

    public static void main(String[] args) {
        int[] height = new int[1];
        if (tryParseInt("124", height))
            System.out.println(height[0]);
        else
            System.out.println("Parsing Error");
    }
}
```

### 📌 Java에서는 out 파라미터를 흉내내기 위해 배열이나 래퍼 객체를 사용합니다.

## ✨ 요약 비교
| 언어 | 방식 | 예외 발생 | 반환값 | 안전성 |
|-----|------|----------|-------|-------| 
| C# | TryParse | ❌ 없음 | bool | ✅ 매우 안전 | 
| C++ | stringstream / from_chars | ❌ 없음 | bool | ✅ | 
| Java | try-catch + parseInt | ✅ 있음 | boolean | ⚠️ 예외 비용 있음 | 

---


## 🔢 정수형: long
- 의미: 큰 범위의 정수를 저장할 수 있는 타입
- 크기:
- C#: 64비트 (–9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807)
- C++: 플랫폼에 따라 다르지만 보통 64비트
- Java: 항상 64비트
- 예시:
long population = 7800000000L;



## 🌊 실수형: float vs double
| 타입 | 크기 | 정밀도 | 사용 예시 |
|------|-----|-------|----------| 
| float | 32비트 | 소수점 약 7자리 | 메모리 절약이 중요한 경우 | 
| double | 64비트 | 소수점 약 15~16자리 | 일반적인 실수 계산 | 


## ✅ 예시
```csharp
float piApprox = 3.14159f;
double piExact = 3.141592653589793;
```

💡 float은 뒤에 f를 붙여야 하고, double은 기본 실수 타입이라 별도 접미사가 필요 없어요.


## 🧪 TryParse 예시 (C# 기준)
```csharp
float f;
if (float.TryParse("3.14", out f))
    Console.WriteLine(f);

double d;
if (double.TryParse("2.71828", out d))
    Console.WriteLine(d);

long l;
if (long.TryParse("1234567890123", out l))
    Console.WriteLine(l);
```


## 🧩 Java와 C++에서도 사용 가능
- Java:
```java
float f = Float.parseFloat("3.14");
double d = Double.parseDouble("2.71828");
long l = Long.parseLong("1234567890123");
```

- C++:
```cpp
float f = std::stof("3.14");
double d = std::stod("2.71828");
long l = std::stol("1234567890123");
```
---





