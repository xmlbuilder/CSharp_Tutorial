## 📌 문자열 메서드 비교: C# vs Java vs C++

| 기능                     | C#                                | Java                              | C++ (std::string)                      |
|--------------------------|------------------------------------|------------------------------------|----------------------------------------|
| Null 또는 빈 문자열 확인 | `String.IsNullOrEmpty(str)`        | `str == null || str.isEmpty()`     | `str.empty()` *(null 체크는 별도)*     |
| Null/공백 문자열 확인    | `String.IsNullOrWhiteSpace(str)`   | `str == null || str.trim().isEmpty()` | `str.find_first_not_of(" \t\n") == std::string::npos` |
| 시작 문자열 확인         | `str.StartsWith("Visual")`         | `str.startsWith("Visual")`         | `str.rfind("Visual", 0) == 0`          |
| 끝 문자열 확인           | `str.EndsWith("Exception")`        | `str.endsWith("Exception")`        | `str.size() >= 9 && str.compare(str.size() - 9, 9, "Exception") == 0` |
| 포함 여부 확인           | `str.Contains("Exception")`        | `str.contains("Exception")` *(Java 11+)*<br>`str.indexOf("Exception") >= 0` *(Java 8)* | `str.find("Exception") != std::string::npos` |

## 예제 코드
``` csharp
namespace GrammarTest
{
    public class GrammarTest
    {
        public static void Main(String[] args)
        {
            string str = "jhjeong";
            if (!String.IsNullOrEmpty(str))
            {
                Console.WriteLine(str);
            }
            //jhjeong
            
            str = " ";
            if (String.IsNullOrWhiteSpace(str))
            {
                Console.WriteLine("빈문자 null or Whitespace");
            }
            //빈문자 null or Whitespace
            
            str = "Visual Exception";
            if (str.StartsWith("Visual"))
            {
                Console.WriteLine(str);
            }
            //Visual Exception
            
            if (str.EndsWith("Exception"))
            {
                Console.WriteLine(str);
            }
            //Visual Exception
            
            if (str.Contains("Exception"))
            {
                Console.WriteLine(str);
            }
            //Visual Exception
        }
    }
}
```

## 🧠 추가 설명
- **C#**은 String 클래스에 다양한 유틸리티 메서드가 내장되어 있어 null 안전성이 뛰어납니다.
- Java는 null 체크를 직접 해야 하며, trim()을 활용해 공백 제거 후 비교합니다.
- **C++**은 std::string이 null을 허용하지 않기 때문에, nullptr 체크는 별도로 필요하며, 공백 확인은 find_first_not_of() 같은 메서드를 사용합니다.


✂️ C++에서 trim 구현 예시
#include <string>
#include <cctype>

std::string trim(const std::string& str) {
    size_t first = str.find_first_not_of(" \t\n\r");
    if (first == std::string::npos) return ""; // 모두 공백
    size_t last = str.find_last_not_of(" \t\n\r");
    return str.substr(first, last - first + 1);
}


- find_first_not_of와 find_last_not_of를 사용해 앞뒤 공백을 제거합니다.
- 공백 문자 집합은 " \t\n\r"로 정의했지만 필요에 따라 확장 가능해요.


| 언어   | 메서드 이름     | 내장 여부 | 사용 예시                          |
|--------|------------------|-----------|------------------------------------|
| C++    | `trim()`         | ❌ 직접 구현 | `trim(str)`                        |
| Java   | `trim()`         | ✅ 내장     | `str.trim()`                       |
| C#     | `Trim()`         | ✅ 내장     | `str.Trim()`                       |


## 🔪 문자열 분리 (Delimiter 기반 Split) 비교

| 언어   | 기본 메서드 / 방식         | 예시 코드                             | 특징 / 단점                          |
|--------|-----------------------------|----------------------------------------|--------------------------------------|
| C++    | `std::stringstream`         | `getline(ss, token, delim)`            | 반복문 필요, 공백 포함 처리 주의     |
|        | `std::regex` (복잡한 경우) | `std::regex_token_iterator`            | 성능 저하 가능, 복잡한 문법          |
|        | 직접 구현                   | `find()` + `substr()` 반복             | 유연하지만 코드 길어짐               |
| Java   | `String.split(regex)`       | `str.split(",")`                       | 간단, 정규식 사용 가능               |
| C#     | `String.Split(char[])`      | `str.Split(',')`                       | 배열로 여러 delim 지정 가능           |
|        | `String.Split(string[])`    | `str.Split(new[] { "," }, ...)`        | 문자열 배열도 가능, 옵션 다양        |



✅ C++ 예시: stringstream 방식


#include <sstream>
#include <vector>
#include <string>

std::vector<std::string> split(const std::string& str, char delim) {
    std::stringstream ss(str);
    std::string token;
    std::vector<std::string> result;
    while (std::getline(ss, token, delim)) {
        result.push_back(token);
    }
    return result;
}


✅ Java 예시

String[] parts = str.split(",");

- 정규식 기반이라 "\\s+" 같은 공백 분리도 쉽게 가능.
- 단점은 split()이 빈 문자열도 포함할 수 있다는 점.


✅ C# 예시
string[] parts = str.Split(',');

- StringSplitOptions.RemoveEmptyEntries 옵션으로 빈 항목 제거 가능.
- Split(new[] { ",", ";" }, StringSplitOptions.None)처럼 여러 구분자도 처리 가능.


🧠 요약
- C++: 유연하지만 번거롭고 직접 구현 필요. std::ranges나 views::split은 C++23부터 가능.
- Java/C#: 내장 메서드로 간단하게 처리 가능. 정규식이나 옵션도 풍부.


✨ C++23 std::views::split 예제


#include <iostream>
#include <string>
#include <ranges>

int main() {
    std::string str = "apple,banana,orange";
    
    for (auto token : std::views::split(str, ',')) {
        // token은 range이므로 다시 문자열로 변환 필요
        std::string word;
        for (char c : token) {
            word += c;
        }
        std::cout << word << std::endl;
    }

    return 0;
}

🔍 출력 결과
apple
banana
orange

apple
banana
orange


✅ 주요 특징
- std::views::split(str, delim)은 lazy range를 반환합니다.
- 각 token은 std::ranges::subrange 타입이므로, 직접 문자열로 변환해야 해요.
- std::string_view로 변환하면 더 효율적입니다:
std::string_view sv = str;
for (auto token : std::views::split(sv, ',')) {
    std::cout << std::string_view(&*token.begin(), std::ranges::distance(token)) << '\n';
}


## 🔍 문자열 분리 방식 비교: 기존 방식 vs C++23

| 방식              | 코드 길이 | 성능       | 가독성     | 특징                                       |
|-------------------|-----------|------------|------------|--------------------------------------------|
| std::stringstream | 중간      | 좋음       | 중간       | 가장 널리 사용됨. 반복문 필요. 공백 처리 주의 |
| find + substr     | 길다      | 매우 좋음  | 낮음       | 유연하지만 직접 구현 필요. 코드 길어짐       |
| views::split (C++23) | 짧다   | 매우 좋음  | 매우 높음  | 선언적 스타일. lazy range. 문자열 변환 필요  |
