# 🌼 Dictionary 요소 추가/조회/삭제 비교 (C#, Java, C++, Python)
Key-Value 기반의 연관 배열에서 요소를 추가하고, 조회하고, 삭제하는 방법을 각 언어별로 정리합니다.


## 🧠 C# - Dictionary<TKey, TValue>
```cpp
using System;
using System.Collections.Generic;

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

            // 요소 추가
            flowerDict["violet"] = 600;         // 존재하지 않으면 추가
            flowerDict.Add("sample", 600);      // 명시적 추가

            // 값 조회
            int price = flowerDict["rose"];
            Console.WriteLine(price);           // 출력: 500

            if (flowerDict.ContainsKey("pansy"))
            {
                Console.WriteLine(flowerDict["pansy"]); // 출력: 300
            }

            // 요소 삭제
            flowerDict.Remove("pansy");

            if (!flowerDict.ContainsKey("pansy"))
            {
                Console.WriteLine("Not found"); // 출력: Not found
            }
        }
    }
}
```


## 💻 C++ - std::map<Key, Value>
```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<std::string, int> flowerDict = {
        {"sunflower", 400},
        {"pansy", 300},
        {"tulip", 350},
        {"rose", 500},
        {"dahlia", 450},
    };

    // 요소 추가
    flowerDict["violet"] = 600; // 존재하지 않으면 추가
    flowerDict.insert(std::pair<std::string, int>("sample", 600));

    // 값 조회
    std::cout << flowerDict["rose"] << std::endl; // 출력: 500

    auto itrfind = flowerDict.find("pansy");
    if (itrfind != flowerDict.end()) {
        std::cout << itrfind->second << std::endl; // 출력: 300
        flowerDict.erase(itrfind);                 // 삭제
    }

    itrfind = flowerDict.find("pansy");
    if (itrfind == flowerDict.end()) {
        std::cout << "Not found" << std::endl;     // 출력: Not found
    }

    return 0;
}
```


## ☕ Java - HashMap<K, V>
```java
import java.util.HashMap;

public class FlowerMap {
    public static void main(String[] args) {
        HashMap<String, Integer> flowerMap = new HashMap<>();

        // 요소 추가
        flowerMap.put("sunflower", 400);
        flowerMap.put("pansy", 300);
        flowerMap.put("tulip", 350);
        flowerMap.put("rose", 500);
        flowerMap.put("dahlia", 450);
        flowerMap.put("violet", 600);
        flowerMap.put("sample", 600);

        // 값 조회
        int price = flowerMap.get("rose");
        System.out.println(price); // 출력: 500

        if (flowerMap.containsKey("pansy")) {
            System.out.println(flowerMap.get("pansy")); // 출력: 300
        }

        // 요소 삭제
        flowerMap.remove("pansy");

        if (!flowerMap.containsKey("pansy")) {
            System.out.println("Not found"); // 출력: Not found
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

# 요소 추가
flower_dict["violet"] = 600
flower_dict["sample"] = 600

# 값 조회
print(flower_dict["rose"])  # 출력: 500

if "pansy" in flower_dict:
    print(flower_dict["pansy"])  # 출력: 300

# 요소 삭제
del flower_dict["pansy"]

if "pansy" not in flower_dict:
    print("Not found")  # 출력: Not found
```


## 📊 기능별 비교 요약
| 언어     | 요소 추가 방식                  | 값 조회 방식             | 삭제 방식                      | 존재 확인 방식               |
|----------|----------------------------------|---------------------------|--------------------------------|------------------------------|
| C#       | `dict["key"] = val`, `Add()`     | `dict["key"]`             | `Remove("key")`                | `ContainsKey("key")`         |
| C++      | `map["key"] = val`, `insert()`   | `map["key"]`, `find()`    | `erase(iterator)`              | `find() != end()`            |
| Java     | `put(key, val)`                  | `get(key)`                | `remove(key)`                  | `containsKey(key)`           |
| Python   | `dict["key"] = val`              | `dict["key"]`             | `del dict["key"]`              | `"key" in dict`              |

---



