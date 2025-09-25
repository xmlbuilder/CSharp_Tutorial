
# C# `string` 메서드 vs Java `String` vs C++ (`std::string`) 비교 + C++ `trim`/`split` 구현 가이드

> 목적: C#에서 자주 쓰는 문자열 유틸을 Java와 C++로 **어떻게 동일하게**(또는 유사하게) 처리하는지 한눈에 정리.  
> 추가로, **C++에는 표준 `trim`/`split`이 없으므로** 안전하고 현대적인 대체 구현을 제공.

---

## 빠른 비교 표

| 기능 | C# (`System.String`) | Java (`java.lang.String`) | C++ (표준) |
|---|---|---|---|
| Null/Empty 체크 | `string.IsNullOrEmpty(s)` | `s == null || s.isEmpty()` | 포인터/옵셔널 여부 + `s.empty()` |
| Null/Whitespace 체크 | `string.IsNullOrWhiteSpace(s)` | `s == null || s.isBlank()` *(Java 11+)* | 직접 구현 필요 (`std::isspace`/`std::ranges`) |
| 접두 검사 | `s.StartsWith(x[, StringComparison])` | `s.startsWith(x)` | `s.rfind(x, 0) == 0` 또는 `s.starts_with(x)` *(C++20)* |
| 접미 검사 | `s.EndsWith(x[, StringComparison])` | `s.endsWith(x)` | `s.size()>=x.size() && s.compare(s.size()-x.size(), x.size(), x)==0` 또는 `s.ends_with(x)` *(C++20)* |
| 포함 검사 | `s.Contains(x[, StringComparison])` | `s.contains(x)` | `s.find(x) != npos` |
| 트림 | `s.Trim()` / `TrimStart` / `TrimEnd` | `s.trim()`(ASCII <= U+0020) / `s.strip()`(Java 11+, 유니코드) | **직접 구현** (아래 예시) |
| 분할 | `s.Split(...)` | `s.split(regex)` | **직접 구현** (`std::views::split`/수동 파싱) |

> ⚠️ 문화권/대소문자 비교:  
> - C#: `StringComparison`/`CultureInfo`로 문화권/대소문자 무시 선택 가능.  
> - Java: 기본 메서드는 문화권 비의존. 필요 시 `Collator` 사용 또는 `toLowerCase(Locale)`로 정규화 후 비교.  
> - C++: 표준만으로는 제한적. 엄밀한 로캘/유니코드 처리는 **ICU**나 **Boost.Locale** 권장.

---

## 1) IsNullOrEmpty / IsNullOrWhiteSpace

### C#
```csharp
string s = " ";
bool a = string.IsNullOrEmpty(s);       // false
bool b = string.IsNullOrWhiteSpace(s);  // true
```

### Java
```java
String s = " ";
boolean a = (s == null) || s.isEmpty(); // false
boolean b = (s == null) || s.isBlank(); // true (Java 11+)
```

### C++
```cpp
#include <string>
#include <cctype>
#include <algorithm>

bool is_null_or_empty(const std::string* ps) {
    return ps == nullptr || ps->empty();
}

bool is_null_or_whitespace(const std::string* ps) {
    if (ps == nullptr) return true;
    return std::all_of(ps->begin(), ps->end(), [](unsigned char ch){ return std::isspace(ch); });
}
```

---

## 2) StartsWith / EndsWith / Contains

### C#
```csharp
var s = "Visual Exception";
bool sw = s.StartsWith("Visual", StringComparison.CurrentCulture);
bool ew = s.EndsWith("Exception", StringComparison.Ordinal);
bool ct = s.Contains("Ex", StringComparison.OrdinalIgnoreCase);
```

### Java
```java
var s = "Visual Exception";
boolean sw = s.startsWith("Visual");
boolean ew = s.endsWith("Exception");
boolean ct = s.contains("Ex");
```

### C++
```cpp
#include <string>
#if __cpp_lib_starts_ends_with >= 201711  // C++20
bool sw = s.starts_with("Visual");
bool ew = s.ends_with("Exception");
#else
bool sw = s.rfind("Visual", 0) == 0;
bool ew = s.size() >= 9 && s.compare(s.size()-9, 9, "Exception") == 0;
#endif
bool ct = s.find("Ex") != std::string::npos;
```

> 대소문자 무시가 필요하다면:  
> - C#: `StringComparison.OrdinalIgnoreCase` 사용.  
> - Java: `s.toLowerCase(Locale.ROOT).contains(x.toLowerCase(Locale.ROOT))` 또는 `regionMatches(true, ...)`.  
> - C++: 소문자화 후 비교(UTF-8 일반화는 ICU/Boost 권장).

---

## 3) `Trim` / `TrimStart` / `TrimEnd` 대체

### C#
```csharp
var t1 = "  a b  ".Trim();      // "a b"
var t2 = "  a b  ".TrimStart(); // "a b  "
var t3 = "  a b  ".TrimEnd();   // "  a b"
```

### Java
```java
var t1 = "  a b  ".trim();   // 앞뒤 ASCII 공백(<=U+0020) 제거
var t2 = "  a b  ".strip();  // (Java 11+) 유니코드 공백 제거
```

### C++ (안전하고 빠른 구현; `std::string_view` 기반)
```cpp
#include <string>
#include <string_view>
#include <cctype>

inline bool is_space(unsigned char c){ return std::isspace(c); }

inline std::string_view trim_view(std::string_view sv) {
    size_t b = 0, e = sv.size();
    while (b < e && is_space(static_cast<unsigned char>(sv[b]))) ++b;
    while (e > b && is_space(static_cast<unsigned char>(sv[e-1]))) --e;
    return sv.substr(b, e - b);
}

// 필요 시 소유 문자열로 반환
inline std::string trim_copy(const std::string& s) {
    auto tv = trim_view(s);
    return std::string(tv.begin(), tv.end());
}

// 좌/우 트림
inline std::string_view ltrim_view(std::string_view sv) {
    size_t b = 0;
    while (b < sv.size() && is_space(static_cast<unsigned char>(sv[b]))) ++b;
    return sv.substr(b);
}
inline std::string_view rtrim_view(std::string_view sv) {
    size_t e = sv.size();
    while (e > 0 && is_space(static_cast<unsigned char>(sv[e-1]))) --e;
    return sv.substr(0, e);
}
```

> 참고: 위 구현은 **바이트 단위 공백** 판정(`std::isspace`)이며, 유니코드 공백 전폭 처리는 외부 라이브러리(예: ICU)가 필요.

---

## 4) `Split` 대체

### C#
```csharp
var parts = "a,b,,c".Split(',', StringSplitOptions.None);  // ["a","b","","c"]
```

### Java
```java
var parts = "a,b,,c".split(",", -1); // 빈 토큰 유지: ["a","b","","c"]
// 기본 split(",")은 trailing 빈 토큰을 버릴 수 있음
```

### C++ (C++20 `ranges::views::split` 예시: 구분자 1개)
```cpp
#include <string>
#include <string_view>
#include <vector>
#include <ranges>

std::vector<std::string_view> split_sv(std::string_view s, char delim) {
    std::vector<std::string_view> out;
    for (auto&& r : s | std::views::split(delim)) {
        auto v = std::string_view(&*r.begin(), std::ranges::distance(r));
        out.push_back(v); // 빈 토큰도 유지
    }
    return out;
}
```

### C++ (정규식 없이 여러 구분자 지원: 수동 파서)
```cpp
#include <string>
#include <string_view>
#include <vector>
#include <unordered_set>

std::vector<std::string_view> split_sv_multi(std::string_view s, std::string_view delims) {
    std::unordered_set<char> D(delims.begin(), delims.end());
    std::vector<std::string_view> out;
    size_t i = 0, n = s.size();
    size_t start = 0;
    while (i <= n) {
        if (i == n || D.count(s[i])) {
            out.emplace_back(s.data() + start, i - start); // 빈 토큰 유지
            start = i + 1;
        }
        ++i;
    }
    return out;
}
```

> 공백 기준 분리 + 트림 조합:
```cpp
std::vector<std::string> split_and_trim(std::string_view s, char delim) {
    std::vector<std::string> out;
    for (auto piece : split_sv(s, delim)) {
        auto tv = trim_view(piece);
        out.emplace_back(tv.begin(), tv.end());
    }
    return out;
}
```

---

## 5) 문화권/대소문자 무시 비교의 차이

- **C#**: `StringComparison.Ordinal`(바이너리), `InvariantCulture`, `CurrentCulture` 등 선택지를 제공. `Contains/StartsWith/EndsWith`에도 비교 옵션 인자 제공(.NET Core 2.1+).
- **Java**: 기본 비교는 바이너리/유니코드 코드포인트 기반. 문화권 민감한 비교가 필요하면 `java.text.Collator` 또는 `Locale`과 함께 정규화(`toLowerCase(Locale)`) 후 비교.
- **C++**: 표준만으로는 로캘 민감 문자열 비교가 제한적. **정확한 국제화**가 필요하면 **ICU** 또는 **Boost.Locale** 사용 권장.

---

## 6) 전체 동작 예 (요청 코드와 유사)

### C#
```csharp
string str = "jhjeong";
if (!string.IsNullOrEmpty(str)) Console.WriteLine(str);

str = " ";
if (string.IsNullOrWhiteSpace(str)) Console.WriteLine("빈문자 null or Whitespace");

str = "Visual Exception";
if (str.StartsWith("Visual")) Console.WriteLine(str);
if (str.EndsWith("Exception")) Console.WriteLine(str);
if (str.Contains("Exception")) Console.WriteLine(str);
```

### Java (동일 동작)
```java
String str = "jhjeong";
if (str != null && !str.isEmpty()) System.out.println(str);

str = " ";
if (str == null || str.isBlank()) System.out.println("빈문자 null or Whitespace");

str = "Visual Exception";
if (str.startsWith("Visual")) System.out.println(str);
if (str.endsWith("Exception")) System.out.println(str);
if (str.contains("Exception")) System.out.println(str);
```

### C++ (근접 동작; `trim`/`split` 별도 제공)
```cpp
#include <iostream>
#include <string>

int main(){
    std::string str = "jhjeong";
    if (!str.empty()) std::cout << str << "\n";

    str = " ";
    if (std::all_of(str.begin(), str.end(), [](unsigned char c){ return std::isspace(c); }))
        std::cout << "빈문자 null or Whitespace\n";

    str = "Visual Exception";
    bool starts = str.rfind("Visual", 0) == 0;
    bool ends   = str.size()>=9 && str.compare(str.size()-9, 9, "Exception")==0;
    bool has    = str.find("Exception") != std::string::npos;
    if (starts) std::cout << str << "\n";
    if (ends)   std::cout << str << "\n";
    if (has)    std::cout << str << "\n";
}
```

---

## 7) 실무 체크리스트

- **UTF-16 vs UTF-8**: C#과 Java는 내부 표현이 UTF-16 기반(서로 API 레벨에서 유사). C++ `std::string`은 **바이트 컨테이너**로 인코딩 계약이 없음. UTF-8을 쓴다면 **대소문자 변환/문자 단위 길이 계산**에 주의.
- **성능**: C++에서 불필요한 복사를 피하려면 `std::string_view`를 적극 사용. 반환이 필요할 때만 `std::string` 생성.
- **정규식 Split 비용**: Java의 `split(regex)`는 정규식 해석 비용이 있음. 단일 문자 구분자는 `String.indexOf` 루프가 더 빠를 수 있음.
- **문화권 비교**: 검색/정렬/대소문자 무시에 문화권이 중요하면 C#: `StringComparison`, Java: `Collator`, C++: ICU를 고려.

---

## 8) 요약

- C# → Java 매핑은 대부분 직관적(`isNullOrEmpty` ↔︎ `isEmpty`, `isBlank`).  
- C++은 표준에 **`trim`/`split`이 없으므로** 본 문서의 구현을 재사용.  
- 국제화/유니코드는 **외부 라이브러리**를 사용하는 것이 옳다.

