
# 🔗 String Split & Join — C#, Java, C++ 비교 가이드

> C#의 `String.Join` / `String.Split`에 해당하는 **Java**와 **C++** 구현/사용법을 한눈에 정리했습니다.  
> 빈 토큰 처리, 여러 구분자, 정규식 vs 리터럴, 성능 팁까지 포함합니다.

---

## 0) 핵심 요약 (표)

| 기능 | C# | Java | C++(표준) |
|---|---|---|---|
| Join(리스트 → 문자열) | `string.Join(sep, IEnumerable)` | `String.join(sep, Iterable)`, `StringJoiner`, `Collectors.joining()` | 헬퍼 함수 직접 구현(아래 제공). C++23: `ranges` 조합 가능 |
| Split(문자열 → 토큰) | `Split(char[]/string[], opts)` | `split(regex[, limit])` (정규식) | 헬퍼 함수 직접 구현(아래 제공), C++20 `views::split` |
| 여러 구분자 | `Split(new[]{' ','.'})` | 정규식 예: `split("[ .]+")` | 직접 루프 또는 정규식, `views::split`은 단일 구분자 |
| 빈 토큰 제거 | `StringSplitOptions.RemoveEmptyEntries` | `split(..., -1)`은 **유지**, 기본/`limit=0`은 **trailing 빈 토큰 제거** | 헬퍼에서 옵션 제공 |
| 리터럴 구분자 | `Split(",")` | `split(Pattern.quote(","))` | 리터럴 비교(빠름) 권장 |
| 문화권/유니코드 | 문자열 비교 아님(단순 분리/연결). 정규식/케이스 변환 시 국제화 주의 | 동일 | 동일 (UTF-8은 바이트 단위 처리. 유니코드 인식 분리에 정규식/ICU 고려) |

---

## 1) C# 예제

```csharp
var languages = new[] { "C#", "Java", "Javascript", "VB", "Ruby" };
var result = string.Join(",", languages);      // "C#,Java,Javascript,VB,Ruby"

// 리터럴 단일 구분자
string[] words = result.Split(",");

// 여러 구분자 + 빈 토큰 제거
string text = "The quick brown for jumps over the lazy dog.";
string[] words2 = text.Split(new[] { ' ', '.' }, StringSplitOptions.RemoveEmptyEntries);
```

### 전체 예제
```csharp
namespace  GrammarTest
{
    public class GrammarTest
    {
        public static void Main(String[] args)
        {
            var word1 = "Visual";
            var word2 = "Studio";
            var text = word1 + ' ' + word2;
            Console.WriteLine(text);
            //Visual Studio
            
            var languages = new string[]
            {
                "C#",
                "Java",
                "Javascript",
                "VB",
                "Ruby"
            };

            var separator = ",";
            var result = String.Join(separator, languages);

            Console.WriteLine(result);
            //C#,Java,Javascript,VB,Ruby

            string[] words = result.Split(",");
            foreach (var word in words)
            {
                Console.WriteLine(word);
            }
            /*
             C#
             Java
             Javascript
             VB
             Ruby
             */

            result = "The quick brown for jumps over the lazy dog.";
            string[] words2 = result.Split(new[] {' ', '.'}, StringSplitOptions.RemoveEmptyEntries);
            foreach (var word in words2)
            {
                Console.WriteLine(word);
            }
            /*
             The
             quick
             brown
             for
             jumps
             over
             the
             lazy
             dog
             */
        }
    }
}

```

---

## 2) Java 예제

```java
import java.util.Arrays;
import java.util.List;
import java.util.StringJoiner;
import java.util.regex.Pattern;
import java.util.stream.Collectors;

class Demo {
  public static void main(String[] args) {
    List<String> languages = Arrays.asList("C#", "Java", "Javascript", "VB", "Ruby");

    // Join
    String result = String.join(",", languages);
    String joined2 = languages.stream().collect(Collectors.joining(","));
    StringJoiner sj = new StringJoiner(",");
    languages.forEach(sj::add);
    String joined3 = sj.toString();

    // Split (리터럴)
    String[] words = result.split(Pattern.quote(","), -1); // 빈 토큰 유지

    // 여러 구분자(정규식)
    String text = "The quick brown for jumps over the lazy dog.";
    String[] words2 = text.split("[ .]+"); // 공백/점 1회 이상
  }
}
```

---

## 3) C++ 구현

### Join
```cpp
template <class It>
std::string join(It first, It last, std::string_view sep) {
  std::string out;
  if (first == last) return out;
  out.append(*first);
  ++first;
  while (first != last) {
    out.append(sep);
    out.append(*first);
    ++first;
  }
  return out;
}

template <class Container>
std::string join(const Container& c, std::string_view sep) {
  return join(std::begin(c), std::end(c), sep);
}
```
- accumulate를 이용하여 구현 할 수도 있다
```cpp

std::vector<std::string> lists;
lists.emplace_back("hello");
lists.emplace_back("world");

std::string delimiter = ",";
std::string ret = std::accumulate(lists.begin()+1, lists.end(), *(lists.begin()), [&delimiter](const std::string& lhs,
        const std::string& rhs){
        return lhs + delimiter + rhs;
});

std::cout << ret << std::endl; 
//hello,world
```


사용:
```cpp
std::vector<std::string> v = {"hello", "world"};
std::cout << join(v, ",") << "\n"; // hello,world
```

### Split (단일 구분자)
```cpp
enum class KeepEmpty { Yes, No };

std::vector<std::string_view>
split_sv(std::string_view s, char delim, KeepEmpty keep = KeepEmpty::Yes) {
  std::vector<std::string_view> out;
  size_t start = 0;
  for (size_t i = 0; i <= s.size(); ++i) {
    if (i == s.size() || s[i] == delim) {
      if (keep == KeepEmpty::Yes || i > start)
        out.emplace_back(s.data() + start, i - start);
      start = i + 1;
    }
  }
  return out;
}
```

사용:
```cpp
auto parts = split_sv("a,,b,c", ',', KeepEmpty::Yes); // ["a", "", "b", "c"]
auto parts2 = split_sv("a,,b,c", ',', KeepEmpty::No); // ["a", "b", "c"]
```

### Split (여러 구분자)
```cpp
std::vector<std::string_view>
split_sv_multi(std::string_view s, std::string_view delims, KeepEmpty keep = KeepEmpty::Yes) {
  std::unordered_set<char> D(delims.begin(), delims.end());
  std::vector<std::string_view> out;
  size_t start = 0;
  for (size_t i = 0; i <= s.size(); ++i) {
    if (i == s.size() || D.count(s[i])) {
      if (keep == KeepEmpty::Yes || i > start)
        out.emplace_back(s.data() + start, i - start);
      start = i + 1;
    }
  }
  return out;
}
```

사용:
```cpp
auto words = split_sv_multi("The.quick brown.for", " .", KeepEmpty::No);
// ["The","quick","brown","for"]
```

---

## 4) 차이점

- **C#**: `Split(..., StringSplitOptions.RemoveEmptyEntries)`로 빈 토큰 제어  
- **Java**: `split(regex, -1)` → 빈 토큰 유지, 기본은 끝의 빈 토큰 제거  
- **C++**: 직접 구현 → 옵션으로 제어

---

## 5) 결론

- C# ↔ Java는 거의 일대일 매핑 (`Join` ↔ `String.join`, `Split` ↔ `split`)  
- C++은 표준 API가 없어 헬퍼 구현 필요.  
- 성능상 단일 구분자는 정규식 대신 루프 구현이 빠름.
