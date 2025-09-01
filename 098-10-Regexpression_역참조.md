# 🔁 정규식 역참조란?
정규식에서 괄호 ()로 감싼 부분은 캡처 그룹이 됩니다. 이후 \1, \2 등으로 해당 그룹을 재참조할 수 있어요.

예시:
- 패턴 (\w)\1은 "같은 문자 두 번 반복"을 의미합니다.
예: "빵빵", "하하", "ㅋㅋ" 등
- 패턴 \b(\w)\w\1\b은 "첫 글자와 마지막 글자가 같은 3글자 단어"를 의미합니다.
예: "pop", "gig", "dad" 등

## ✅ C# 예제
```csharp
using System.Text.RegularExpressions;

namespace GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            var text1 = "도로를 지나가는 차들이 뛰뛰하고 경적을 울리면 반대쪽 차들이 빵빵하고 울렸다.";
            var pattern1 = @"(\w)\1";
            var matches1 = Regex.Matches(text1, pattern1);
            foreach (Match match in matches1)
            {
                Console.WriteLine(match.Value);
            }
            /*
             뛰뛰
             빵빵
             */

            var text2 = "기러기 펠리컨 pop";
            var pattern2 = @"\b(\w)\w\1\b";
            var matches2 = Regex.Matches(text2, pattern2);
            foreach (Match match in matches2)
            {
                Console.WriteLine(match.Value);
            }
            /*
             기러기
             pop
             */
        }
    }
}

```

var pattern1 = @"(\w)\1";           // 같은 문자 두 번 반복
var pattern2 = @"\b(\w)\w\1\b";     // 첫글자=끝글자, 3글자 단어


- \1은 첫 번째 캡처 그룹을 재참조
- \b는 단어 경계를 의미


## 🔍 C++에서 역참조 사용 예제
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text1 = "도로를 지나가는 차들이 뛰뛰하고 경적을 울리면 반대쪽 차들이 빵빵하고 울렸다.";
    std::regex pattern1(R"((\w)\1)");
    auto words_begin = std::sregex_iterator(text1.begin(), text1.end(), pattern1);
    auto words_end = std::sregex_iterator();

    for (std::sregex_iterator i = words_begin; i != words_end; ++i) {
        std::cout << i->str() << std::endl;
    }

    std::string text2 = "기러기 펠리컨 pop";
    std::regex pattern2(R"(\b(\w)\w\1\b)");
    auto words2_begin = std::sregex_iterator(text2.begin(), text2.end(), pattern2);
    auto words2_end = std::sregex_iterator();

    for (std::sregex_iterator i = words2_begin; i != words2_end; ++i) {
        std::cout << i->str() << std::endl;
    }

    return 0;
}
```
### ✅ 출력 결과:
```
뛰뛰  
빵빵  
pop  
```

### ⚠️ 주의할 점
- std::regex는 일부 정규식 엔진보다 기능이 제한적입니다. 특히 Unicode 처리나 복잡한 역참조는 잘 안 될 수 있어요.
- std::regex_replace()에서는 \1 같은 역참조를 치환 문자열에서 사용할 수 없습니다.


## ☕ Java 예제
```java
import java.util.regex.*;

public class Main {
    public static void main(String[] args) {
        String text1 = "도로를 지나가는 차들이 뛰뛰하고 경적을 울리면 반대쪽 차들이 빵빵하고 울렸다.";
        Pattern pattern1 = Pattern.compile("(\\w)\\1");
        Matcher matcher1 = pattern1.matcher(text1);
        while (matcher1.find()) {
            System.out.println(matcher1.group());
        }

        String text2 = "기러기 펠리컨 pop";
        Pattern pattern2 = Pattern.compile("\\b(\\w)\\w\\1\\b");
        Matcher matcher2 = pattern2.matcher(text2);
        while (matcher2.find()) {
            System.out.println(matcher2.group());
        }
    }
}
```


- Java에서는 \\로 이스케이프를 두 번 써야 함
- group()으로 매칭된 전체 문자열 출력

## 🐍 Python 예제
```python
import re

text1 = "도로를 지나가는 차들이 뛰뛰하고 경적을 울리면 반대쪽 차들이 빵빵하고 울렸다."
pattern1 = r"(\w)\1"
matches1 = re.findall(pattern1, text1)
print([m + m for m in matches1])  # ['뛰뛰', '빵빵']

text2 = "기러기 펠리컨 pop"
pattern2 = r"\b(\w)\w\1\b"
matches2 = re.findall(pattern2, text2)
print([m[0] + m[1] + m[0] for m in re.findall(r"\b(\w)(\w)\1\b", text2)])  # ['pop']
```


- re.findall()은 그룹만 반환하므로 재조합 필요
- (\w)(\w)\1로 두 글자 캡처 후 첫 글자와 마지막 글자 비교

## 🔁 정규식 역참조 비교

| 언어     | 역참조 문법 | 예시 패턴         | 설명                          |
|----------|-------------|-------------------|-------------------------------|
| C#       | `\1`, `\2`  | `(\w)\1`          | 같은 문자 두 번 반복          |
| Java     | `\1`, `\2`  | `(\\w)\\1`        | 이스케이프 두 번 필요         |
| Python   | `\1`, `\2`  | `(\w)\1`          | 그룹만 반환 시 재조합 필요    |


---
