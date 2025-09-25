
## ✅ C#의 Concat() 기능
- 기능: 두 시퀀스를 하나로 연결
- 형식: collection1.Concat(collection2)
- 특징:
- 원본 컬렉션은 변경되지 않음
- 오버로드 없음 (단순 병합만 지원)
- 예시:
```csharp
string[] files1 = Directory.GetFiles(@"/Users/jeongjunghwan");
string[] files2 = Directory.GetFiles(@"/Users/jeongjunghwan/Downloads");

var files = files1.Concat(files2); // 두 배열 병합

foreach (var fileName in files)
{
    Console.WriteLine(fileName);
}
```


## 🧊 C++에서의 Concat 기능
### 🔹 방법 1: std::vector::insert
```cpp
#include <vector>
#include <string>

std::vector<std::string> files1 = {/* ... */};
std::vector<std::string> files2 = {/* ... */};

files1.insert(files1.end(), files2.begin(), files2.end()); // files1에 files2 병합
```

### 🔹 방법 2: std::ranges::join (C++23)
- 최신 C++에서는 ranges::join으로 병합 가능 (단, 지원 여부 확인 필요)

## ☕ Java에서의 Concat 기능
### 🔹 방법 1: Stream.concat()
```java
Stream<String> files1 = Files.list(Paths.get("/Users/jeongjunghwan"));
Stream<String> files2 = Files.list(Paths.get("/Users/jeongjunghwan/Downloads"));

Stream<String> allFiles = Stream.concat(files1, files2);
allFiles.forEach(System.out::println);
```


### 🔹 방법 2: List.addAll()
```java
List<String> list1 = new ArrayList<>();
List<String> list2 = new ArrayList<>();

list1.addAll(list2); // list1에 list2 병합
```


## 🐍 Python에서의 Concat 기능
### 🔹 방법 1: 리스트 덧셈 (+ 연산자)
```python
import os

files1 = os.listdir("/Users/jeongjunghwan")
files2 = os.listdir("/Users/jeongjunghwan/Downloads")

files = files1 + files2  # 리스트 병합

for file in files:
    print(file)

```

### 🔹 방법 2: itertools.chain()
```python
from itertools import chain

files = list(chain(files1, files2))  # 메모리 효율적으로 병합
```


## 📊 요약 비교 (Markdown 표)
| 언어   | 병합 방식                          | 순서 유지 | 특징 및 메모                          |
|--------|-----------------------------------|------------|---------------------------------------|
| C#     | `Concat(collection2)`             | ✅         | LINQ 기반, 오버로드 없음              |
| C++    | `vector::insert()`                | ✅         | 명시적 병합, C++23부터 `ranges::join` |
| Java   | `Stream.concat()` / `addAll()`    | ✅         | 스트림 기반 또는 리스트 병합          |
| Python | `list1 + list2` / `itertools.chain()` | ✅     | 간결하고 직관적, 제너레이터 방식도 가능 |



이런 병합 기능은 실무에서 파일 목록 통합, 로그 병합, 사용자 데이터 합치기, 다중 소스 처리 등에 자주 활용돼요.

---

