# Regular Expression MultiLine / IgnoreCase
C#에서 RegexOptions.IgnoreCase와 RegexOptions.Multiline을 활용한 예제를 기반으로, 
아래는 각 언어별로 대소문자 구별 없이, 그리고 멀티라인 처리를 하는 방법을 예제와 함께 보여주는 표입니다.

## 🧠 Multiline + IgnoreCase 정리표 (언어별)
| 언어   | 대소문자 무시 옵션         | 멀티라인 옵션              | 예시 코드 요약                                                                 |
|--------|-----------------------------|-----------------------------|--------------------------------------------------------------------------------|
| C#     | `RegexOptions.IgnoreCase`   | `RegexOptions.Multiline`    | `Regex.Matches(text, pattern, RegexOptions.IgnoreCase | RegexOptions.Multiline)` |
| C++    | `std::regex_constants::icase` | `(?m)` 패턴 사용 또는 줄 단위 처리 | `std::regex pattern("(?m)^pattern", std::regex_constants::icase);`             |
| Java   | `Pattern.CASE_INSENSITIVE`  | `Pattern.MULTILINE`         | `Pattern.compile(pattern, CASE_INSENSITIVE | MULTILINE)`                        |
| Python | `re.IGNORECASE`             | `re.MULTILINE`              | `re.findall(pattern, text, re.IGNORECASE | re.MULTILINE)`                       |



## 🔍 각 언어별 예제 코드
✅ C# 예제
```csharp
using System.Text.RegularExpressions;
namespace GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            var text = "C#에는 <값형>과 <참조형>이라는 두가지의 형이 존재합니다.";
            var matches1 = Regex.Matches(text, @"<([^<>]+)>");
            foreach (Match match in matches1)
            {
                Console.WriteLine(match.Groups[1]);
            }
            /*
             값형
             참조형
             */
            var text1 = "kor, KOR, Kor";
            var mc = Regex.Matches(text1, @"\bkor\b", RegexOptions.IgnoreCase);
            foreach (Match match in mc)
            {
                Console.WriteLine(match);
            }
            /*
             kor
             KOR
             Kor
             */
            var text2 = "Word\nExcel\nPowerPoint\nOutlook\nOneNote\n";
            var pattern = @"^[a-zA-Z]{5,7}";
            var matches2 = Regex.Matches(text2, pattern, RegexOptions.Multiline);
            foreach (Match match in matches2)
            {
                Console.WriteLine(match.Value);
            }
            /*
             Excel
             PowerPo
             Outlook
             OneNote
             */
        }
    }
}
```

## ✅ C++ 예제 (<regex> 사용)
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text = "kor\nKOR\nKor";
    std::regex pattern("\\bkor\\b", std::regex_constants::icase);
    auto words_begin = std::sregex_iterator(text.begin(), text.end(), pattern);
    auto words_end = std::sregex_iterator();

    for (std::sregex_iterator i = words_begin; i != words_end; ++i)
        std::cout << i->str() << std::endl;
}
```
멀티라인은 C++에서 (?m) 패턴을 직접 넣거나 줄 단위로 처리하는 방식이 일반적입니다.


## ✅ Java 예제
```java
import java.util.regex.*;

public class Main {
    public static void main(String[] args) {
        String text = "kor\nKOR\nKor";
        Pattern pattern = Pattern.compile("\\bkor\\b", Pattern.CASE_INSENSITIVE | Pattern.MULTILINE);
        Matcher matcher = pattern.matcher(text);

        while (matcher.find()) {
            System.out.println(matcher.group());
        }
    }
}
```

## ✅ Python 예제
```python
import re

text = "kor\nKOR\nKor"
pattern = r"\bkor\b"
matches = re.findall(pattern, text, re.IGNORECASE | re.MULTILINE)

for match in matches:
    print(match)
```


## 💡 참고 팁
- Multiline 옵션은 ^, $를 각 줄의 시작과 끝으로 인식하게 해줍니다.
- IgnoreCase는 대소문자 구별 없이 매칭합니다.
- C++은 옵션을 std::regex_constants::icase로 지정하고, 멀티라인은 직접 패턴에 (?m)을 넣거나 줄 단위로 처리합니다.

---

