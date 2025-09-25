# 🌐 Dictionary 사용법 정리 (C#, Java, C++, Python)
Dictionary는 Key-Value 쌍으로 데이터를 저장하는 자료구조입니다. 각 언어에서 이를 어떻게 사용하는지 비교해봅니다.


## 📌 개념 설명
- Key: 값을 찾기 위한 식별자
- Value: Key에 대응되는 실제 데이터
- 연관 배열 (Associative Array): Key-Value 쌍으로 구성된 자료구조
C#, Java, C++, Python 모두 Dictionary 또는 Map을 통해 연관 배열을 구현합니다.

## 🧠 C# - Dictionary<TKey, TValue>
```csharp
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

            Console.WriteLine(flowerDict["dahlia"]); // 출력: 450
        }
    }
}
```

- Dictionary<TKey, TValue> 클래스 사용
- Key는 중복 불가
- flowerDict["key"]로 값 접근

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

        System.out.println(flowerMap.get("dahlia")); // 출력: 450
    }
}
```

- HashMap<K, V> 사용
- put()으로 값 추가
- get(key)로 값 조회

## 💻 C++ - std::map<Key, Value>
```cpp
#include <iostream>
#include <map>
#include <string>

int main() {
    std::map<std::string, int> maps = {
        {"sunflower", 400},
        {"pansy", 300},
        {"tulip", 350},
        {"rose", 500},
        {"dahlia", 450},
    };

    std::cout << maps["dahlia"] << std::endl; // 출력: 450
    return 0;
}
```


- std::map<Key, Value> 사용
- 자동 정렬됨 (Key 기준)
- maps[key]로 값 접근

## 🐍 Python - dict
```python
flower_dict = {
    "sunflower": 400,
    "pansy": 300,
    "tulip": 350,
    "rose": 500,
    "dahlia": 450
}

print(flower_dict["dahlia"])  # 출력: 450
```

- dict 타입 사용
- {key: value} 형식
- dict[key]로 값 조회


## 📊 비교 요약

| 언어     | 자료형           | 값 추가 방식        | 값 조회 방식       | Key 중복 허용 |
|----------|------------------|----------------------|---------------------|----------------|
| C#       | Dictionary        | `dict["key"] = val`  | `dict["key"]`       | ❌             |
| Java     | HashMap           | `put(key, val)`      | `get(key)`          | ❌             |
| C++      | std::map          | `{key, val}`         | `map[key]`          | ❌             |
| Python   | dict              | `{key: val}`         | `dict[key]`         | ❌             |

---


