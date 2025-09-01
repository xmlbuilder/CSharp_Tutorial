# 정규식 기반 문자열 분할(Split)
정규식을 이용한 문자열 분할은 구분자 패턴을 정의하고, Split 계열 함수를 호출하여 입력 문자열을 배열로 나누는 방식입니다.
아래 예제에서는 쉼표(,)와 주변 공백(\s*)을 제거하며 분할하는 패턴 \s*,\s*를 사용합니다.

## 📝 주요 함수 비교
| 언어   | 라이브러리/함수               | 호출 방식                                                           |
| ------ | ----------------------------- | ------------------------------------------------------------------- |
| C#     | System.Text.RegularExpressions | Regex.Split(text, pattern)                                          |
| C++    | <regex>                        | std::regex_split(text, pattern)                                     |
| Java   | java.util.regex               | text.split(pattern)<br>Pattern.compile(pattern).split(text)         |
| Python | re                            | re.split(pattern, text)                                             |

* C++ 표준에는 std::regex_split이 없으므로, <regex> 헤더의 std::sregex_token_iterator 등을 사용하거나 Boost.Regex를 활용해야 합니다.


## C# 예제
```csharp
using System;
using System.Text.RegularExpressions;

class Program
{
    static void Main()
    {
        var text    = "Word, Excel  ,PowerPoint   , Outlook,OneNote";
        var pattern = @"\s*,\s*";
        string[] parts = Regex.Split(text, pattern);

        foreach (var part in parts)
            Console.WriteLine(part);

        // 출력:
        // Word
        // Excel
        // PowerPoint
        // Outlook
        // OneNote
    }
}
```


## C++ 예제
```cpp
#include <iostream>
#include <regex>
#include <string>
#include <vector>

int main() {
    std::string text    = "Word, Excel  ,PowerPoint   , Outlook,OneNote";
    std::regex pattern(R"(\s*,\s*)");

    // sregex_token_iterator를 사용한 분할
    std::sregex_token_iterator it(text.begin(), text.end(), pattern, -1);
    std::sregex_token_iterator end;

    std::vector<std::string> parts(it, end);
    for (auto& part : parts)
        std::cout << part << std::endl;

    // 출력:
    // Word
    // Excel
    // PowerPoint
    // Outlook
    // OneNote

    return 0;
}
```


## Java 예제
```java
public class Main {
    public static void main(String[] args) {
        String text    = "Word, Excel  ,PowerPoint   , Outlook,OneNote";
        String pattern = "\\s*,\\s*";

        // 방법 1: String.split
        String[] parts1 = text.split(pattern);
        for (String part : parts1) {
            System.out.println(part);
        }

        // 방법 2: Pattern.split
        // Pattern p = Pattern.compile(pattern);
        // String[] parts2 = p.split(text);
    }
}
```


## Python 예제
```python
import re

text    = "Word, Excel  ,PowerPoint   , Outlook,OneNote"
pattern = r"\s*,\s*"

parts = re.split(pattern, text)
for part in parts:
    print(part)

# 출력:
# Word
# Excel
# PowerPoint
# Outlook
# OneNote
```


## 💡 실전 팁
- split 후 불필요한 여분 공백 제거가 필요하면 각 요소에 Trim()(.NET/C#), strip()(Python), trim()(Java) 등을 적용하세요.
- Java/Python 모두 **최대 분할 횟수(limit)**를 인자로 받을 수 있어, 배열 길이를 제어할 때 유용합니다.
- C++ 표준 <regex>는 분할 전용 함수가 없으므로 std::sregex_token_iterator를 활용하거나 Boost.Regex의 regex_split을 사용하여 구현합니다.

---

