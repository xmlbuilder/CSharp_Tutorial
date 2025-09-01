# Regular Expression (?i)
C#에서 (?i)를 사용해 정규식 내에서 대소문자 구분 없이 매칭하는 기능은 매우 유용. 
이 기능은 **정규식 엔진의 "inline modifier"**라고 불린다.

## ✅ C#
```csharp
/*
(?i)  ->  앞 부분에 (?i) 라는 옵션을 넣어주면 대소문자를 구분하지 않음
*/


using System;
using System.Text.RegularExpressions;
namespace  GrammarTest
{
    
    class Program
    {
        public static void Main(String[] args)
        {
            var text = "private list<string> results = new list<string>();";
            bool isMatch = Regex.IsMatch(text, @"(?i)List<\w+>");
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


```csharp
Regex.IsMatch(text, @"(?i)List<\w+>");
```

- (?i)는 대소문자 구분 없이 "list<string>", "List<string>" 모두 매칭

## ☕ Java
```java
Java도 (?i)를 지원합니다. 또는 Pattern.CASE_INSENSITIVE 옵션을 사용할 수도 있어요.
import java.util.regex.*;

public class Main {
    public static void main(String[] args) {
        String text = "private list<string> results = new list<string>();";

        // 방법 1: (?i) 사용
        boolean isMatch1 = Pattern.compile("(?i)List<\\w+>").matcher(text).find();

        // 방법 2: 옵션 사용
        boolean isMatch2 = Pattern.compile("List<\\w+>", Pattern.CASE_INSENSITIVE).matcher(text).find();

        System.out.println(isMatch1 ? "success" : "failed");  // success
        System.out.println(isMatch2 ? "success" : "failed");  // success
    }
}
```


## 🐍 Python
Python도 (?i)를 지원하며, re.IGNORECASE 옵션도 자주 사용됩니다.
```python
import re

text = "private list<string> results = new list<string>();"

# 방법 1: (?i) 사용
is_match1 = re.search(r"(?i)List<\w+>", text)

# 방법 2: 옵션 사용
is_match2 = re.search(r"List<\w+>", text, re.IGNORECASE)

print("success" if is_match1 else "failed")  # success
print("success" if is_match2 else "failed")  # success
```


## 💻 C++

C++의 <regex>도 대소문자 무시 기능을 지원하지만, inline modifier (?i)는 지원하지 않습니다. 대신 std::regex_constants::icase 옵션을 사용해야 해요.
```cpp
#include <iostream>
#include <regex>
#include <string>

int main() {
    std::string text = "private list<string> results = new list<string>();";
    std::regex pattern("List<\\w+>", std::regex_constants::icase);  // 대소문자 무시

    if (std::regex_search(text, pattern)) {
        std::cout << "success" << std::endl;
    } else {
        std::cout << "failed" << std::endl;
    }
    // success
}
```

## 📊 정규식 대소문자 무시 옵션 비교

| 언어   | inline modifier | 옵션 방식                     | 비고                         |
|--------|------------------|-------------------------------|------------------------------|
| C#     | `(?i)`           | `RegexOptions.IgnoreCase`     | 둘 다 사용 가능              |
| Java   | `(?i)`           | `Pattern.CASE_INSENSITIVE`    | 둘 다 사용 가능              |
| Python | `(?i)`           | `re.IGNORECASE`               | 둘 다 사용 가능              |
| C++    | ❌               | `std::regex_constants::icase` | inline modifier는 미지원     |


---

