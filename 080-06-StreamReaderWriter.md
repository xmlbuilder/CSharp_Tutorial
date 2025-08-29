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



