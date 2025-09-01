# 정규식 최장 일치
정규식에서 **최장 일치(Greedy Matching)**는 핵심적인 개념 중 하나입니다. 
이 원리를 이해하면 정규식이 왜 특정 방식으로 동작하는지, 그리고 어떻게 제어할 수 있는지를 명확히 알 수 있어요.


## 🔍 정규식의 최장 일치(Greedy Matching)란?
정규식에서 대부분의 수량자(*, +, {n,m} 등)는 기본적으로 최대한 많이 매칭하려고 합니다. 이를 **Greedy(탐욕적)**이라고 부르죠.
예를 들어, 다음 문자열이 있다고 할 때:
"<tag>content</tag>"


정규식 <.*>는 가능한 한 많은 문자를 포함하려고 해서 결과는:
<**tag>content</tag**>


반면 <.*?>처럼 ?를 붙이면 **최소 일치(Lazy Matching)**가 되어:
<**tag**>


처럼 짧게 매칭됩니다.

## 🧪 C# 예제 분석
```csharp
using System.Text.RegularExpressions;

namespace GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            var text = "<person><name>JungHwan Jeong</name><age>22</age></person>";
            var pattern = @"<(\w+?)>";
            var matches = Regex.Matches(text, pattern);
            foreach (Match march in matches)
            {
                Console.WriteLine(march.Groups[1].Value);
            }
            /*
            person
            name
            age
             */

            var text1 = "<person><name>JungHwan Jeong</name><age>22</age></person>";
            var pattern1 = @"<(\w[^>]+)>";
            var matches1 = Regex.Matches(text1, pattern1);
            foreach (Match march in matches1)
            {
                Console.WriteLine(march.Groups[1].Value);
            }
            /*
            person
            name
            age
             */
        }
    }
}


```

```csharp
var pattern = @"<(\w+?)>";  // Lazy: 최소 일치
var pattern1 = @"<(\w[^>]+)>";  // Greedy: 최장 일치
```

- \w+?는 최소한의 문자만 매칭 → <name>에서 name만 추출
- \w[^>]+는 >가 나오기 전까지 가능한 많은 문자 매칭 → 여전히 name, age 등 추출되지만, 구조상 Greedy
C#의 Regex 클래스는 .NET의 정규식 엔진을 사용하며, Perl 스타일을 따릅니다. 따라서 ?를 붙이면 Lazy로 동작하고, 기본은 Greedy입니다.

## 🧠 언어별 정규식 기능 비교

| 언어     | 정규식 엔진 스타일 | Lazy 매칭 지원 | 주요 클래스/모듈                        |
|----------|--------------------|----------------|----------------------------------------|
| C#       | Perl 스타일 (.NET) | ✅              | `System.Text.RegularExpressions.Regex` |
| C++      | ECMAScript 스타일  | ✅              | `<regex>` / `std::regex`               |
| Java     | Perl 스타일        | ✅              | `java.util.regex.Pattern`              |
| Python   | Perl 스타일        | ✅              | `re`                                   |


모든 언어에서 기본 수량자는 Greedy이며, ?를 붙이면 Lazy로 전환됩니다.

☕ Java 예시
```java
String text = "<person><name>JungHwan</name><age>22</age></person>";
Pattern pattern = Pattern.compile("<(\\w+?)>");
Matcher matcher = pattern.matcher(text);
while (matcher.find()) {
    System.out.println(matcher.group(1));
}
```

- \w+? → Lazy
- \w+ → Greedy

🐍 Python 예시
```python
import re

text = "<person><name>JungHwan</name><age>22</age></person>"
pattern = r"<(\w+?)>"  # Lazy
matches = re.findall(pattern, text)
print(matches)  # ['person', 'name', 'age']
```

Python도 동일하게 ?를 붙이면 Lazy로 작동합니다.

## 💡 정리: Greedy vs Lazy 정규식

| 패턴      | 설명             | 매칭 결과 (`<tag>content</tag>`) |
|-----------|------------------|----------------------------------|
| `<.*>`    | Greedy (최장 일치) | `<tag>content</tag>` 전체 매칭   |
| `<.*?>`   | Lazy (최소 일치)  | `<tag>`만 매칭                   |



- *는 가능한 많은 문자를 포함하려는 탐욕적(Greedy) 수량자입니다.
- *?는 가능한 적은 문자를 포함하려는 게으른(Lazy) 수량자입니다.


---



#  최적의 예제: HTML 태그 안에서 텍스트 추출
## 📄 테스트 문자열
<div>첫 번째</div><div>두 번째</div>

### 🧪 Greedy 패턴: <div>.*</div>
```csharp
var text = "<div>첫 번째</div><div>두 번째</div>";
var pattern = @"<div>.*</div>";  // Greedy
var match = Regex.Match(text, pattern);
Console.WriteLine(match.Value);
```

### 🔍 결과:
```
<div>첫 번째</div><div>두 번째</div>
```

- .*는 가능한 많은 문자를 포함하려고 하므로 전체 문자열을 한 번에 매칭합니다.

### 🧪 Lazy 패턴: <div>.*?</div>
```csharp
var text = "<div>첫 번째</div><div>두 번째</div>";
var pattern = @"<div>.*?</div>";  // Lazy
var matches = Regex.Matches(text, pattern);
foreach (Match match in matches)
{
    Console.WriteLine(match.Value);
}
```            

### 🔍 결과:
```
<div>첫 번째</div>
<div>두 번째</div>
```

- .*?는 가능한 적은 문자를 포함하려고 하므로 각각의 <div> 태그 쌍을 따로 매칭합니다.

## 💡 Greedy vs Lazy 정규식 비교

| 패턴           | 설명               | 매칭 결과                                 |
|----------------|--------------------|-------------------------------------------|
| `<div>.*</div>`  | Greedy (최장 일치) | `<div>첫 번째</div><div>두 번째</div>`     |
| `<div>.*?</div>` | Lazy (최소 일치)  | `<div>첫 번째</div>`, `<div>두 번째</div>` |

이 예제는 HTML/XML 태그처럼 반복되는 구조에서 Greedy와 Lazy의 차이를 가장 명확하게 보여줍니다. 
---
