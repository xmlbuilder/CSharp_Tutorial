# 파일 시스템을 순회
파일 시스템을 순회 하면서 파일 정보를 읽고, 드라이브 목록까지 탐색하는 건 시스템 유틸리티나 백엔드 작업에서 자주 쓰이는 핵심 기능

## ✅ C# 예제 요약
```csharp
// 디렉토리 내 파일 목록 가져오기
string[] files = Directory.GetFiles("/home/jhjeong");

foreach (string file in files)
{
    FileInfo fi = new FileInfo(file);
    if (fi != null)
    {
        int size = (int)fi.Length;
        string text = fi.OpenText().ReadToEnd();
        Console.WriteLine(text);
        break;
    }
}

// 드라이브 목록 탐색
DriveInfo[] drives = DriveInfo.GetDrives();
foreach (var drv in drives)
{
    Console.WriteLine(drv.Name); // 예: C:\, D:\
}
```


## 🧊 C++ 디렉토리 및 드라이브 탐색 (C++17 이상)
```cpp
#include <iostream>
#include <filesystem>
#include <fstream>

namespace fs = std::filesystem;

int main() {
    std::string path = "/home/jhjeong";

    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_regular_file(entry)) {
            std::ifstream file(entry.path());
            std::string content((std::istreambuf_iterator<char>(file)),
                                 std::istreambuf_iterator<char>());
            std::cout << content << std::endl;
            break;
        }
    }

    // Windows에서 드라이브 탐색 (Windows 전용)
    #ifdef _WIN32
    char drives[256];
    DWORD count = GetLogicalDriveStringsA(sizeof(drives), drives);
    for (int i = 0; i < count; i += strlen(&drives[i]) + 1)
        std::cout << &drives[i] << std::endl; // 예: C:\, D:\
    #endif
}
```

💡 std::filesystem은 C++17 이상에서 사용 가능하며, Windows 드라이브 탐색은 GetLogicalDriveStrings API를 사용합니다.


## ☕ Java 디렉토리 및 드라이브 탐색
```java
import java.io.*;
import java.nio.file.*;

public class Main {
    public static void main(String[] args) throws IOException {
        Path dir = Paths.get("/home/jhjeong");

        try (DirectoryStream<Path> stream = Files.newDirectoryStream(dir)) {
            for (Path file : stream) {
                if (Files.isRegularFile(file)) {
                    String content = Files.readString(file);
                    System.out.println(content);
                    break;
                }
            }
        }

        // 드라이브 탐색 (Windows 전용)
        File[] roots = File.listRoots();
        for (File root : roots) {
            System.out.println(root.getAbsolutePath()); // 예: C:\, D:\
        }
    }
}
```


## 🐍 Python 디렉토리 및 드라이브 탐색
```python
import os

# 디렉토리 순회
path = "/home/jhjeong"
for filename in os.listdir(path):
    full_path = os.path.join(path, filename)
    if os.path.isfile(full_path):
        with open(full_path, 'r', encoding='utf-8') as f:
            content = f.read()
            print(content)
            break

# 드라이브 탐색 (Windows 전용)
if os.name == 'nt':
    import string
    from ctypes import windll

    drives = []
    bitmask = windll.kernel32.GetLogicalDrives()
    for letter in string.ascii_uppercase:
        if bitmask & 1:
            drives.append(f"{letter}:\\")
        bitmask >>= 1
    print(drives)  # 예: ['C:\\', 'D:\\']

```

## 📊 언어별 디렉토리/드라이브 탐색 비교
| 언어   | 디렉토리 순회 방식                  | 파일 읽기 방식             | 드라이브 탐색 방식              |
|--------|-------------------------------------|----------------------------|----------------------------------|
| C#     | `Directory.GetFiles()`              | `FileInfo.OpenText()`      | `DriveInfo.GetDrives()`         |
| C++    | `fs::directory_iterator()`          | `std::ifstream`            | `GetLogicalDriveStrings()`      |
| Java   | `Files.newDirectoryStream()`        | `Files.readString()`       | `File.listRoots()`              |
| Python | `os.listdir()` + `os.path.isfile()` | `open().read()`            | `ctypes.windll.kernel32`        |


# 재귀적 파일 순회

파일 크기 확인, 확장자 필터링, 그리고 재귀적 디렉토리 탐색 구현

## 📊 언어별 기능 비교
| 기능                     | C#                                | C++ (C++17 이상)                     | Java                              | Python                            |
|--------------------------|------------------------------------|--------------------------------------|-----------------------------------|-----------------------------------|
| 파일 크기 확인           | `FileInfo.Length`                 | `fs::file_size(path)`               | `Files.size(path)`               | `os.path.getsize(path)`          |
| 확장자 필터링           | `Path.GetExtension(file) == ".txt"` | `entry.path().extension() == ".txt"` | `file.getName().endsWith(".txt")` | `file.endswith(".txt")`          |
| 재귀적 디렉토리 탐색     | `Directory.GetFiles(path, "*", SearchOption.AllDirectories)` | `fs::recursive_directory_iterator()` | `Files.walk(path)`               | `os.walk(path)`                  |



## 💡 각 기능별 코드 예시
### ✅ C# 예시
```csharp
string[] files = Directory.GetFiles("/home/jhjeong", "*", SearchOption.AllDirectories);

foreach (string file in files)
{
    if (Path.GetExtension(file) == ".txt")
    {
        FileInfo fi = new FileInfo(file);
        long size = fi.Length;
        Console.WriteLine($"File: {file}, Size: {size} bytes");
    }
}
```


### 🧊 C++ 예시 (C++17 이상)
```cpp
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main() {
    std::string path = "/home/jhjeong";

    for (const auto& entry : fs::recursive_directory_iterator(path)) {
        if (fs::is_regular_file(entry) && entry.path().extension() == ".txt") {
            auto size = fs::file_size(entry.path());
            std::cout << "File: " << entry.path() << ", Size: " << size << " bytes\n";
        }
    }
}
```

### ☕ Java 예시
```java
import java.io.IOException;
import java.nio.file.*;

public class Main {
    public static void main(String[] args) throws IOException {
        Path dir = Paths.get("/home/jhjeong");

        Files.walk(dir)
            .filter(Files::isRegularFile)
            .filter(path -> path.toString().endsWith(".txt"))
            .forEach(path -> {
                try {
                    long size = Files.size(path);
                    System.out.println("File: " + path + ", Size: " + size + " bytes");
                } catch (IOException e) {
                    e.printStackTrace();
                }
            });
    }
}
```

### 🐍 Python 예시
```python
import os

for root, dirs, files in os.walk("/home/jhjeong"):
    for file in files:
        if file.endswith(".txt"):
            full_path = os.path.join(root, file)
            size = os.path.getsize(full_path)
            print(f"File: {full_path}, Size: {size} bytes")
```            


## 🔍 추가 팁
- 확장자 필터링은 대소문자 구분이 있을 수 있으니 .lower()를 활용하면 더 안전해요.
- 재귀 탐색 시 .hidden 파일이나 .git 폴더 등을 제외하고 싶다면 조건문을 추가하면 됩니다.
- 파일 크기 단위 변환 (KB, MB)도 간단히 size / 1024 또는 size / (1024*1024)로 처리 가능.

---

