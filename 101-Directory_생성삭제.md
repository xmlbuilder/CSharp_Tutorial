# 디렉토리 생성 및 삭제
파일 시스템을 다루는 데 있어 가장 기본적이면서도 중요한 작업이죠.

## 📁 C# 디렉토리 생성 및 삭제 요약
```csharp
// 디렉토리 생성 (재귀적으로 가능)
Directory.CreateDirectory("/Users/jeongjunghwan/Temp/Sample/Sample2");

// 디렉토리 삭제 (비어있지 않으면 실패)
Directory.Delete("/Users/jeongjunghwan/Temp/Sample");

// 디렉토리 삭제 (재귀적으로 삭제)
Directory.Delete("/Users/jeongjunghwan/Temp/Sample", recursive: true);

// 사용자 정의 재귀 삭제 함수
public static void DeleteRecursiveDirectory(string dirPath)
{
    foreach (var directory in Directory.GetDirectories(dirPath))
    {
        DeleteRecursiveDirectory(directory);
    }
    try
    {
        Directory.Delete(dirPath);
    }
    catch (IOException)
    {
        Directory.Delete(dirPath, true);
    }
    catch (UnauthorizedAccessException)
    {
        Directory.Delete(dirPath, true);
    }
}
```


## 🧊 C++ 디렉토리 생성 및 삭제
### ✅ C++17 이상 (<filesystem> 사용)
```cpp
#include <filesystem>
namespace fs = std::filesystem;

int main() {
    // 디렉토리 생성 (재귀적)
    fs::create_directories("/home/junghwan/Temp/Sample/Sample2");

    // 디렉토리 삭제 (비어있을 경우만 삭제됨)
    fs::remove("/home/junghwan/Temp/Sample");

    // 디렉토리 삭제 (재귀적으로 삭제)
    fs::remove_all("/home/junghwan/Temp/Sample");
}
```

💡 create_directories()는 중간 경로까지 모두 생성해줍니다. remove_all()은 하위 디렉토리까지 모두 삭제합니다.


## ☕ Java 디렉토리 생성 및 삭제
```java
import java.io.File;
import java.nio.file.*;

public class Main {
    public static void main(String[] args) throws Exception {
        // 디렉토리 생성 (재귀적)
        Files.createDirectories(Paths.get("/Users/jeongjunghwan/Temp/Sample/Sample2"));

        // 디렉토리 삭제 (비어있을 경우만)
        Files.delete(Paths.get("/Users/jeongjunghwan/Temp/Sample"));

        // 디렉토리 삭제 (재귀적)
        deleteDirectoryRecursively(Paths.get("/Users/jeongjunghwan/Temp/Sample"));
    }

    public static void deleteDirectoryRecursively(Path path) throws Exception {
        if (Files.exists(path)) {
            Files.walk(path)
                .sorted(Comparator.reverseOrder())
                .forEach(p -> {
                    try {
                        Files.delete(p);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                });
        }
    }
}
```


## 🐍 Python 디렉토리 생성 및 삭제
```python
import os
import shutil

# 디렉토리 생성 (재귀적)
os.makedirs("/Users/jeongjunghwan/Temp/Sample/Sample2", exist_ok=True)

# 디렉토리 삭제 (비어있을 경우만)
os.rmdir("/Users/jeongjunghwan/Temp/Sample")

# 디렉토리 삭제 (재귀적)
shutil.rmtree("/Users/jeongjunghwan/Temp/Sample")
```

os.makedirs()는 중간 디렉토리까지 생성하며, exist_ok=True로 이미 존재해도 에러가 발생하지 않아요. shutil.rmtree()는 하위 디렉토리까지 모두 삭제합니다.


## 📊 언어별 디렉토리 처리 비교

| 언어     | 디렉토리 생성 함수               | 삭제 함수 (비어있을 때)       | 삭제 함수 (재귀적)                  |
|----------|----------------------------------|-------------------------------|-------------------------------------|
| C#       | `Directory.CreateDirectory()`    | `Directory.Delete()`          | `Directory.Delete(..., true)`      |
| C++      | `fs::create_directories()`       | `fs::remove()`                | `fs::remove_all()`                 |
| Java     | `Files.createDirectories()`      | `Files.delete()`              | `Files.walk() + Files.delete()`    |
| Python   | `os.makedirs()`                  | `os.rmdir()`                  | `shutil.rmtree()`                  |

---


