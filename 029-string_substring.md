# 🔍 문자열 검색 및 추출 비교 (C#, C++, Java)
## ✅ C# 예제
```csharp
string target = "model;Sample=Test;";
int index = target.IndexOf("Sample="); // 6
int value = "Sample=".Length;          // 7
index += value;

string subStr = target.Substring(index); // "Test;"
Console.WriteLine(subStr);

target = "Novelist=김만중;BestWork=구운몽;Born=1687";
string searchValue = "BestWork=";
int startIndex = target.IndexOf(searchValue) + searchValue.Length;
int endIndex = target.IndexOf(";", startIndex);

string bestWork = target.Substring(startIndex, endIndex - startIndex); // "구운몽"
Console.WriteLine(bestWork);
```

### ✅ C#

| 메서드                          | 정의 및 설명 |
|----------------------------------|--------------|
| `string.IndexOf(string value)`   | 지정된 문자열이 처음 나타나는 인덱스를 반환. 없으면 -1 |
| `string.Substring(int startIndex)` | 지정된 시작 위치부터 문자열 끝까지 부분 문자열 반환 |
| `string.Substring(int startIndex, int length)` | 지정된 시작 위치부터 지정된 길이만큼 부분 문자열 반환 |
| `string.Contains(string value)`  | 문자열에 특정 값이 포함되어 있는지 여부 반환 (bool) |
| `string.StartsWith(string value)`| 문자열이 특정 값으로 시작하는지 여부 반환 |
| `string.EndsWith(string value)`  | 문자열이 특정 값으로 끝나는지 여부 반환 |

---



## ✅ C++ 예제
### 🔹 C++17 기준
```cpp
#include <iostream>
#include <string>

int main() {
    std::string target = "model;Sample=Test;";
    std::string key = "Sample=";

    size_t index = target.find(key); // 6
    size_t value = key.size();       // 7
    std::string subStr = target.substr(index + value); // "Test;"
    std::cout << subStr << std::endl;

    target = "Novelist=sample;BestWork=Model;Born=1687";
    std::string searchValue = "BestWork=";
    size_t startIndex = target.find(searchValue) + searchValue.size();
    size_t endIndex = target.find(";", startIndex);

    std::string bestWork = target.substr(startIndex, endIndex - startIndex); // "Model"
    std::cout << bestWork << std::endl;
}
```

### 🔹 C++20 개선 예시 (UTF-8 고려는 별도 필요)
```cpp
#include <iostream>
#include <string>
#include <string_view>

int main() {
    std::string_view target = "model;Sample=Test;";
    std::string_view key = "Sample=";

    size_t index = target.find(key);
    std::string_view subStr = target.substr(index + key.size()); // "Test;"
    std::cout << subStr << std::endl;

    target = "Novelist=sample;BestWork=Model;Born=1687";
    std::string_view searchValue = "BestWork=";
    size_t startIndex = target.find(searchValue) + searchValue.size();
    size_t endIndex = target.find(";", startIndex);

    std::string_view bestWork = target.substr(startIndex, endIndex - startIndex); // "Model"
    std::cout << bestWork << std::endl;
}
```

### ✅ C++ (C++17 기준)

| 함수 / 메서드                     | 정의 및 설명 |
|----------------------------------|--------------|
| `std::string::find(const std::string& str)` | 지정된 문자열이 처음 나타나는 위치 반환. 없으면 `std::string::npos` |
| `std::string::substr(size_t pos)` | 지정된 위치부터 끝까지 부분 문자열 반환 |
| `std::string::substr(size_t pos, size_t len)` | 지정된 위치부터 지정된 길이만큼 부분 문자열 반환 |
| `std::string::starts_with(string_view)` *(C++20)* | 문자열이 특정 값으로 시작하는지 여부 반환 |
| `std::string::ends_with(string_view)` *(C++20)* | 문자열이 특정 값으로 끝나는지 여부 반환 |
| `std::string::contains(string_view)` *(C++23)* | 문자열에 특정 값이 포함되어 있는지 여부 반환 |
---

⚠️ 한글 등 UTF-8 문자열은 std::string에서 문자 단위가 아닌 바이트 단위로 처리되므로, 정확한 문자 추출을 위해서는 std::wstring 또는 외부 라이브러리(ICU, fmt, etc.)가 필요합니다.

## ✅ Java 예제
```java
public class GrammarTest {
    public static void main(String[] args) {
        String target = "model;Sample=Test;";
        String key = "Sample=";

        int index = target.indexOf(key); // 6
        int value = key.length();        // 7
        String subStr = target.substring(index + value); // "Test;"
        System.out.println(subStr);

        target = "Novelist=김만중;BestWork=구운몽;Born=1687";
        String searchValue = "BestWork=";
        int startIndex = target.indexOf(searchValue) + searchValue.length();
        int endIndex = target.indexOf(";", startIndex);

        String bestWork = target.substring(startIndex, endIndex); // "구운몽"
        System.out.println(bestWork);
    }
}
```
### ✅ Java

| 메서드                              | 정의 및 설명 |
|--------------------------------------|--------------|
| `String.indexOf(String str)`         | 지정된 문자열이 처음 나타나는 인덱스를 반환. 없으면 -1 |
| `String.substring(int beginIndex)`   | 지정된 시작 위치부터 문자열 끝까지 부분 문자열 반환 |
| `String.substring(int beginIndex, int endIndex)` | 시작 위치부터 종료 위치 전까지 부분 문자열 반환 |
| `String.contains(CharSequence s)`    | 문자열에 특정 값이 포함되어 있는지 여부 반환 (boolean) |
| `String.startsWith(String prefix)`   | 문자열이 특정 값으로 시작하는지 여부 반환 |
| `String.endsWith(String suffix)`     | 문자열이 특정 값으로 끝나는지 여부 반환 |

---



## 🧠 요약 비교

| 언어   | 검색 함수           | 부분 문자열 추출 방식                         | 주요 메서드/함수 예시                          | 특징 및 설명 |
|--------|---------------------|-----------------------------------------------|------------------------------------------------|--------------|
| C++17  | `find()`            | `substr(start)`, `substr(start, length)`      | `target.find("key")`, `target.substr(pos, len)`| 바이트 단위 처리, UTF-8은 주의 필요 |
| C++20  | `find()` + `string_view` | `substr(start, length)` (복사 없이 참조)     | `std::string_view.substr(pos, len)`            | 성능 향상, 복사 없이 부분 문자열 참조 가능 |
| Java   | `indexOf()`         | `substring(start)`, `substring(start, end)`   | `target.indexOf("key")`, `target.substring(start, end)` | UTF-16 기반, 유니코드 안전 |
| C#     | `IndexOf()`         | `Substring(start)`, `Substring(start, length)`| `target.IndexOf("key")`, `target.Substring(start, length)` | .NET 문자열 처리 강력, 유니코드 완전 지원 |
---
