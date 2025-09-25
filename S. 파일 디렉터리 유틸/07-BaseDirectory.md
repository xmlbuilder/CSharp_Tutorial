# Base Directory
C#에서 AppDomain.CurrentDomain.BaseDirectory는 현재 실행 중인 애플리케이션의 루트 디렉토리를 알려주는 아주 유용한 속성.  
이걸 활용하면 실행 파일 기준으로 리소스 파일 경로를 쉽게 구성할 수 있어요.

## ✅ C# 예시
```csharp
string baseFolder = AppDomain.CurrentDomain.BaseDirectory;
string logoPath = Path.Combine(baseFolder, "logo.jpg");
Console.WriteLine(File.Exists(logoPath));
```

- BaseDirectory: 실행 파일이 위치한 디렉토리
- Path.Combine: 경로 결합
- File.Exists: 파일 존재 여부 확인

## 🧊 C++ 예시 (C++17 이상)
```cpp
#include <iostream>
#include <filesystem>

int main() {
    std::filesystem::path baseDir = std::filesystem::current_path();
    std::filesystem::path logoPath = baseDir / "logo.jpg";

    std::cout << "Base Directory: " << baseDir << std::endl;
    std::cout << "Logo Exists: " << std::filesystem::exists(logoPath) << std::endl;
}
```
- std::filesystem::current_path() → 현재 작업 디렉토리
- exists() → 파일 존재 여부 확인

## ☕ Java 예시
```java
import java.io.File;

public class Main {
    public static void main(String[] args) {
        String baseDir = System.getProperty("user.dir");
        File logoFile = new File(baseDir, "logo.jpg");

        System.out.println("Base Directory: " + baseDir);
        System.out.println("Logo Exists: " + logoFile.exists());
    }
}
```

- System.getProperty("user.dir") → 현재 실행 디렉토리
- File.exists() → 파일 존재 여부 확인

## 🐍 Python 예시
```python
import os
from pathlib import Path

base_dir = Path.cwd()
logo_path = base_dir / "logo.jpg"

print("Base Directory:", base_dir)
print("Logo Exists:", logo_path.exists())
```

- Path.cwd() → 현재 작업 디렉토리
- .exists() → 파일 존재 여부 확인

## 📊 요약 비교 (Markdown 표)
| 언어     | Base Directory 구하는 방법             | 파일 존재 확인 방법               |
|----------|----------------------------------------|----------------------------------|
| C#       | `AppDomain.CurrentDomain.BaseDirectory` | `File.Exists(path)`              |
| C++      | `std::filesystem::current_path()`       | `std::filesystem::exists(path)`  |
| Java     | `System.getProperty("user.dir")`        | `File.exists()`                  |
| Python   | `Path.cwd()`                            | `path.exists()`                  |

---

# ✅ 더 안전한 대안: 실행 파일 경로 기반
C++에서는 실행 파일의 절대 경로를 기반으로 base directory를 구하는 것이 훨씬 안전합니다. 
## 🧊 Windows (C++)
```cpp
#include <windows.h>
#include <filesystem>
#include <iostream>

int main() {
    char buffer[MAX_PATH];
    GetModuleFileNameA(NULL, buffer, MAX_PATH);
    std::filesystem::path exePath(buffer);
    std::filesystem::path baseDir = exePath.parent_path();

    std::cout << "Base Directory: " << baseDir << std::endl;
}
```

## 🐧 Linux/macOS (C++)
```cpp
#include <unistd.h>
#include <filesystem>
#include <iostream>

int main() {
    char buffer[1024];
    ssize_t len = readlink("/proc/self/exe", buffer, sizeof(buffer)-1);
    buffer[len] = '\0';
    std::filesystem::path exePath(buffer);
    std::filesystem::path baseDir = exePath.parent_path();

    std::cout << "Base Directory: " << baseDir << std::endl;
}
```


## ☕ Java에서 안전한 base directory

Java는 System.getProperty("user.dir")가 기본이지만, 실행 JAR 기준으로 경로를 잡고 싶다면 다음처럼 할 수 있어요:
```java
import java.nio.file.Paths;

public class Main {
    public static void main(String[] args) {
        String baseDir = Main.class.getProtectionDomain()
                                   .getCodeSource()
                                   .getLocation()
                                   .getPath();
        System.out.println("Base Directory: " + Paths.get(baseDir).getParent());
    }
}
```

## 🐍 Python에서 안전한 base directory
Python은 __file__을 활용하면 현재 스크립트 기준으로 base directory를 잡을 수 있어요:
```python
from pathlib import Path

base_dir = Path(__file__).resolve().parent
print("Base Directory:", base_dir)
```

단, __file__은 인터프리터나 일부 환경에서는 없을 수 있으니 주의가 필요해요 (예: REPL, 일부 패키징 환경)


## 📌 요약
| 언어     | 안전한 Base Directory 접근 방식                             |
|----------|-------------------------------------------------------------|
| C++      | `GetModuleFileName` / `readlink("/proc/self/exe")`          |
| Java     | `Main.class.getProtectionDomain().getCodeSource()`          |
| Python   | `Path(__file__).resolve().parent`                           |

----
