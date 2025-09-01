# Regular Expression Replace

정규 표현식을 활용한 문자열 치환(Replace) 작업

## 🧪 언어별 정규식 문자열 치환 요약
| 언어     | 정규식 치환 함수         | 정규식 라이브러리         | 예시 함수 호출 방식                         |
|----------|--------------------------|----------------------------|---------------------------------------------|
| **C#**   | `Regex.Replace()`        | `System.Text.RegularExpressions` | `Regex.Replace(text, pattern, replacement)` |
| **C++**  | `std::regex_replace()`   | `<regex>`                  | `std::regex_replace(text, pattern, replacement)` |
| **Java** | `String.replaceAll()`    | `java.util.regex.Pattern` | `text.replaceAll(pattern, replacement)`     |
| **Python** | `re.sub()`             | `re`                       | `re.sub(pattern, replacement, text)`        |

## ✅ C# 예제
```csharp
using System.Text.RegularExpressions;
namespace GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            var text1 = "C# 공부를 쪼끔씩 진행해 보자";
            var pattern1 = @"쪼끔씩|쪼금씩|쬐끔씩";
            var replaced1 = Regex.Replace(text1, pattern1, "조금씩");
            Console.WriteLine(replaced1);
            //C# 공부를 조금씩 진행해 보자

            var text2 = "Word,  PowerPoint   ,Outlook,OneNote";
            var pattern2 = @"\s*,\s*";
            var replaced2 = Regex.Replace(text2, pattern2, ", ");
            Console.WriteLine(replaced2);
            //Word, PowerPoint, Outlook, OneNote
            
            var text3 = "foo.htm bar.html baz.htm";
            var pattern3 = @"\.(htm)\b";
            var replaced3 = Regex.Replace(text3, pattern3, ".html");
            Console.WriteLine(replaced3);
            //foo.html bar.html baz.html
        }
    }
}
```



## ✅ C++ 예제
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text = "foo.htm bar.html baz.htm";
    std::regex pattern(R"(\.(htm)\b)");
    std::string replaced = std::regex_replace(text, pattern, ".html");
    std::cout << replaced << std::endl;
    // 출력: foo.html bar.html baz.html
}
```


## ✅ Java 예제
```java
public class RegexReplace {
    public static void main(String[] args) {
        String text = "Word,  PowerPoint   ,Outlook,OneNote";
        String pattern = "\\s*,\\s*";
        String replaced = text.replaceAll(pattern, ", ");
        System.out.println(replaced);
        // 출력: Word, PowerPoint, Outlook, OneNote
    }
}
```


## ✅ Python 예제
```python
import re

text = "C# 공부를 쪼끔씩 진행해 보자"
pattern = r"쪼끔씩|쪼금씩|쬐끔씩"
replaced = re.sub(pattern, "조금씩", text)
print(replaced)
# 출력: C# 공부를 조금씩 진행해 보자
```



## 💡 실전 팁
- 그룹 활용: (\w+) 같은 그룹을 사용하면 치환 시 $1, \1 등으로 참조 가능 (언어마다 문법 다름)
- 조건부 치환: 일부 언어에서는 람다나 함수형 치환도 가능 (예: Python의 re.sub에서 lambda 사용)
- 공백 정리: \s*,\s* 같은 패턴은 리스트 정리에 매우 유용

---
