# 📘 C# – StreamReader / StreamWriter 정리
| 클래스         | 용도         | 사용 방식 예시                                      | 특징 및 설명                                      |
|----------------|--------------|-----------------------------------------------------|---------------------------------------------|
| StreamReader   | 파일 읽기    | `using (StreamReader sr = new StreamReader(...))`  | 텍스트 파일을 줄 단위로 읽음                      |
| StreamWriter   | 파일 쓰기    | `using (StreamWriter sw = new StreamWriter(...))`  | 텍스트 파일에 줄 단위로 쓰기 가능                 |
| using 키워드   | 자원 관리    | `using (...) { ... }`                              | 파일 스트림을 자동으로 닫고 메모리 누수 방지       |


## ✅ C# 샘플 코드
```csharp
// 파일 쓰기
using (FileStream fs = File.Create("test.txt"))
using (TextWriter writer = new StreamWriter(fs))
{
    writer.WriteLine("Line1");
    writer.WriteLine("Line2");
}

// 파일 읽기
using (FileStream fs = File.OpenRead("test.txt"))
using (TextReader reader = new StreamReader(fs))
{
    Console.WriteLine(reader.ReadLine()); // Line1
    Console.WriteLine(reader.ReadLine()); // Line2
}

// 다양한 타입 읽기
using (StreamReader sr = new StreamReader(new FileStream("test.txt", FileMode.Open, FileAccess.Read)))
{
    int value = int.Parse(sr.ReadLine());
    float fval = float.Parse(sr.ReadLine());
    string str1 = sr.ReadLine();
    Console.WriteLine($"{value} {fval} {str1}");
}
```

## 📗 C++ – 줄 단위 파일 처리
```cpp
#include <fstream>
#include <iostream>
#include <string>

int main() {
    std::ofstream outFile("test.txt");
    outFile << "Line1\nLine2\n";
    outFile.close();

    std::ifstream inFile("test.txt");
    std::string line;
    while (std::getline(inFile, line)) {
        std::cout << line << std::endl;
    }
    inFile.close();
}
```

- std::ofstream으로 쓰기, std::ifstream으로 읽기
- std::getline()으로 줄 단위 처리

## 📙 Java – 줄 단위 파일 처리
```java
import java.io.*;
import java.nio.file.*;
import java.util.stream.*;

public class FileExample {
    public static void main(String[] args) throws IOException {
        // 쓰기
        BufferedWriter writer = Files.newBufferedWriter(Paths.get("test.txt"));
        writer.write("Line1\nLine2");
        writer.close();

        // 읽기
        Files.lines(Paths.get("test.txt"))
             .forEach(System.out::println);
    }
}
```

- Files.lines()는 Stream<String> 반환
- BufferedWriter로 줄 단위 쓰기

## 🐍 Python – 줄 단위 파일 처리

### 쓰기
```python
with open("test.txt", "w") as f:
    f.write("Line1\n")
    f.write("Line2\n")
```
### 읽기
```python
with open("test.txt", "r") as f:
    for line in f:
        print(line.strip())
```

- with open(...)으로 자원 자동 관리
- for line in f로 줄 단위 읽기


## 📄 줄 단위 파일 처리 – 언어별 요약 비교

| 언어   | 읽기 방식                  | 쓰기 방식                  | 자원 관리 방식           | 주요 특징                          |
|--------|----------------------------|----------------------------|--------------------------|------------------------------------|
| C#     | `StreamReader.ReadLine()` | `StreamWriter.WriteLine()` | `using` 블록             | 강력한 타입 처리, LINQ 활용 가능   |
| C++    | `std::getline()`           | `std::ofstream <<`         | 명시적 `close()` 또는 RAII | 직접 제어 가능, 성능 우수          |
| Java   | `Files.lines()`            | `BufferedWriter.write()`   | 자동 또는 try-with-resources | Stream API 활용, 병렬 처리 가능    |
| Python | `for line in file`         | `file.write()`             | `with open(...)`         | 간결하고 직관적, 빠른 프로토타이핑 |

---

## ✅ C#에서 using과 try를 써야 하는 이유
### 🔹 using – 자원 자동 해제
- StreamReader, StreamWriter, FileStream 등은 관리되지 않는 리소스(파일 핸들, 메모리 등)를 사용합니다.
- using을 쓰면 블록이 끝날 때 자동으로 Dispose()가 호출되어 파일이 닫히고 자원이 해제됩니다.
- 예외가 발생해도 안전하게 자원을 정리할 수 있어 메모리 누수 방지에 효과적입니다.
```csharp
using (var reader = new StreamReader("test.txt"))
{
    string line = reader.ReadLine();
    Console.WriteLine(line);
} // 여기서 reader.Dispose() 자동 호출됨
```

### 🔹 try-catch – 예외 처리
- 파일이 없거나 권한이 없을 때 IOException, UnauthorizedAccessException 등이 발생할 수 있습니다.
- try-catch를 사용하면 프로그램이 중단되지 않고 적절한 메시지를 출력하거나 대체 로직을 실행할 수 있습니다.
```java
try
{
    using (var reader = new StreamReader("test.txt"))
    {
        Console.WriteLine(reader.ReadLine());
    }
}
catch (Exception ex)
{
    Console.WriteLine($"파일 처리 중 오류 발생: {ex.Message}");
}

```

## 🧹 언어별 자원 해제 방식 비교

| 언어   | 자원 해제 방식               | 키워드 / 구조                | 설명                                                         |
|--------|------------------------------|------------------------------|--------------------------------------------------------------|
| C#     | 명시적 해제 (`Dispose`)      | `using`, `using var`         | `IDisposable` 구현 객체를 자동으로 해제 (`Dispose()` 호출)   |
| C++    | RAII (Resource Acquisition Is Initialization) | 생성자/소멸자               | 객체 소멸 시 자원 자동 해제. `std::ifstream`, `std::ofstream` 등 |
| Java   | 자동 해제 (`AutoCloseable`)  | `try-with-resources`         | `AutoCloseable` 구현 객체를 블록 종료 시 자동으로 `close()` 호출 |
| Python | 컨텍스트 매니저              | `with`                       | `__enter__`, `__exit__` 메서드로 자원 자동 해제 (`file`, `open`) |



## 🔍 핵심 요약
- using은 자원 누수 방지를 위한 안전장치입니다.
- try는 예외 발생 시 프로그램 안정성을 확보합니다.
- 두 가지를 함께 쓰면 안전하고 견고한 파일 처리 코드가 됩니다.
---

