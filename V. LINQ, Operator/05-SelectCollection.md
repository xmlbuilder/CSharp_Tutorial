# C#의 Select - New Collection
C#의 Select를 활용해 새로운 컬렉션을 만드는 예제를 기반
- 문자열 리스트를 소문자로 변환한 새 컬렉션 생성
- 숫자 리스트를 4자리 문자열로 포맷한 새 컬렉션 생성
각 언어별로 대응되는 코드를 아래에 정리했습니다.

## 🌳 C#
```csharp
var words = new List<string> {"Microsoft", "Apple", "Google", "Oracle", "Facebook"};
var lowers = words.Select(s => s.ToLower()).ToArray();

var numbers = new List<int>() {0, 20, 15, 40, 2};
var strings = numbers.Select(n => n.ToString("0000")).ToArray();
```

## 🧩 C++ (소문자 변환 + 숫자 포맷)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <iomanip>
#include <sstream>

int main() {
    std::vector<std::string> words = {"Microsoft", "Apple", "Google", "Oracle", "Facebook"};
    std::vector<std::string> lowers;

    for (const auto& word : words) {
        std::string lower = word;
        std::transform(lower.begin(), lower.end(), lower.begin(), ::tolower);
        lowers.push_back(lower);
    }

    for (const auto& str : lowers) {
        std::cout << str << std::endl;
    }

    std::vector<int> numbers = {0, 20, 15, 40, 2};
    std::vector<std::string> formatted;

    for (int num : numbers) {
        std::ostringstream oss;
        oss << std::setw(4) << std::setfill('0') << num;
        formatted.push_back(oss.str());
    }

    for (const auto& str : formatted) {
        std::cout << str << std::endl;
    }

    return 0;
}
```


## ☕ Java (Stream API 활용)
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class GrammarTest {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("Microsoft", "Apple", "Google", "Oracle", "Facebook");
        List<String> lowers = words.stream()
                                   .map(String::toLowerCase)
                                   .collect(Collectors.toList());

        lowers.forEach(System.out::println);

        List<Integer> numbers = Arrays.asList(0, 20, 15, 40, 2);
        List<String> formatted = numbers.stream()
                                        .map(n -> String.format("%04d", n))
                                        .collect(Collectors.toList());

        formatted.forEach(System.out::println);
    }
}
```


## 🐍 Python (List Comprehension)
```python
words = ["Microsoft", "Apple", "Google", "Oracle", "Facebook"]
lowers = [word.lower() for word in words]

for word in lowers:
    print(word)

numbers = [0, 20, 15, 40, 2]
formatted = [f"{num:04d}" for num in numbers]

for num_str in formatted:
    print(num_str)
```


✅ 출력 결과 (공통)
```
microsoft
apple
google
oracle
facebook
0000
0020
0015
0040
0002
```

### C# 전체소스
```csharp
namespace GrammarTest
{
    class Program
    {
        public static void Main(string[] args)
        {
            var words = new List<string> {"Microsoft", "Apple", "Google", "Oracle", "Facebook"};
            var lowers = words.Select(s => s.ToLower()).ToArray();
            foreach (var str in lowers)
            {
                Console.WriteLine(str); // microsoft apple google oracle facebook
            }
            var numbers = new List<int>() {0, 20, 15, 40, 2};
            var strings = numbers.Select(n => n.ToString("0000")).ToArray();
            foreach (var str in strings)
            {
                Console.WriteLine(str);
            }
            /*
            0000
            0020
            0015
            0040
            0002
             */
        }
    }
}

```

---

# C# Select 심화

- 컬렉션 간 매핑: 하나의 리스트를 기반으로 다른 리스트 생성

## 🔁 1. 컬렉션 간 매핑 (예: 이름 → 이메일)
Input: ["JungHwan", "Minji", "Taeyoung"]
Output: ["jung.hwan@email.com", "min.ji@email.com", "tae.young@email.com"]


### C#
```csharp
var names = new List<string> { "JungHwan", "Minji", "Taeyoung" };
var emails = names.Select(name =>
{
    var parts = System.Text.RegularExpressions.Regex.Split(name, "(?=[A-Z])");
    return string.Join(".", parts.Where(p => p != "")).ToLower() + "@email.com";
}).ToList();

emails.ForEach(Console.WriteLine);
```

### C++
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

std::string to_email(const std::string& name) {
    std::string email;
    for (size_t i = 0; i < name.size(); ++i) {
        if (i > 0 && isupper(name[i])) email += '.';
        email += tolower(name[i]);
    }
    return email + "@email.com";
}

int main() {
    std::vector<std::string> names = {"JungHwan", "Minji", "Taeyoung"};
    for (const auto& name : names)
        std::cout << to_email(name) << std::endl;
    return 0;
}
```

### Java
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class EmailMapper {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("JungHwan", "Minji", "Taeyoung");

        List<String> emails = names.stream()
            .map(name -> name.replaceAll("([A-Z])", ".$1").toLowerCase().replaceFirst("^\\.", "") + "@email.com")
            .collect(Collectors.toList());

        emails.forEach(System.out::println);
    }
}
```

## Python
```python
names = ["JungHwan", "Minji", "Taeyoung"]

def to_email(name):
    email = ""
    for i, c in enumerate(name):
        if i > 0 and c.isupper():
            email += "."
        email += c.lower()
    return email + "@email.com"

emails = [to_email(name) for name in names]
for email in emails:
    print(email)

```

---



