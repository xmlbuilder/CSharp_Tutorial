# 📘 C# 실전 Dictionary 활용 예제

```csharp
class Abbreviation
{
    private Dictionary<string, string> _dict;

    public Abbreviation()
    {
        List<string> lines = new List<string>
        {
            "Sample1=Test1",
            "Sample2=Test2",
            "Sample3=Test3"
        };

        _dict = lines.Select(line => line.Split("="))
                     .ToDictionary(x => x[0], x => x[1]);
    }

    public string? this[string abbr] => _dict.ContainsKey(abbr) ? _dict[abbr] : null;

    public string find(string value)
    {
        return _dict.FirstOrDefault(x => x.Value == value).Key;
    }

    public IEnumerable<KeyValuePair<string, string>> FindAll(string subString)
    {
        return _dict.Where(item => item.Value.Contains(subString));
    }
}

public static void Main(String[] args)
{
    var abbrs = new Abbreviation();

    foreach (var item in abbrs.FindAll("Test"))
    {
        Console.WriteLine($"{item.Key}={item.Value}");
    }

    string? val = abbrs["Sample1"];
    Console.WriteLine(val); // Test1

    string val1 = abbrs.find("Test2");
    Console.WriteLine(val1); // Sample2
}
```

## ✅ 주요 기능
- 문자열 리스트를 Dictionary로 변환
- Key로 값 조회 (this[])
- Value로 Key 역조회 (find)
- 부분 문자열 포함된 항목 필터링 (FindAll)

## 💻 C++ 실전 Map 활용 예제
```cpp
std::map<std::string, std::string> dict = {
    {"Sample1", "Test1"},
    {"Sample2", "Test2"},
    {"Sample3", "Test3"},
};

std::string val = "Test2";

// Value로 Key 찾기
auto itrFind = std::find_if(dict.begin(), dict.end(),
    [&val](const std::pair<std::string, std::string>& item) {
        return item.second == val;
    });

if (itrFind != dict.end()) {
    std::cout << itrFind->first << std::endl; // Sample2
}
```

### ✅ 주요 기능
- std::map 초기화
- std::find_if로 Value 기반 검색
- 람다 표현식으로 조건 필터링




## ☕ Java 버전 - Abbreviation 클래스
```java
import java.util.*;
import java.util.stream.Collectors;

public class Abbreviation {
    private Map<String, String> dict;

    public Abbreviation() {
        List<String> lines = Arrays.asList("Sample1=Test1", "Sample2=Test2", "Sample3=Test3");
        dict = lines.stream()
                .map(line -> line.split("="))
                .collect(Collectors.toMap(x -> x[0], x -> x[1]));
    }

    public String get(String abbr) {
        return dict.getOrDefault(abbr, null);
    }

    public String find(String value) {
        return dict.entrySet()
                .stream()
                .filter(entry -> entry.getValue().equals(value))
                .map(Map.Entry::getKey)
                .findFirst()
                .orElse(null);
    }

    public List<Map.Entry<String, String>> findAll(String subString) {
        return dict.entrySet()
                .stream()
                .filter(entry -> entry.getValue().contains(subString))
                .collect(Collectors.toList());
    }

    public static void main(String[] args) {
        Abbreviation abbrs = new Abbreviation();

        for (Map.Entry<String, String> item : abbrs.findAll("Test")) {
            System.out.println(item.getKey() + "=" + item.getValue());
        }

        String val = abbrs.get("Sample1");
        System.out.println(val); // Test1

        String val1 = abbrs.find("Test2");
        System.out.println(val1); // Sample2
    }
}

```

## 🐍 Python 버전 - Abbreviation 클래스
```python
class Abbreviation:
    def __init__(self):
        lines = ["Sample1=Test1", "Sample2=Test2", "Sample3=Test3"]
        self._dict = dict(line.split("=") for line in lines)

    def __getitem__(self, abbr):
        return self._dict.get(abbr, None)

    def find(self, value):
        for k, v in self._dict.items():
            if v == value:
                return k
        return None

    def find_all(self, substring):
        return {k: v for k, v in self._dict.items() if substring in v}
```

# 실행 예시
```python
abbrs = Abbreviation()

for k, v in abbrs.find_all("Test").items():
    print(f"{k}={v}")

val = abbrs["Sample1"]
print(val)  # Test1

val1 = abbrs.find("Test2")
print(val1)  # Sample2
```



## 📌 Dictionary 기능 비교 요약 (C#, C++, Java, Python)

| 기능                     | C#                          | C++                          | Java                                     | Python                          |
|--------------------------|------------------------------|------------------------------|------------------------------------------|----------------------------------|
| Key로 값 조회            | `dict[key]`, `this[key]`     | `dict.find(key)`             | `dict.get(key)`                          | `dict[key]`, `dict.get(key)`     |
| Value로 Key 역조회       | `FirstOrDefault()`           | `std::find_if()`             | `stream().filter().findFirst()`          | `for-loop`, `next()`             |
| 부분 문자열 필터링       | `yield return`               | `std::find_if()`             | `stream().filter()`                      | `dict.items()` + 조건 필터링     |


✅ 이 표는 실전에서 Dictionary를 다룰 때 가장 많이 쓰이는 패턴들을 기능 중심으로 정리한 것입니다.




