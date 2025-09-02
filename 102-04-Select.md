# C#의 LINQ Select 구문

## 🌳 C# (LINQ 사용)
```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace GrammarTest
{
    class Program
    {
        public static void Main(string[] args)
        {
            List<string> trees = new List<string>
            {
                "Oak", "Birth", "Beech", "Elem", "Hazel", "Maple"
            };

            var initials = trees.Select(tree => tree.Substring(0, 1));
            foreach (var str in initials)
            {
                Console.WriteLine(str);
            }
        }
    }
}
```


## 🧩 C++ (Standard Library 사용)
```cpp
#include <iostream>
#include <vector>
#include <string>

int main() {
    std::vector<std::string> trees = {
        "Oak", "Birth", "Beech", "Elem", "Hazel", "Maple"
    };

    for (const auto& tree : trees) {
        std::cout << tree.substr(0, 1) << std::endl;
    }

    return 0;
}
```


## ☕ Java (Stream API 사용)
```cpp
import java.util.Arrays;
import java.util.List;

public class GrammarTest {
    public static void main(String[] args) {
        List<String> trees = Arrays.asList("Oak", "Birth", "Beech", "Elem", "Hazel", "Maple");

        trees.stream()
             .map(tree -> tree.substring(0, 1))
             .forEach(System.out::println);
    }
}
```


### 🐍 Python (List Comprehension 사용)
```python
trees = ["Oak", "Birth", "Beech", "Elem", "Hazel", "Maple"]

initials = [tree[0] for tree in trees]
for char in initials:
    print(char)
```



### ✅ 출력 결과 (공통)
```
O
B
B
E
H
M
```

---

# Select 심화

- 문자열 길이가 5 이상인 것만 필터링
- 첫 글자를 대문자로, 나머지는 소문자로 변환
- 결과를 정렬


## 🌳 C# (LINQ + 필터링 + 변환 + 정렬)
```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace GrammarTest
{
    class Program
    {
        public static void Main(string[] args)
        {
            List<string> trees = new List<string>
            {
                "Oak", "Birth", "Beech", "Elem", "Hazel", "Maple"
            };

            var transformed = trees
                .Where(tree => tree.Length >= 5)
                .Select(tree => char.ToUpper(tree[0]) + tree.Substring(1).ToLower())
                .OrderBy(tree => tree);

            foreach (var tree in transformed)
            {
                Console.WriteLine(tree);
            }
        }
    }
}
```

### 🟢 출력:
```
Beech
Birth
Hazel
Maple
```


## 🧩 C++ (필터링 + 변환 + 정렬)
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <cctype>

std::string transform(const std::string& str) {
    if (str.empty()) return str;
    std::string result = str;
    result[0] = std::toupper(result[0]);
    for (size_t i = 1; i < result.size(); ++i)
        result[i] = std::tolower(result[i]);
    return result;
}

int main() {
    std::vector<std::string> trees = {
        "Oak", "Birth", "Beech", "Elem", "Hazel", "Maple"
    };

    std::vector<std::string> filtered;
    for (const auto& tree : trees) {
        if (tree.length() >= 5)
            filtered.push_back(transform(tree));
    }

    std::sort(filtered.begin(), filtered.end());

    for (const auto& tree : filtered) {
        std::cout << tree << std::endl;
    }

    return 0;
}

```


## ☕ Java (Stream + 필터링 + 변환 + 정렬)
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class GrammarTest {
    public static void main(String[] args) {
        List<String> trees = Arrays.asList("Oak", "Birth", "Beech", "Elem", "Hazel", "Maple");

        List<String> transformed = trees.stream()
            .filter(tree -> tree.length() >= 5)
            .map(tree -> tree.substring(0, 1).toUpperCase() + tree.substring(1).toLowerCase())
            .sorted()
            .collect(Collectors.toList());

        transformed.forEach(System.out::println);
    }
}
```


## 🐍 Python (List Comprehension + 필터링 + 변환 + 정렬)
```python
trees = ["Oak", "Birth", "Beech", "Elem", "Hazel", "Maple"]

transformed = sorted([
    tree.capitalize()
    for tree in trees
    if len(tree) >= 5
])

for tree in transformed:
    print(tree)

```


