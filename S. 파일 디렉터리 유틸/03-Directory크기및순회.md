# 서브디렉토리 목록 열거

C# 코드에서는 지정한 디렉토리(/Users/jeongjunghwan/Downloads) 안의 서브 디렉토리 목록을 열거하고, 
디렉토리 이름 길이가 10자 이상인 것만 필터링해서 생성 시간과 함께 출력.


## ✅ C# 코드 요약
```csharp
var di = new DirectoryInfo(@"/Users/jeongjunghwan/Downloads");

var directories = di.EnumerateDirectories().Where(d => d.Name.Length > 10);

foreach (var dir in directories)
{
    Console.WriteLine($"{dir.Name} - {dir.CreationTime}");
}
```


## 🧊 C++ (C++17 이상)
```cpp
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main() {
    std::string path = "/Users/jeongjunghwan/Downloads";

    for (const auto& entry : fs::directory_iterator(path)) {
        if (fs::is_directory(entry)) {
            std::string name = entry.path().filename().string();
            if (name.length() > 10) {
                auto ftime = fs::last_write_time(entry);
                std::time_t cftime = decltype(ftime)::clock::to_time_t(ftime);
                std::cout << name << " - " << std::asctime(std::localtime(&cftime));
            }
        }
    }
}
```


💡 last_write_time()은 생성 시간과는 다르지만, 대부분의 시스템에서 유사하게 사용됩니다.


## ☕ Java
```java
import java.io.File;
import java.text.SimpleDateFormat;

public class Main {
    public static void main(String[] args) {
        File dir = new File("/Users/jeongjunghwan/Downloads");
        File[] directories = dir.listFiles(File::isDirectory);

        if (directories != null) {
            for (File subdir : directories) {
                String name = subdir.getName();
                if (name.length() > 10) {
                    long modified = subdir.lastModified();
                    String time = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(modified);
                    System.out.println(name + " - " + time);
                }
            }
        }
    }
}

```

## 🐍 Python
```python
import os
import datetime

path = "/Users/jeongjunghwan/Downloads"

for name in os.listdir(path):
    full_path = os.path.join(path, name)
    if os.path.isdir(full_path) and len(name) > 10:
        creation_time = os.path.getctime(full_path)
        formatted_time = datetime.datetime.fromtimestamp(creation_time).strftime('%Y-%m-%d %H:%M:%S')
        print(f"{name} - {formatted_time}")

```

## 📊 기능 비교 요약 (Markdown 표)
| 언어   | 디렉토리 탐색 방식               | 이름 길이 필터링         | 생성/수정 시간 확인 방식         |
|--------|----------------------------------|---------------------------|----------------------------------|
| C#     | `DirectoryInfo.EnumerateDirectories()` | `d.Name.Length > 10`      | `dir.CreationTime`              |
| C++    | `fs::directory_iterator()`       | `name.length() > 10`      | `fs::last_write_time()`         |
| Java   | `File.listFiles(File::isDirectory)` | `name.length() > 10`      | `file.lastModified()`           |
| Python | `os.listdir()` + `os.path.isdir()` | `len(name) > 10`          | `os.path.getctime()`            |


---

# 디렉토리 크기 계산 및 출력

지정한 디렉토리 내의 서브 디렉토리 목록을 열거하면서, 각 디렉토리의 크기를 계산하고, 정렬한 뒤, JSON 형식으로 출력하는 방법

## 📊 기능 요약 (Markdown 표)
| 기능               | C++ (C++17 이상)               | Java                              | Python                            |
|--------------------|--------------------------------|-----------------------------------|-----------------------------------|
| 디렉토리 크기 계산 | `fs::recursive_directory_iterator()` + `file_size()` | `Files.walk()` + `Files.size()` | `os.walk()` + `os.path.getsize()` |
| 정렬               | `std::sort()`                  | `Collections.sort()`              | `sorted()`                        |
| JSON 출력          | `nlohmann/json` 라이브러리     | `org.json` 또는 `Gson`            | `json` 내장 모듈                  |





## ✅ C# 예제: 디렉토리 크기 계산 + 정렬 + JSON 출력
```csharp
using System;
using System.IO;
using System.Linq;
using System.Collections.Generic;
using System.Text.Json;

namespace GrammarTest
{
    public class Program
    {
        public static void Main(string[] args)
        {
            string basePath = @"/Users/jeongjunghwan/Downloads";
            var di = new DirectoryInfo(basePath);

            var dirSizes = new List<(string Name, long Size)>();

            foreach (var dir in di.EnumerateDirectories())
            {
                long size = GetDirectorySize(dir);
                dirSizes.Add((dir.Name, size));
            }

            // 크기 기준 내림차순 정렬
            var sorted = dirSizes.OrderByDescending(d => d.Size);

            // JSON 출력
            var jsonDict = sorted.ToDictionary(d => d.Name, d => d.Size);
            string json = JsonSerializer.Serialize(jsonDict, new JsonSerializerOptions { WriteIndented = true });
            Console.WriteLine(json);
        }

        public static long GetDirectorySize(DirectoryInfo dir)
        {
            long size = 0;
            try
            {
                foreach (var file in dir.EnumerateFiles("*", SearchOption.AllDirectories))
                {
                    size += file.Length;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error reading {dir.FullName}: {ex.Message}");
            }
            return size;
        }
    }
}
```

## 🧊 C++ 예시 (C++17 이상)
```cpp
#include <iostream>
#include <filesystem>
#include <vector>
#include <algorithm>
#include <nlohmann/json.hpp>

namespace fs = std::filesystem;
using json = nlohmann::json;

uintmax_t get_directory_size(const fs::path& dir) {
    uintmax_t size = 0;
    for (const auto& entry : fs::recursive_directory_iterator(dir)) {
        if (fs::is_regular_file(entry)) {
            size += fs::file_size(entry);
        }
    }
    return size;
}

int main() {
    std::string base = "/Users/jeongjunghwan/Downloads";
    std::vector<std::pair<std::string, uintmax_t>> dirs;

    for (const auto& entry : fs::directory_iterator(base)) {
        if (fs::is_directory(entry)) {
            auto size = get_directory_size(entry.path());
            dirs.emplace_back(entry.path().filename().string(), size);
        }
    }

    std::sort(dirs.begin(), dirs.end(), [](auto& a, auto& b) {
        return a.second > b.second;
    });

    json output;
    for (const auto& [name, size] : dirs) {
        output[name] = size;
    }

    std::cout << output.dump(4) << std::endl;
}
```

💡 nlohmann/json은 인기 있는 C++ JSON 라이브러리입니다. 설치가 필요해요.


## ☕ Java 예시
```java
import java.io.File;
import java.nio.file.*;
import java.util.*;
import org.json.JSONObject;

public class Main {
    public static long getDirectorySize(Path dir) throws Exception {
        final long[] size = {0};
        Files.walk(dir)
            .filter(Files::isRegularFile)
            .forEach(p -> {
                try {
                    size[0] += Files.size(p);
                } catch (Exception e) {}
            });
        return size[0];
    }

    public static void main(String[] args) throws Exception {
        File baseDir = new File("/Users/jeongjunghwan/Downloads");
        File[] subDirs = baseDir.listFiles(File::isDirectory);
        List<Map.Entry<String, Long>> dirSizes = new ArrayList<>();

        for (File dir : subDirs) {
            long size = getDirectorySize(dir.toPath());
            dirSizes.add(Map.entry(dir.getName(), size));
        }

        dirSizes.sort((a, b) -> Long.compare(b.getValue(), a.getValue()));

        JSONObject json = new JSONObject();
        for (var entry : dirSizes) {
            json.put(entry.getKey(), entry.getValue());
        }

        System.out.println(json.toString(4));
    }
}
```


## 🐍 Python 예시
```python
import os
import json

def get_directory_size(path):
    total = 0
    for root, dirs, files in os.walk(path):
        for f in files:
            fp = os.path.join(root, f)
            if os.path.isfile(fp):
                total += os.path.getsize(fp)
    return total

base = "/Users/jeongjunghwan/Downloads"
dirs = []

for name in os.listdir(base):
    full_path = os.path.join(base, name)
    if os.path.isdir(full_path):
        size = get_directory_size(full_path)
        dirs.append((name, size))

# 정렬 (크기 기준 내림차순)
dirs.sort(key=lambda x: x[1], reverse=True)

# JSON 출력
output = {name: size for name, size in dirs}
print(json.dumps(output, indent=4))
```
---

















