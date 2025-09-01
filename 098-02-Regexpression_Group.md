# RegularExpression Grup

정규 표현식에서 **그룹(Group)**을 활용해 특정 부분을 추출하는 방식


## 🧪 정규 표현식 그룹 비교 요약
| 언어     | 정규식 객체 생성 방식       | 그룹 접근 방식               | 반복 매칭 방식             | 비고                         |
|----------|-----------------------------|------------------------------|----------------------------|------------------------------|
| **C#**   | `new Regex("패턴")`         | `match.Groups[i]`            | `regex.Matches(text)`      | `System.Text.RegularExpressions` 필요 |
| **C++**  | `std::regex("패턴")`        | `match.str(i)`               | `std::sregex_iterator`     | `<regex>` 헤더 필요           |
| **Java** | `Pattern.compile("패턴")`   | `matcher.group(i)`           | `matcher.find()` 반복      | `java.util.regex` 패키지 사용 |
| **Python** | `re.compile("패턴")` 또는 직접 사용 | `match.group(i)` 또는 `groups()` | `re.finditer()` 또는 `re.findall()` | `re` 모듈 기본 포함           |



## 🔍 각 언어별 예제
### C#
```csharp
using System;
using System.Text.RegularExpressions;
namespace  GrammarTest
{
    
    class Program
    {
        public static void Main(String[] args)
        {
            string str = "서울시 강남구 역삼동 강남아파트";
            Regex regex = new Regex("강남");
            Match m = regex.Match(str);
            if (m.Success)
            {
                Console.WriteLine($"{m.Index} {m.Value}");
                //4 강남
            }

            m = regex.Match(str);
            while (m.Success)
            {
                Console.WriteLine($"{m.Index} {m.Value}");
                m = m.NextMatch();
            }
            /*
             4 강남
             12 강남
             */
            MatchCollection mc = regex.Matches(str);
            foreach (Match m1 in mc)
            {
                Console.WriteLine($"{m1.Index} {m1.Value}");
    
            }
            /*
             4 강남
             12 강남
             */            
        }
    }
}


using System.Text.RegularExpressions;

namespace  GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            string str = "강남빌라 역삼아파트 서초APT";
            Regex regex = new Regex(@"(아파트|APT)");
            MatchCollection mc = regex.Matches(str);
            foreach (Match m in mc)
            {
                Group g = m.Groups[1];
                Console.WriteLine($"{g.Index} {g.Value}");
            }
            // 7 아파트
            // 13 APT
            
            string str1 = "<ul><li>홈페이지</li><li>주문메뉴</li></ul>";
            Regex regex1 = new Regex(@"<li>(\w+)</li>");
            MatchCollection mc1 = regex1.Matches(str1);
            foreach (Match m1 in mc1)
            {
                Group g = m1.Groups[1];
                Console.WriteLine($"{g.Index} {g.Value}");
            }
            /*
             *  8 홈페이지
                21 주문메뉴
             */
            
            string str2 = "02-632-5432; 032-645-7361";
            Regex regex2 = new Regex(@"(\d+)-(\d+-\d+)");
            MatchCollection mc3 = regex2.Matches(str2);
            foreach (Match m2 in mc3)
            {
                for (int i = 1; i < m2.Groups.Count; i++)
                {
                    Group g = m2.Groups[i];
                    Console.WriteLine($"{g.Index} : {g.Value}");
                    
                }   
            }
            /*
            0 : 02
            3 : 632-5432
            13 : 032
            17 : 645-7361
             */
            
            string str3 = "02-632-5432; 032-645-7361";
            Regex regex3 = new Regex(@"(\d+-\d+-\d+)");
            MatchCollection mc4 = regex3.Matches(str2);
            foreach (Match m3 in mc4)
            {
                for (int i = 1; i < m3.Groups.Count; i++)
                {
                    Group g = m3.Groups[i];
                    Console.WriteLine($"{g.Index} : {g.Value}");
                }   
            }
            /*
             0 : 02-632-5432
             13 : 032-645-7361
             */
        }
        
    }
}
```

✅ C++ (C++11 이상)
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text = "02-632-5432; 032-645-7361";
    std::regex pattern(R"((\d+)-(\d+-\d+))");
    auto begin = std::sregex_iterator(text.begin(), text.end(), pattern);
    auto end = std::sregex_iterator();

    for (auto it = begin; it != end; ++it) {
        std::smatch match = *it;
        std::cout << match.position(1) << " : " << match.str(1) << std::endl;
        std::cout << match.position(2) << " : " << match.str(2) << std::endl;
    }
}

```

✅ Java
```java
import java.util.regex.*;

public class RegexGroupTest {
    public static void main(String[] args) {
        String text = "02-632-5432; 032-645-7361";
        Pattern pattern = Pattern.compile("(\\d+)-(\\d+-\\d+)");
        Matcher matcher = pattern.matcher(text);

        while (matcher.find()) {
            System.out.println(matcher.start(1) + " : " + matcher.group(1));
            System.out.println(matcher.start(2) + " : " + matcher.group(2));
        }
    }
}
```


✅ Python
```python
import re

text = "02-632-5432; 032-645-7361"
pattern = r"(\d+)-(\d+-\d+)"

for match in re.finditer(pattern, text):
    print(f"{match.start(1)} : {match.group(1)}")
    print(f"{match.start(2)} : {match.group(2)}")

```

## ✨ 그룹이란?
- () 괄호로 묶은 부분이 그룹입니다.
- 그룹은 부분 문자열 추출에 사용되며, 여러 개일 경우 인덱스로 접근합니다.

---

