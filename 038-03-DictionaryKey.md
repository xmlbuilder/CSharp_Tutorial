# 🔍 Dictionary Key/Value 순회 비교 (C#, Java, C++, Python)
Dictionary 또는 Map에서 Key와 Value를 어떻게 순회하고 추출하는지 각 언어별로 정리합니다.


## 🧠 C# - Dictionary<TKey, TValue>
```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace GrammarTest
{
    public class GrammarTest
    {
        public static void Main(String[] args)
        {
            var flowerDict = new Dictionary<string, int>()
            {
                ["sunflower"] = 400,
                ["pansy"] = 300,
                ["tulip"] = 350,
                ["rose"] = 500,
                ["dahlia"] = 450,
            };

            // 🔑 Keys만 출력
            foreach (var key in flowerDict.Keys)
            {
                Console.WriteLine(key);
            }

            // 🔁 List를 Dictionary로 변환 (Key=Value)
            var list = new List<int>() {1, 2, 3, 4, 5, 6, 7, 8, 9};
            var newDict = list.ToDictionary(s => s);
            foreach (KeyValuePair<int, int> var in newDict)
            {
                Console.WriteLine($"{var.Key} = {var.Value}");
            }
        }
    }
}
```


## 💻 C++20 - std::map + std::views::keys / values
```cpp
#include <iostream>
#include <map>
#include <ranges> // C++20부터 필요

int main() {
    std::map<std::string, int> flowerDict = {
        {"sunflower", 400},
        {"pansy", 300},
        {"tulip", 350},
        {"rose", 500},
        {"dahlia", 450},
    };

    // 🔑 Keys만 출력
    auto keys = std::views::keys(flowerDict);
    for (auto key : keys) {
        std::cout << key << std::endl;
    }

    // 🔢 Values만 출력
    auto vals = std::views::values(flowerDict);
    for (auto val : vals) {
        std::cout << val << std::endl;
    }

    return 0;
}
```

✅ std::views::keys와 std::views::values는 C++20부터 사용 가능


## ☕ Java - HashMap<K, V>
```java
import java.util.HashMap;

public class FlowerMap {
    public static void main(String[] args) {
        HashMap<String, Integer> flowerMap = new HashMap<>();
        flowerMap.put("sunflower", 400);
        flowerMap.put("pansy", 300);
        flowerMap.put("tulip", 350);
        flowerMap.put("rose", 500);
        flowerMap.put("dahlia", 450);

        // 🔑 Keys만 출력
        for (String key : flowerMap.keySet()) {
            System.out.println(key);
        }

        // 🔢 Values만 출력
        for (Integer val : flowerMap.values()) {
            System.out.println(val);
        }

        // 🔁 Key-Value 쌍 출력
        for (var entry : flowerMap.entrySet()) {
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }
    }
}
```


## 🐍 Python - dict
```python
flower_dict = {
    "sunflower": 400,
    "pansy": 300,
    "tulip": 350,
    "rose": 500,
    "dahlia": 450
}
```
# 🔑 Keys만 출력
for key in flower_dict.keys():
    print(key)

# 🔢 Values만 출력
for val in flower_dict.values():
    print(val)

# 🔁 Key-Value 쌍 출력
for key, val in flower_dict.items():
    print(f"{key} = {val}")



## 📊 기능별 비교 요약
| 언어     | Keys 순회 방식                  | Values 순회 방식               | Key-Value 쌍 순회 방식             |
|----------|----------------------------------|--------------------------------|------------------------------------|
| C#       | `foreach (var k in dict.Keys)`   | `foreach (var v in dict.Values)` | `foreach (var kv in dict)`         |
| C++20    | `std::views::keys(map)`          | `std::views::values(map)`       | `for (auto& [k,v] : map)` (C++17+) |
| Java     | `map.keySet()`                   | `map.values()`                  | `map.entrySet()`                   |
| Python   | `dict.keys()`                    | `dict.values()`                 | `dict.items()`                     |

---



