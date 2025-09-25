
# BufferedStream
버퍼를 두고 읽는 방식인데, 한 바이트만 읽더라도 내부적으로 미리 다량의 데이터를 가져오는 읽기 앞당김(Read-Ahead) 방식 덕분에 I/O 호출을 최소화하는 장점이 있습니다.
이 개념은 C++, Java, Python에도 모두 존재하고, 각각의 표준 라이브러리에서 비슷하게 구현돼 있어요.

## 📘 C# – BufferedStream 핵심
- 역할: 내부 버퍼를 둬서 물리적 디스크 접근 횟수를 줄임.
- BufferedStream은 Stream(예: FileStream) 위에 얹어서 동작.
- 첫 ReadByte() 시 버퍼 크기(예: 20KB)만큼 한 번에 읽어와 메모리에 저장.
- 이후 요청은 버퍼에서 처리하므로 속도가 빨라짐.
```csharp
using (FileStream fs = File.OpenRead("test.txt"))
using (BufferedStream bs = new BufferedStream(fs, 20000))
{
    bs.ReadByte();
    Console.WriteLine(fs.Position); // 20000: 내부적으로 20KB 미리 읽음
}
```



## 📗 C++ – Buffered I/O
C++ 표준 I/O(ifstream, ofstream, fstream)는 기본적으로 버퍼링이 적용됩니다.
또한 std::streambuf나 setbuf()/rdbuf() 등을 통해 버퍼 크기를 제어할 수 있어요.
```cpp
#include <iostream>
#include <fstream>

int main() {
    std::ifstream file("test.txt", std::ios::binary);
    char buffer[20000];
    file.rdbuf()->pubsetbuf(buffer, sizeof(buffer)); // 버퍼 크기 설정
    char c;
    file.get(c);
    std::cout << "첫 문자: " << c << std::endl;
}
```

- 기본 동작: 내부 버퍼에 블록 단위로 읽음.
- 한 글자씩 get() 해도 성능이 떨어지지 않음.

## 📙 Java – BufferedInputStream / BufferedOutputStream
Java의 BufferedInputStream은 내부적으로 디폴트 8KB 버퍼를 사용하지만, 생성자에서 크기를 지정할 수 있습니다.
```java
import java.io.*;

public class Main {
    public static void main(String[] args) throws IOException {
        try (FileInputStream fis = new FileInputStream("test.txt");
             BufferedInputStream bis = new BufferedInputStream(fis, 20000)) {
            bis.read();
            System.out.println("버퍼 크기 20KB로 읽음");
        }
    }
}
```

- 장점: 파일, 네트워크 스트림 모두 사용 가능.
- 읽기 한 번으로 대량 데이터 미리 가져옴.

## 🐍 Python – BufferedReader / BufferedWriter
Python의 open() 함수는 기본적으로 버퍼링 I/O를 제공하며, io 모듈로 직접 제어할 수도 있습니다.
```python
import io

with open("test.txt", "rb", buffering=20000) as f:  # buffering 인자 = 버퍼 크기
    f.read(1)  # 1바이트 읽어도 20KB 버퍼로 미리 읽음
    print(f.tell())  # 내부 파일 포인터 위치 확인

```

- io.BufferedReader / io.BufferedWriter로 세밀한 제어 가능.
- 네트워크, 파일, 파이프 등 다양한 스트림 대상 지원.

### 📊 언어별 Buffered Stream 비교

| 언어   | 주요 클래스/함수                           | 기본 버퍼 크기      | 버퍼 크기 변경 방법                              | 특징 및 사용 목적 |
|--------|---------------------------------------------|---------------------|--------------------------------------------------|------------------|
| C#     | `BufferedStream`        | 4KB~8KB (OS/환경 의존) | 생성자에서 `bufferSize` 지정            | 파일·네트워크 스트림 위에 얹어 I/O 성능 향상 |
| C++    | `std::istream` / `std::ostream` (내장 버퍼) | 구현체 의존 | `pubsetbuf()` 또는 커스텀 `streambuf` 사용       | 기본적으로 버퍼링 적용, 세밀 제어 가능 |
| Java   | `BufferedInputStream`, `BufferedOutputStream` | 8KB    | 생성자 두 번째 인자로 크기 지정      | 모든 Input/OutputStream에 래핑 가능, 범용성 높음 |
| Python | `io.BufferedReader`, `io.BufferedWriter`  | 구현체 의존   | `open(..., buffering=n)` 또는 `io` 모듈 직접 사용 | 간단하고 직관적, 파일·소켓 등 다양한 대상 지원 |


## 💡 정리 
Buffered Stream은 언어와 상관없이 "자주 읽는 작은 단위 요청을 모아 한 번에 처리하여 I/O 성능을 올리는 기술"이라는 공통 철학을 가집니다.
