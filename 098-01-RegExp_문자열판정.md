# RegularExpression 문장열 판정

정규 표현식을 사용해 "List<string>" 같은 패턴을 판별하는 예제

## C#
```csharp
using System;
using System.Text.RegularExpressions;

namespace  GrammarTest
{
    class Program
    {
        public static void Main(String[] args)
        {
            var text = "private List<string> results = new List<string>();";
            bool isMatch = Regex.IsMatch(text, @"List<\w+>");
            if (isMatch)
            {
                Console.WriteLine("success");
            }
            else
            {
                Console.WriteLine("failed");
            }
            //success

            var regEx = new Regex(@"List<\w+>");
            isMatch = regEx.IsMatch(text);
            if (isMatch)
            {
                Console.WriteLine("success");
            }
            else
            {
                Console.WriteLine("failed");
            }
            //success
        }
        
    }
}

```


## ✅ C++ (C++11 이상, <regex> 사용)
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text = "private List<string> results = new List<string>();";
    std::regex pattern("List<\\w+>");
    
    if (std::regex_search(text, pattern)) {
        std::cout << "success\n";
    } else {
        std::cout << "failed\n";
    }

    return 0;
}
```


- std::regex_search는 문자열 내에서 패턴이 어디든 존재하는지 확인합니다.
- \\w+는 단어 문자(알파벳, 숫자, _)를 의미합니다.

## ✅ Java
```java
import java.util.regex.*;

public class GrammarTest {
    public static void main(String[] args) {
        String text = "private List<string> results = new List<string>();";
        Pattern pattern = Pattern.compile("List<\\w+>");
        Matcher matcher = pattern.matcher(text);

        if (matcher.find()) {
            System.out.println("success");
        } else {
            System.out.println("failed");
        }
    }
}
```

- Pattern.compile()로 정규식 객체 생성
- matcher.find()는 문자열 전체에서 패턴을 찾습니다

## ✅ Python
```python
import re

text = "private List<string> results = new List<string>();"
pattern = r"List<\w+>"

if re.search(pattern, text):
    print("success")
else:
    print("failed")
```

- re.search()는 문자열 전체에서 패턴을 찾습니다
- r"..."는 raw string으로 백슬래시를 이스케이프하지 않게 해줍니다

## 🧪 요약 비교
| 언어     | 정규식 객체 생성 방식       | 문자열 검사 함수         | 결과 출력 방식         | 비고                         |
|----------|-----------------------------|---------------------------|------------------------|------------------------------|
| **C#**   | `new Regex("패턴")`         | `Regex.IsMatch(text)`     | `Console.WriteLine(...)` | `System.Text.RegularExpressions` 필요 |
| **C++**  | `std::regex("패턴")`        | `std::regex_search(text, pattern)` | `std::cout << ...`     | `<regex>` 헤더 필요           |
| **Java** | `Pattern.compile("패턴")`   | `matcher.find()`          | `System.out.println(...)` | `java.util.regex` 패키지 사용 |
| **Python** | `re.compile("패턴")` 또는 직접 사용 | `re.search(pattern, text)` | `print(...)`            | `re` 모듈 기본 포함           |

---


