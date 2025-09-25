# Zip 파일 
System.IO.Compression 네임스페이스의 ZipFile/ZipArchive를 이용해서
- 디렉터리를 ZIP으로 압축하고
- ZIP 파일을 열어서 내부 파일 목록을 출력하는 코드입니다.

## 1. C#에서 라이브러리 설치 필요 여부
- .NET Framework 4.5 이상 또는 .NET Core/5+/6+에서는 기본 제공됩니다.
- 단, ZipFile 클래스를 쓰려면
using System.IO.Compression;
using System.IO.Compression.ZipFile;
- 두 네임스페이스를 추가해야 하고,
.NET Framework 프로젝트라면 System.IO.Compression.FileSystem 어셈블리를 참조에 추가해야 합니다.
- NuGet 패키지 설치는 필요 없습니다(구버전 .NET 제외).
```csharp
ZipFile.CreateFromDirectory(@"/home/jhjeong/Projects/UnsafeTest", @"/home/jhjeong/test.zip");

using (ZipArchive zip = ZipFile.Open("/home/jhjeong/test.zip", ZipArchiveMode.Read))
{
    foreach(ZipArchiveEntry entry in zip.Entries)
    {
        Console.WriteLine(entry.Name + " " + entry.Length + " " + entry.FullName);
    }
}
```

## 2. C++에서 ZIP 만들기/읽기
C++ 표준 라이브러리에는 ZIP 지원이 없어서 외부 라이브러리를 설치해야 합니다. 대표적으로:
- libzip (C API)
- minizip
- zlib + 래퍼
libzip 예시 (읽기):
```cpp
#include <zip.h>
#include <iostream>

int main() {
    int err = 0;
    zip* archive = zip_open("/home/jhjeong/test.zip", 0, &err);
    if (!archive) {
        std::cerr << "ZIP 파일 열기 실패\n";
        return 1;
    }

    zip_int64_t num_entries = zip_get_num_entries(archive, 0);
    for (zip_uint64_t i = 0; i < num_entries; i++) {
        struct zip_stat st;
        zip_stat_init(&st);
        zip_stat_index(archive, i, 0, &st);
        std::cout << st.name << " " << st.size << "\n";
    }

    zip_close(archive);
}
```

⚠️ libzip는 OS 패키지 매니저(예: Ubuntu sudo apt install libzip-dev)로 설치해야 합니다.


## 3. Java에서 ZIP 만들기/읽기
Java는 java.util.zip 패키지로 기본 지원합니다.
ZIP 생성 & 읽기 예시:
```java
import java.io.*;
import java.nio.file.*;
import java.util.zip.*;

public class ZipExample {
    public static void main(String[] args) throws IOException {
        // 디렉터리 → ZIP
        Path sourceDir = Paths.get("/home/jhjeong/Projects/UnsafeTest");
        try (ZipOutputStream zos = new ZipOutputStream(
                new FileOutputStream("/home/jhjeong/test.zip"))) {
            Files.walk(sourceDir).filter(Files::isRegularFile).forEach(path -> {
                ZipEntry zipEntry = new ZipEntry(sourceDir.relativize(path).toString());
                try {
                    zos.putNextEntry(zipEntry);
                    Files.copy(path, zos);
                    zos.closeEntry();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            });
        }

        // ZIP 읽기
        try (ZipFile zipFile = new ZipFile("/home/jhjeong/test.zip")) {
            zipFile.stream().forEach(entry -> {
                System.out.println(entry.getName() + " " + entry.getSize());
            });
        }
    }
}

```
Java는 별도 라이브러리 설치 없이 바로 사용 가능.


## 4. Python에서 ZIP 만들기/읽기
Python 표준 라이브러리 zipfile 모듈 사용.
```python
import zipfile
from pathlib import Path

# 디렉터리 → ZIP
source_dir = Path("/home/jhjeong/Projects/UnsafeTest")
with zipfile.ZipFile("/home/jhjeong/test.zip", "w", zipfile.ZIP_DEFLATED) as zipf:
    for file in source_dir.rglob("*"):
        if file.is_file():
            zipf.write(file, file.relative_to(source_dir))

# ZIP 읽기
with zipfile.ZipFile("/home/jhjeong/test.zip", "r") as zipf:
    for info in zipf.infolist():
        print(info.filename, info.file_size)

```
Python도 기본 내장 모듈이라 설치 불필요.


## 📌 정리
| 언어      | ZIP 기본 지원 여부 | 필요 라이브러리 / 모듈 | 비고 |
|-----------|-------------------|------------------------|------|
| **C#**    | O ( .NET 4.5+ / .NET Core+ ) | `System.IO.Compression`, `System.IO.Compression.ZipFile`<br>※ .NET Framework 구버전은 `System.IO.Compression.FileSystem` 참조 추가 필요 | `using` 구문으로 네임스페이스 추가 |
| **C++**   | X                 | `libzip`, `minizip`, `zlib` 등 외부 라이브러리 설치 필요 | 표준 라이브러리에는 ZIP 지원 없음 |
| **Java**  | O                 | `java.util.zip` (기본 제공) | `ZipOutputStream`, `ZipFile` 등 사용 |
| **Python**| O                 | `zipfile` (표준 라이브러리) | `with zipfile.ZipFile(...)` 형태로 사용 |


--- 

# C#, Java, Python, C++(libzip) 네 언어에서 모두 동일하게 동작하는 ZIP 생성/읽기 예제입니다.
동작 흐름은 모두 동일하게 맞췄습니다.

## 📌 동작 시나리오
- /home/jhjeong/Projects/UnsafeTest 디렉터리를 test.zip으로 압축
- test.zip을 열어서 내부 파일 이름과 크기를 출력

## C# 예제 (.NET 6+)
```csharp
using System;
using System.IO;
using System.IO.Compression;

class Program
{
    static void Main()
    {
        string sourceDir = "/home/jhjeong/Projects/UnsafeTest";
        string zipPath = "/home/jhjeong/test.zip";

        // 디렉터리 → ZIP
        if (Directory.Exists(sourceDir))
        {
            ZipFile.CreateFromDirectory(sourceDir, zipPath, CompressionLevel.Optimal, false);
        }

        // ZIP 읽기
        using (ZipArchive zip = ZipFile.Open(zipPath, ZipArchiveMode.Read))
        {
            foreach (ZipArchiveEntry entry in zip.Entries)
            {
                Console.WriteLine($"{entry.FullName} {entry.Length}");
            }
        }
    }
}
```

참고:
- using System.IO.Compression;
- using System.IO.Compression.ZipFile;
- .NET Framework 구버전은 System.IO.Compression.FileSystem 참조 추가 필요


## Java 예제
```java
import java.io.*;
import java.nio.file.*;
import java.util.zip.*;

public class ZipExample {
    public static void main(String[] args) throws IOException {
        Path sourceDir = Paths.get("/home/jhjeong/Projects/UnsafeTest");
        String zipPath = "/home/jhjeong/test.zip";

        // 디렉터리 → ZIP
        try (ZipOutputStream zos = new ZipOutputStream(new FileOutputStream(zipPath))) {
            Files.walk(sourceDir)
                 .filter(Files::isRegularFile)
                 .forEach(path -> {
                     ZipEntry zipEntry = new ZipEntry(sourceDir.relativize(path).toString());
                     try {
                         zos.putNextEntry(zipEntry);
                         Files.copy(path, zos);
                         zos.closeEntry();
                     } catch (IOException e) {
                         e.printStackTrace();
                     }
                 });
        }

        // ZIP 읽기
        try (ZipFile zipFile = new ZipFile(zipPath)) {
            zipFile.stream().forEach(entry -> {
                System.out.println(entry.getName() + " " + entry.getSize());
            });
        }
    }
}

```
Java는 java.util.zip 기본 제공 → 별도 라이브러리 설치 불필요


## Python 예제
```python
import zipfile
from pathlib import Path

source_dir = Path("/home/jhjeong/Projects/UnsafeTest")
zip_path = Path("/home/jhjeong/test.zip")

# 디렉터리 → ZIP
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zipf:
    for file in source_dir.rglob("*"):
        if file.is_file():
            zipf.write(file, file.relative_to(source_dir))

# ZIP 읽기
with zipfile.ZipFile(zip_path, "r") as zipf:
    for info in zipf.infolist():
        print(info.filename, info.file_size)
```

Python 표준 라이브러리 zipfile 사용 → 설치 불필요


## C++ (libzip) 예제
⚠️ 사전 준비:
- Ubuntu/Debian: sudo apt install libzip-dev
- macOS(Homebrew): brew install libzip
- Windows(vcpkg): vcpkg install libzip

```cpp
#include <zip.h>
#include <iostream>
#include <filesystem>
#include <vector>

namespace fs = std::filesystem;

int main() {
    std::string sourceDir = "/home/jhjeong/Projects/UnsafeTest";
    std::string zipPath = "/home/jhjeong/test.zip";

    // 디렉터리 → ZIP 생성
    int errorp;
    zip_t* archive = zip_open(zipPath.c_str(), ZIP_CREATE | ZIP_TRUNCATE, &errorp);
    if (!archive) {
        std::cerr << "ZIP 파일 생성 실패\n";
        return 1;
    }

    for (auto& p : fs::recursive_directory_iterator(sourceDir)) {
        if (p.is_regular_file()) {
            std::string relPath = fs::relative(p.path(), sourceDir).string();
            zip_source_t* src = zip_source_file(archive, p.path().string().c_str(), 0, 0);
            if (src == nullptr || zip_file_add(archive, relPath.c_str(), src, ZIP_FL_ENC_UTF_8) < 0) {
                zip_source_free(src);
                std::cerr << "파일 추가 실패: " << relPath << "\n";
            }
        }
    }
    zip_close(archive);

    // ZIP 읽기
    archive = zip_open(zipPath.c_str(), 0, &errorp);
    if (!archive) {
        std::cerr << "ZIP 파일 열기 실패\n";
        return 1;
    }

    zip_int64_t num_entries = zip_get_num_entries(archive, 0);
    for (zip_uint64_t i = 0; i < num_entries; i++) {
        struct zip_stat st;
        zip_stat_init(&st);
        if (zip_stat_index(archive, i, 0, &st) == 0) {
            std::cout << st.name << " " << st.size << "\n";
        }
    }
    zip_close(archive);
}

```

## 📊 비교 표
| 언어       | ZIP 생성 방법 | ZIP 읽기 방법 | 기본 지원 여부 | 필요 라이브러리 / 모듈 | 비고 |
|------------|--------------|--------------|----------------|------------------------|------|
| **C#**     | `ZipFile.CreateFromDirectory` | `ZipFile.Open` + `ZipArchive` | O | `System.IO.Compression`, `System.IO.Compression.ZipFile`<br>※ .NET Framework 구버전은 `System.IO.Compression.FileSystem` 참조 필요 | .NET 4.5+ / .NET Core+ 기본 제공 |
| **Java**   | `ZipOutputStream` | `ZipFile` | O | `java.util.zip` (기본 제공) | 별도 설치 불필요 |
| **Python** | `zipfile.ZipFile` | `zipfile.ZipFile` | O | `zipfile` (표준 라이브러리) | `with` 문으로 안전하게 사용 |
| **C++**    | `zip_file_add` (libzip) | `zip_stat_index` (libzip) | X | `libzip` 설치 필요<br>(Ubuntu: `apt install libzip-dev`, macOS: `brew install libzip`) | 표준 라이브러리에 ZIP 없음 |

---




