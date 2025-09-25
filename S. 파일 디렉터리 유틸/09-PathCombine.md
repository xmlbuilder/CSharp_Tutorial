# Path Combine

C#는 Path.Combine()을 이용하여 여러 문자열을 안전하게 연결해서 경로를 구성하는 기능


## C#
```csharp
var txt1 = "/Uesrs";
var txt2 = "junghwanjeong";
var txt3 = "Downloads";
var path = Path.Combine(txt1, txt2, txt3);
Console.WriteLine(path);
```


## 🐍 Python (os.path.join 또는 pathlib.Path)
```python
import os
from pathlib import Path

txt1 = "/Users"
txt2 = "junghwanjeong"
txt3 = "Downloads"

# os.path 방식
path = os.path.join(txt1, txt2, txt3)
print(path)  # /Users/junghwanjeong/Downloads

# pathlib 방식 (Python 3.4+)
p = Path(txt1) / txt2 / txt3
print(p)     # /Users/junghwanjeong/Downloads
```



- os.path.join()은 문자열 기반
- Path(...) / ...는 객체 기반으로 더 직관적이고 안전함

## ☕ Java (Paths.get() 또는 Path.resolve())
```java
import java.nio.file.*;

public class PathJoinExample {
    public static void main(String[] args) {
        String txt1 = "/Users";
        String txt2 = "junghwanjeong";
        String txt3 = "Downloads";

        Path path = Paths.get(txt1, txt2, txt3);
        System.out.println(path);  // /Users/junghwanjeong/Downloads
    }
}
```

- Paths.get(...)은 자동으로 경로 구분자를 처리해줌
- Path.resolve(...)를 사용하면 기존 경로에 추가하는 방식도 가능

## 💻 C++17 이상 (std::filesystem::path)
```cpp
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main() {
    fs::path txt1 = "/Users";
    fs::path txt2 = "junghwanjeong";
    fs::path txt3 = "Downloads";

    fs::path fullPath = txt1 / txt2 / txt3;
    std::cout << fullPath << std::endl;  // /Users/junghwanjeong/Downloads

    return 0;
}
```


- / 연산자를 사용해 경로를 자연스럽게 연결
- fs::path 객체는 경로 조작에 특화되어 있어 안전하고 편리함

## 📌 요약 비교 (Markdown 표)
| 언어     | 경로 구성 방식                          | 결과 예시                          |
|----------|------------------------------------------|------------------------------------|
| C#       | `Path.Combine(txt1, txt2, txt3)`         | `/Users/junghwanjeong/Downloads`  |
| Python   | `os.path.join(...)` 또는 `Path(...) / ...` | `/Users/junghwanjeong/Downloads`  |
| Java     | `Paths.get(txt1, txt2, txt3)`            | `/Users/junghwanjeong/Downloads`  |
| C++17+   | `fs::path txt1 / txt2 / txt3`            | `/Users/junghwanjeong/Downloads`  |

---


