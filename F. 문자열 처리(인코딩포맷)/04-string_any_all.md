# 🔍 문자열 조건 검색 비교: C#, C++, Java

## ✅ C# 예제
```csharp
using System;
using System.Linq;

namespace GrammarTest
{
    public class GrammarTest
    {
        public static void Main(String[] args)
        {
            var target = "C# Programming";

            var isExist = target.Any(c => Char.IsLower(c)); // 소문자가 하나라도 있는지
            Console.WriteLine(isExist); // True

            target = "12345678";
            isExist = target.All(c => Char.IsDigit(c)); // 모두 숫자인지
            Console.WriteLine(isExist); // True
        }
    }
}
```

| 조건         | 메서드                          | 설명                                      |
|--------------|----------------------------------|-------------------------------------------|
| 하나라도 만족 | `target.Any(c => ...)`          | 조건을 만족하는 문자가 하나라도 있는지    |
| 모두 만족    | `target.All(c => ...)`          | 모든 문자가 조건을 만족하는지             |


## ✅ C++ 예제 (C++20 기준)
```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <cctype>

int main() {
    std::string target = "C++ Programming";

    bool hasLower = std::any_of(target.begin(), target.end(), [](char c) {
        return std::islower(static_cast<unsigned char>(c));
    });
    std::cout << std::boolalpha << hasLower << std::endl; // true

    target = "12345678";
    bool allDigits = std::all_of(target.begin(), target.end(), [](char c) {
        return std::isdigit(static_cast<unsigned char>(c));
    });
    std::cout << std::boolalpha << allDigits << std::endl; // true
}
```

| 조건         | 메서드                          | 설명                                      |
|--------------|----------------------------------|-------------------------------------------|
| 하나라도 만족 | `target.Any(c => ...)`          | 조건을 만족하는 문자가 하나라도 있는지    |
| 모두 만족    | `target.All(c => ...)`          | 모든 문자가 조건을 만족하는지             |


## ✅ Java 예제 (Java 8 이상)
```java
public class GrammarTest {
    public static void main(String[] args) {
        String target = "Java Programming";

        boolean hasLower = target.chars().anyMatch(c -> Character.isLowerCase(c));
        System.out.println(hasLower); // true

        target = "12345678";
        boolean allDigits = target.chars().allMatch(c -> Character.isDigit(c));
        System.out.println(allDigits); // true
    }
}
```

| 조건         | 메서드                                | 설명                                      |
|--------------|----------------------------------------|-------------------------------------------|
| 하나라도 만족 | `target.chars().anyMatch(predicate)`   | 조건을 만족하는 문자가 하나라도 있는지    |
| 모두 만족    | `target.chars().allMatch(predicate)`   | 모든 문자가 조건을 만족하는지             |


## 🧠 요약 비교
| 언어   | 하나라도 만족 (`Any`)         | 모두 만족 (`All`)             | 람다 사용 여부 |
|--------|-------------------------------|-------------------------------|----------------|
| C#     | `target.Any(c => ...)`        | `target.All(c => ...)`        | ✅              |
| C++    | `std::any_of(..., [](char))`  | `std::all_of(..., [](char))`  | ✅ (C++11 이상) |
| Java   | `chars().anyMatch(...)`       | `chars().allMatch(...)`       | ✅ (Java 8 이상)|


