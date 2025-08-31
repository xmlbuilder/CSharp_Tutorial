# C# Lazily reading a file line-by-line 
C#의 File.ReadLines처럼 파일을 한 줄씩 지연(lazy) 읽기하는 패턴.
이 방식은 메모리에 파일 전체를 올리지 않고, 필요한 시점에 한 줄씩 읽어 처리할 수 있어 대용량 파일에 유리합니다.


## C#
```csharp
using System.Text;
class Program
{
    public static async Task Main(String[] args)
    {
        IEnumerable<string> allLines = File.ReadLines("filename.txt", Encoding.Default);
        string text = "model";
        using (StreamWriter streamWriter = new StreamWriter("filename.text", true))
        {
            await streamWriter.WriteLineAsync(text);
        }
    }
}
```

## 1️⃣ C++ — std::ifstream + std::getline
```cpp
#include <iostream>
#include <fstream>
#include <string>

int main() {
    std::ifstream file("filename.txt");
    if (!file) {
        std::cerr << "파일을 열 수 없습니다.\n";
        return 1;
    }

    std::string line;
    while (std::getline(file, line)) { // 한 줄씩 읽기
        std::cout << line << "\n";
    }

    // 파일에 append
    std::ofstream out("filename.txt", std::ios::app);
    out << "model\n";
}
```

- std::getline은 한 줄씩 읽어 메모리 사용을 최소화합니다.
- 쓰기는 std::ofstream에 std::ios::app 플래그로 append 모드.

## 2️⃣ Java — Files.lines (Stream API)
```jva
import java.io.*;
import java.nio.file.*;
import java.nio.charset.StandardCharsets;
import java.util.stream.Stream;

public class Main {
    public static void main(String[] args) {
        Path path = Paths.get("filename.txt");

        // Lazy line-by-line 읽기
        try (Stream<String> lines = Files.lines(path, StandardCharsets.UTF_8)) {
            lines.forEach(System.out::println);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // Append 쓰기
        try (BufferedWriter writer = Files.newBufferedWriter(path, StandardCharsets.UTF_8, StandardOpenOption.APPEND)) {
            writer.write("model");
            writer.newLine();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

- Files.lines는 내부적으로 lazy하게 스트림을 생성합니다.
- try-with-resources로 자동 close.

## 3️⃣ Python — 파일 객체 이터레이터
```python
# Lazy line-by-line 읽기
with open("filename.txt", "r", encoding="utf-8") as f:
    for line in f:  # 한 줄씩 읽기
        print(line.strip())

# Append 쓰기
with open("filename.txt", "a", encoding="utf-8") as f:
    f.write("model\n")

```

- Python의 파일 객체는 자체적으로 이터레이터를 구현해 한 줄씩 읽습니다.
- strip()으로 개행 제거.

## 📌 특징 비교
| 언어    | Lazy 읽기 방식                   | 장점                                   | 쓰기(Append) 방식                   |
|---------|-----------------------------------|----------------------------------------|--------------------------------------|
| C#      | File.ReadLines                    | 메모리 절약, IEnumerable로 지연 처리   | StreamWriter + true 플래그           |
| C++     | std::getline                      | 표준 라이브러리, 간단한 구현           | std::ofstream + std::ios::app        |
| Java    | Files.lines (Stream)              | 스트림 API와 결합 가능                 | Files.newBufferedWriter + APPEND     |
| Python  | 파일 객체 이터레이터 (for line)   | 문법 간결, 기본 기능                   | open(..., "a")                        |



---


# 💡 비동기 구현 간단 예시
## C#
```csharp
using (StreamReader reader = new StreamReader("filename.txt"))
{
    string? line;
    while ((line = await reader.ReadLineAsync()) != null)
    {
        Console.WriteLine(line);
    }
}
```        

## C++ 비동기 예시 (std::async 활용)
```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <future>

void read_file_line_by_line(const std::string& filename) {
    std::ifstream file(filename);
    std::string line;
    while (std::getline(file, line)) {
        std::cout << line << "\n";
    }
}

int main() {
    auto fut = std::async(std::launch::async, read_file_line_by_line, "filename.txt");

    // Append 쓰기
    std::ofstream out("filename.txt", std::ios::app);
    out << "model\n";

    fut.get(); // 비동기 읽기 완료 대기
}

- std::async + std::future로 읽기 작업을 비동기로 실행

```

## Java (NIO.2)
```java
AsynchronousFileChannel channel = AsynchronousFileChannel.open(
    Paths.get("filename.txt"), StandardOpenOption.READ);

ByteBuffer buffer = ByteBuffer.allocate(1024);
channel.read(buffer, 0, buffer, new CompletionHandler<Integer, ByteBuffer>() {
    public void completed(Integer result, ByteBuffer buf) {
        buf.flip();
        System.out.println(StandardCharsets.UTF_8.decode(buf));
    }
    public void failed(Throwable exc, ByteBuffer buf) {
        exc.printStackTrace();
    }
});

```

## Python (aiofiles)
```python
import aiofiles
import asyncio

async def read_file():
    async with aiofiles.open("filename.txt", mode="r", encoding="utf-8") as f:
        async for line in f:
            print(line.strip())

asyncio.run(read_file())

```


