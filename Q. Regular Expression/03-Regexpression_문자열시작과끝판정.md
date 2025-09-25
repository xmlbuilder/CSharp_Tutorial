# 정규 표현식 문자열 판정 (^, $, \b)
정규 표현식의 핵심 메타 문자 ^, $, \b를 활용해 문자열의 시작/끝/단어 경계를 판별하는 방식

## 🧠 핵심 메타 문자 설명
| 메타 문자 | 의미                     | 예시 패턴      | 설명                                      | 사용 목적                  |
|-----------|--------------------------|----------------|-------------------------------------------|----------------------------|
| `^`       | 문자열의 시작            | `^Hello`       | "Hello"로 시작하는 문자열과 일치           | 접두어 검사                |
| `$`       | 문자열의 끝              | `World$`       | "World"로 끝나는 문자열과 일치             | 접미어 검사                |
| `\b`      | 단어 경계 (word boundary) | `\bclass\b`    | "class"라는 단어만 일치 (접두/접미 없음)   | 정확한 단어 추출 또는 필터 |

## 전체 소스
```csharp
using System.Text.RegularExpressions;

namespace GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            var text = "using System.Text.RegularExpression";
            bool isMatch = Regex.IsMatch(text, "^using");
            Console.WriteLine(isMatch);
            //True
            
            isMatch = Regex.IsMatch(text, "Expression$");
            Console.WriteLine(isMatch);
            //True
            
            var strings = new[] {"Microsoft Windows", "Windows Server", "Windows"};
            var regex = new Regex(@"^[Ww]indows$");
            var count = strings.Count(s => regex.IsMatch(s));
            Console.WriteLine(count);
            //1
            
            var strings1 = new[]
            {
                "1300", "-50.6", "0.123", "+180.00", "10.2.5", "320-0851",
                "$1200", "500원"
            };
            var regex2 = new Regex(@"^[-+]?(\d+)(\.\d+)?$");
            foreach (var s in strings1)
            {
                var isMatch1 = regex2.IsMatch(s);
                if(isMatch1) Console.WriteLine(s);
            }
            /*
             1300
            -50.6
            0.123
            +180.00
             */
            
            
            var text1 = "private List<string> results = new List<string>()?";
            Match match1 = Regex.Match(text1, @"List<\w+>");
            while (match1.Success)
            {
                Console.WriteLine($"{match1.Index} {match1.Length} {match1.Value}");
                match1 = match1.NextMatch();
            }
            /*
             8 12 List<string>
             35 12 List<string>
             */

            /*
             정규표현식에서 \b는 word boundary이며, 이름 그대로 word character(\w)의 경계를 말합니다.
             */
            
            var text2 = "private List<string> results = new List<string>()?";

            var matches = Regex.Matches(text2, @"\b[a-z]+\b")
                .Cast<Match>()
                .OrderBy(s => s.Length);

            foreach (var match in matches)
            {
                Console.WriteLine($"{match.Index} {match.Length} {match.Value}");
            }

            /*
             31 3 new
             13 6 string
             40 6 string
             0 7 private
             21 7 results
             */
        }
    }
}


```

## ✅ C# 예제 요약
```csharp
Regex.IsMatch("using System.Text.RegularExpression", "^using");     // True
Regex.IsMatch("using System.Text.RegularExpression", "Expression$"); // True
Regex.IsMatch("Windows", @"^[Ww]indows$");                            // True
Regex.IsMatch("1300", @"^[-+]?(\d+)(\.\d+)?$");                       // True
Regex.Matches("List<string>", @"\b[a-z]+\b");                         // 단어 추출
```


## ✅ C++ 예제 (C++11 이상)
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text = "using System.Text.RegularExpression";
    std::regex start("^using");
    std::regex end("Expression$");
    std::regex word(R"(\b[a-z]+\b)");

    std::cout << std::boolalpha << std::regex_search(text, start) << std::endl; // true
    std::cout << std::regex_search(text, end) << std::endl;                     // true

    std::string text2 = "private List<string> results = new List<string>()?";
    auto begin = std::sregex_iterator(text2.begin(), text2.end(), word);
    auto endIt = std::sregex_iterator();
    for (auto it = begin; it != endIt; ++it)
        std::cout << it->position() << " " << it->length() << " " << it->str() << std::endl;
}

```

## ✅ Java 예제
```java
import java.util.regex.*;

public class RegexTest {
    public static void main(String[] args) {
        String text = "using System.Text.RegularExpression";
        System.out.println(text.matches("^using.*"));         // true
        System.out.println(text.matches(".*Expression$"));    // true

        String text2 = "private List<string> results = new List<string>()?";
        Pattern pattern = Pattern.compile("\\b[a-z]+\\b");
        Matcher matcher = pattern.matcher(text2);
        while (matcher.find()) {
            System.out.println(matcher.start() + " " + matcher.end() + " " + matcher.group());
        }
    }
}

```

## ✅ Python 예제
```python
import re

text = "using System.Text.RegularExpression"
print(bool(re.match(r"^using", text)))           # True
print(bool(re.search(r"Expression$", text)))     # True

text2 = "private List<string> results = new List<string>()?"
matches = re.finditer(r"\b[a-z]+\b", text2)
for m in matches:
    print(m.start(), m.end() - m.start(), m.group())

```

## 🧪 요약 비교
| 언어     | 시작(^) 검사         | 끝($) 검사           | 단어 경계(\b) 추출 방식         |
|----------|----------------------|----------------------|----------------------------------|
| **C#**   | `Regex.IsMatch("^...")` | `Regex.IsMatch("...$")` | `Regex.Matches(@"\b...\b")`     |
| **C++**  | `std::regex_search("^...")` | `std::regex_search("...$")` | `std::sregex_iterator`         |
| **Java** | `text.matches("^...")` | `text.matches("...$")` | `Pattern + Matcher.find()`      |
| **Python** | `re.match("^...")`     | `re.search("...$")`     | `re.finditer(r"\b...\b")`       |


## 메타 문자 정리

| 메타 문자 | 의미                              | 예시 패턴        | 설명                                                    | 사용 목적                     |
|-----------|-----------------------------------|------------------|---------------------------------------------------------|-------------------------------|
| `^`       | 문자열의 시작                      | `^Hello`         | "Hello"로 시작하는 문자열과 일치                         | 접두어 검사                   |
| `$`       | 문자열의 끝                        | `World$`         | "World"로 끝나는 문자열과 일치                           | 접미어 검사                   |
| `\b`      | 단어 경계 (word boundary)          | `\bclass\b`      | "class"라는 단어만 일치 (앞뒤에 다른 문자가 없을 때)     | 정확한 단어 매칭              |
| `\B`      | 단어 경계가 아닌 위치              | `\Bclass\B`      | "class"가 단어 내부에 있을 때만 일치                     | 부분 단어 필터링              |
| `\d`      | 숫자 (digit)                       | `\d{3}`          | 숫자 3자리와 일치 (`123`, `456` 등)                      | 숫자 추출                     |
| `\D`      | 숫자가 아닌 문자                   | `\D+`            | 숫자가 아닌 문자들과 일치 (`abc`, `!@#`)                 | 숫자 제외 필터링              |
| `\s`      | 공백 문자 (space, tab 등)          | `Hello\sWorld`   | "Hello World"처럼 공백이 있는 문자열과 일치              | 공백 포함 여부 확인           |
| `\S`      | 공백이 아닌 문자                   | `\S+`            | 공백이 아닌 문자들과 일치 (`Hello`, `World!`)            | 공백 제외 필터링              |
| `\w`      | 단어 문자 (영문자, 숫자, _)        | `\w+`            | 하나 이상의 단어 문자와 일치 (`hello`, `user_1`)         | 단어 또는 식별자 추출         |
| `\W`      | 단어 문자가 아닌 문자              | `\W+`            | 특수 문자 등과 일치 (`!@#`, `-+=`)                       | 특수 문자 필터링              |



지금까지 정리한 핵심 메타 문자들을 바탕으로, 실제 프로그래밍 언어에서 어떻게 활용.

## ✅C# 에서의 활용 예제
```csharp
using System;
using System.Text.RegularExpressions;

class Program
{
    static void Main()
    {
        string input = "user@example.com, 010-1234-5678, keyword, Hello@World!";

        // 이메일 추출
        Match email = Regex.Match(input, @"\w+@\w+\.\w+");
        Console.WriteLine("📧 이메일: " + email.Value);  // user@example.com

        // 전화번호 추출
        Match phone = Regex.Match(input, @"\d{3}-\d{4}-\d{4}");
        Console.WriteLine("📱 전화번호: " + phone.Value);  // 010-1234-5678

        // 특정 단어만 필터링
        Match keyword = Regex.Match(input, @"\bkeyword\b");
        Console.WriteLine("🔍 키워드: " + keyword.Value);  // keyword

        // 특수 문자 제거
        string cleaned = Regex.Replace(input, @"\W+", "");
        Console.WriteLine("🧼 특수 문자 제거: " + cleaned);  // userexamplecom01012345678keywordHelloWorld
    }
}
```

## 🐍 Python에서의 활용 예제
Python에서는 re 모듈을 사용해 정규 표현식을 처리합니다.
```python
import re

# ^ : 시작
print(re.match(r'^Hello', 'Hello World'))  # ✅ 매칭됨

# $ : 끝
print(re.search(r'World$', 'Hello World'))  # ✅ 매칭됨

# \b : 단어 경계
print(re.search(r'\bclass\b', 'my class is great'))  # ✅ 'class'만 매칭

# \B : 단어 내부
print(re.search(r'\Bclass\B', 'myclassroom'))  # ✅ 'class'가 내부에 있음

# \d : 숫자
print(re.findall(r'\d+', 'ID: 123, Code: 4567'))  # ✅ ['123', '4567']

# \D : 숫자 아닌 문자
print(re.findall(r'\D+', 'ID: 123'))  # ✅ ['ID: ']

# \s : 공백
print(re.findall(r'\s', 'Hello World'))  # ✅ [' ']

# \S : 공백 아닌 문자
print(re.findall(r'\S+', 'Hello World'))  # ✅ ['Hello', 'World']

# \w : 단어 문자
print(re.findall(r'\w+', 'user_1, test123'))  # ✅ ['user_1', 'test123']

# \W : 특수 문자
print(re.findall(r'\W+', 'Hello@World!'))  # ✅ ['@', '!']
```


## 🌐 JavaScript에서의 활용 예제
JavaScript에서는 RegExp 객체와 .test(), .match() 등을 사용합니다.
```javascript
// ^ : 시작
/^Hello/.test("Hello World"); // ✅ true

// $ : 끝
/World$/.test("Hello World"); // ✅ true

// \b : 단어 경계
/\bclass\b/.test("my class is great"); // ✅ true

// \B : 단어 내부
/\Bclass\B/.test("myclassroom"); // ✅ true

// \d : 숫자
"ID: 123, Code: 4567".match(/\d+/g); // ✅ ['123', '4567']

// \D : 숫자 아닌 문자
"ID: 123".match(/\D+/g); // ✅ ['ID: ']

// \s : 공백
"Hello World".match(/\s/g); // ✅ [' ']

// \S : 공백 아닌 문자
"Hello World".match(/\S+/g); // ✅ ['Hello', 'World']

// \w : 단어 문자
"user_1, test123".match(/\w+/g); // ✅ ['user_1', 'test123']

// \W : 특수 문자
"Hello@World!".match(/\W+/g); // ✅ ['@', '!']
```


## 💡 실전 팁
| 상황 또는 목적         | 정규식 또는 코드 예시             | 예시 입력 / 결과값         | 설명                                      |
|------------------------|----------------------------------|-----------------------------|-------------------------------------------|
| 이메일 주소 추출       | `\w+@\w+\.\w+`                   | `user@example.com`          | 기본적인 이메일 형식과 일치               |
| 전화번호 형식 추출     | `\d{3}-\d{4}-\d{4}`              | `010-1234-5678`             | 한국형 휴대폰 번호 형식과 일치            |
| 특정 단어만 필터링     | `\bkeyword\b`                   | `keyword`                   | 정확히 "keyword"라는 단어만 매칭          |
| 특수 문자 제거 (Python) | `re.sub(r'\W+', '', text)`       | `'Hello@World!' → 'HelloWorld'` | 특수 문자 제거 후 깔끔한 문자열 반환       |

---


