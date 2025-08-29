# 📘TextReader / TextWriter
TextWriter / TextReader를 이용해 텍스트 기반 파일 입출력을 하는 전형적인 패턴입니다.
이 개념은 C++, Java, Python에도 거의 동일하게 존재하며, 각 언어마다 이름과 사용법만 조금씩 다릅니다.

## 📘 C# – TextReader / TextWriter 개념
- TextWriter: 텍스트를 파일, 메모리, 네트워크 스트림 등에 쓰는 추상 클래스. 대표 구현체: StreamWriter, StringWriter
- TextReader: 텍스트를 읽는 추상 클래스. 대표 구현체: StreamReader, StringReader
- File.CreateText() → StreamWriter 반환
- File.OpenText() → StreamReader 반환
```csharp
using (TextWriter w = File.CreateText("data.txt"))
{
    w.WriteLine(123);
    w.WriteLine(true);
}

using (TextReader r = File.OpenText("data.txt"))
{
    int myInt = int.Parse(r.ReadLine());
    bool yes = bool.Parse(r.ReadLine());
}
```


## 📗 C++ – 유사 개념
C++에서는 <fstream>의 ofstream(쓰기), ifstream(읽기)이 TextWriter/TextReader 역할을 합니다.
```cpp
#include <fstream>
#include <string>

int main() {
    // 쓰기
    std::ofstream out("data.txt");
    out << 123 << "\n";
    out << std::boolalpha << true << "\n"; // "true"로 출력
    out.close();

    // 읽기
    std::ifstream in("data.txt");
    int myInt;
    bool yes;
    in >> myInt;
    in >> std::boolalpha >> yes;
}
```

- std::boolalpha → bool을 true/false 문자열로 입출력
- RAII로 파일 자동 닫힘

## 📙 Java – 유사 개념
Java에서는 Writer / Reader 추상 클래스가 C#의 TextWriter / TextReader와 동일한 역할을 합니다.
```java
import java.io.*;

public class Main {
    public static void main(String[] args) throws IOException {
        // 쓰기
        try (Writer w = new FileWriter("data.txt")) {
            w.write("123\n");
            w.write("true\n");
        }

        // 읽기
        try (BufferedReader r = new BufferedReader(new FileReader("data.txt"))) {
            int myInt = Integer.parseInt(r.readLine());
            boolean yes = Boolean.parseBoolean(r.readLine());
        }
    }
}
```

- FileWriter / FileReader는 문자 기반 스트림
- BufferedReader로 성능 향상 가능

## 🐍 Python – 유사 개념
Python에서는 open()이 파일 객체를 반환하며, 이 객체가 텍스트 읽기/쓰기 기능을 모두 가집니다.
```python
# 쓰기
with open("data.txt", "w", encoding="utf-8") as f:
    f.write(f"{123}\n")
    f.write(f"{True}\n")

# 읽기
with open("data.txt", "r", encoding="utf-8") as f:
    my_int = int(f.readline().strip())
    yes = f.readline().strip().lower() == "true"
```

- with open(...) → C#의 using과 동일하게 자원 자동 해제
- 문자열 변환은 str(), int(), bool() 등으로 처리

## 📊 언어별 비교 (Markdown 표)
| 언어   | 쓰기 클래스/함수                | 읽기 클래스/함수                | 특징 |
|--------|---------------------------------|----------------------------------|------|
| C#     | `TextWriter` (`StreamWriter`)  | `TextReader` (`StreamReader`)   | 추상 클래스 기반, `using`으로 자원 해제 |
| C++    | `std::ofstream`                | `std::ifstream`                 | RAII 기반 자동 해제, `<<`/`>>` 연산자 사용 |
| Java   | `Writer` (`FileWriter`)        | `Reader` (`FileReader`)         | 추상 클래스 기반, `try-with-resources` 사용 |
| Python | `open(..., "w")`               | `open(..., "r")`                | 컨텍스트 매니저(`with`)로 자원 해제 |

---




