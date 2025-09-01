## 정규식 그룹화(Capturing Group)를 이용한 문자열 치환
정규식에서 ()로 묶은 부분을 그룹(capturing group)이라고 부릅니다.
그룹화 기능을 활용하면 특정 패턴을 부분별로 나누어 캡처한 뒤, 치환(replace) 과정에서 $1, $2(또는 \1, \2) 같은 참조 구문으로 재조합할 수 있습니다.

## 주요 개념
- (\d{4})
숫자 4개에 해당하는 부분을 하나의 그룹으로 캡처
- 치환 시 참조
- C#, C++, Java: $1, $2, …
- Python         : \1, \2, … (raw string 권장)
- 전체 흐름
- 입력 문자열에서 패턴 매칭
- 매칭된 각 그룹을 캡처
- 치환 문자열에서 그룹 참조 구문으로 조합

## 비교
| 언어   | 라이브러리/함수                         | 그룹 참조 문법 |
| ------ | ---------------------------------------- | -------------- |
| C#     | System.Text.RegularExpressions / Regex.Replace() | $1$2           |
| C++    | `<regex>` / std::regex_replace()         | $1$2           |
| Java   | java.util.regex / String.replaceAll()    | $1$2           |
| Python | re / re.sub()                            | \1\2           |


## C# 예제
```csharp
using System;
using System.Text.RegularExpressions;

class Program
{
    static void Main()
    {
        var text    = "1234567890123456";
        var pattern = @"(\d{4})(\d{4})(\d{4})(\d{4})";
        var replaced = Regex.Replace(text, pattern, "$1-$2-$3-$4");
        Console.WriteLine(replaced);
        // 출력: 1234-5678-9012-3456
    }
}
```

- Regex.Replace(input, pattern, replacement)
- $1, $2, …로 캡처된 네 개의 그룹을 하이픈으로 연결

## C++ 예제
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text    = "1234567890123456";
    std::regex  pattern(R"((\d{4})(\d{4})(\d{4})(\d{4}))");
    // ECMAScript 문법을 기본으로 사용하며, $1-$2-$3-$4 참조 가능
    std::string replaced = std::regex_replace(text, pattern, "$1-$2-$3-$4");
    std::cout << replaced << std::endl;
    // 출력: 1234-5678-9012-3456
    return 0;
}
```

- <regex> 헤더에 있는 std::regex_replace
- ECMAScript 기반이므로 $1 방식으로 그룹 참조

## Java 예제
```java
public class Main {
    public static void main(String[] args) {
        String text    = "1024바이트, 8바이트 문자, 바이트, 킬로바이트";
        String pattern = "(\\d+)바이트";
        // replaceAll은 정규식 기반 치환, "$1byte"는 첫 번째 그룹 참조
        String replaced = text.replaceAll(pattern, "$1byte");
        System.out.println(replaced);
        // 출력: 1024byte, 8byte 문자, 바이트, 킬로바이트
    }
}
```

```java
var text = "1024바이트, 8바이트 문자, 바이트, 킬로바이트";
var pattern = "(\\d+)바이트";
var replaced = text.replaceAll(pattern, "$1byte");
System.out.println(replaced); //1024byte, 8byte 문자, 바이트, 킬로바이트
```

- String.replaceAll(regex, replacement)
- Java 문자열 내 \는 \\로 이스케이프

Python 예제
```python
import re

text    = "1234567890123456"
pattern = r"(\d{4})(\d{4})(\d{4})(\d{4})"
# raw string으로 패턴과 치환 문자열을 작성하면 이스케이프 걱정 최소화
replaced = re.sub(pattern, r"\1-\2-\3-\4", text)
print(replaced)
# 출력: 1234-5678-9012-3456
```


- re.sub(pattern, repl, string)
- 치환 문자열에서 \1, \2 방식으로 그룹 참조
- r"…"(raw string) 사용을 권장

## 추가로 알아두면 좋은 팁
- Named Group
- Python: (?P<name>\d{4}) → r"\g<name>-\g<other>"
- .NET:   (?<name>\d{4}) → @"${name}-${other}"
- 함수형 치환
- Python: re.sub(pattern, lambda m: transform(m), text)
- Java 9+: Matcher.replaceAll(Function<MatchResult,String>)
- Unicode/Multiline 옵션
- C#: RegexOptions.Multiline, RegexOptions.IgnoreCase
- Java: Pattern.MULTILINE, Pattern.CASE_INSENSITIVE
- Python: re.M, re.I

---
