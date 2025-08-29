# C#에서의 기준 동작
- 기본 UTF-8 (BOM 없음): File.CreateText()는 UTF-8(대부분 BOM 없음)을 쓰는 StreamWriter를 반환합니다. 
WriteLine("but-")는 Windows에서 기본으로 \r\n을 붙입니다. 
그래서 바이트는 62 75 74 2D 0D 0A(10진수: 98 117 116 45 13 10).
- UTF-16LE + BOM: new StreamWriter(stream, Encoding.Unicode)는 UTF-16LE이며, 선두에 BOM FF FE(10진수 255 254)를 씁니다. 
ASCII 범위 문자는 낮은 바이트에 코드가 들어가고 높은 바이트는 00이 됩니다. 줄바꿈도 각각 2바이트씩 기록됩니다.
- BOM 자동 인식(읽기): File.OpenText()/new StreamReader(path)는 BOM을 자동 감지(UTF-8/16/32)해서 올바른 디코딩을 시도합니다.
- BOM 강제 지정: UTF-8에 BOM을 쓰고 싶다면 new StreamWriter(path, false, new UTF8Encoding(true))처럼 명시합니다.
```csharp
// UTF-8 (BOM 없음, 기본)
using (var w = File.CreateText("but.txt"))
    w.WriteLine("but-");
```

```csharp
// UTF-8 (BOM 포함)
using (var w = new StreamWriter("but-utf8-bom.txt", false, new UTF8Encoding(encoderShouldEmitUTF8Identifier: true)))
    w.WriteLine("but-");
```

```csharp
// UTF-16LE (BOM 포함)
using (var s = File.Create("but-utf16le.txt"))
using (var w = new StreamWriter(s, Encoding.Unicode))
    w.WriteLine("but-");
```

```csharp
// 원시 바이트 확인
foreach (byte b in File.ReadAllBytes("but-utf16le.txt"))
    Console.WriteLine(b);
```

## 샘플 코드

```csharp
using (TextWriter w = File.CreateText ("but.txt")) // Use default UTF-8
    w.WriteLine ("but-"); // encoding.

using (Stream s = File.OpenRead ("but.txt"))
    for (int b; (b = s.ReadByte()) > −1;)
    Console.WriteLine (b);
    
    98 // b
    117 // u
    116 // t
    45 // -
    13 // <CR>
    10 // <LF>

using (Stream s = File.Create ("but.txt"))
    using (TextWriter w = new StreamWriter (s, Encoding.Unicode))
    w.WriteLine ("but-");

foreach (byte b in File.ReadAllBytes ("but.txt"))
    Console.WriteLine (b);

The output is then:
255 // Byte-order mark 1
254 // Byte-order mark 2
98 // 'b' byte 1
0 // 'b' byte 2
117 // 'u' byte 1
0 // 'u' byte 2
116 // 't' byte 1
0 // 't' byte 2
45 // '-' byte 1
0 // '-' byte 2
13 // <CR> byte 1
0 // <CR> byte 2
10 // <LF> byte 1
0 // <LF> byte 2

```


## C++에서의 인코딩 제어
C++ 표준 스트림은 “바이트 덩어리”만 다룹니다. 인코딩은 사용자가 직접 선택·변환해서 바이트로 써야 합니다. BOM도 자동으로 붙지 않습니다.
핵심 포인트
- 기본 상태: std::ofstream/std::ifstream은 바이트를 있는 그대로 씁니다/읽습니다. 인코딩 개념이 없습니다.
- UTF-8 쓰기: 텍스트를 UTF-8 바이트로 변환해 기록하세요. ASCII만 포함되면 그대로가 UTF-8입니다. UTF-8 BOM을 원하면 선두에 EF BB BF를 직접 써야 합니다.
- UTF-16LE 쓰기: 선두에 FF FE를 쓰고, 각 코드 유닛을 리틀엔디언 2바이트로 기록합니다(ASCII는 <코드, 00>).
- 줄바꿈: '\n'은 단일 바이트 0x0A입니다. CRLF를 원하면 "\r\n"을 직접 쓰세요.
- 문자열 변환: 실전에서는 ICU, iconv 등 라이브러리로 유니코드 변환을 처리하는 것이 안전합니다. 
  표준의 codecvt 계열은 폐지(deprecated)되어 대안 선택이 필요합니다.

```cpp
#include <fstream>
#include <vector>
#include <cstdint>
#include <iostream>

// UTF-8 (BOM 없음)
void write_utf8_no_bom() {
    std::ofstream out("but-utf8.txt", std::ios::binary);
    out << "but-\r\n";
}
```

```cpp
// UTF-8 (BOM 포함)
void write_utf8_bom() {
    std::ofstream out("but-utf8-bom.txt", std::ios::binary);
    const unsigned char bom[] = {0xEF, 0xBB, 0xBF};
    out.write(reinterpret_cast<const char*>(bom), 3);
    out << "but-\r\n";
}
```

```cpp
// UTF-16LE (BOM 포함)
void write_utf16le_bom() {
    std::ofstream out("but-utf16le.txt", std::ios::binary);
    const unsigned char bom[] = {0xFF, 0xFE};
    out.write(reinterpret_cast<const char*>(bom), 2);
    // 'b','u','t','-','\r','\n'를 16비트 리틀엔디언으로
    const uint16_t data[] = {u'b', u'u', u't', u'-', u'\r', u'\n'};
    out.write(reinterpret_cast<const char*>(data), sizeof(data));
}
```

```cpp
// 원시 바이트 덤프
void dump_bytes(const char* path) {
    std::ifstream in(path, std::ios::binary);
    std::vector<unsigned char> bytes((std::istreambuf_iterator<char>(in)), {});
    for (unsigned char b : bytes) std::cout << int(b) << "\n";
}
```


## Java에서의 인코딩 제어

Java는 Reader/Writer가 문자 스트림을 다루고, 인코딩은 보통 InputStreamReader/OutputStreamWriter에서 명시합니다.
핵심 포인트
- 기본 문자셋: Java 18+는 기본 문자셋이 UTF-8입니다. 그래도 이식성을 위해 항상 문자셋을 명시하는 것을 권장합니다.
- FileReader/FileWriter 지양: 플랫폼 기본 문자셋을 쓰므로 피하고, InputStreamReader/OutputStreamWriter에 StandardCharsets.UTF_8 등을 넘기세요.
- BOM 처리:
- UTF-8: BOM을 자동 제거하지 않습니다. 파일에 UTF-8 BOM이 있으면 첫 문자 \uFEFF로 읽힐 수 있습니다(수동으로 제거하거나 Apache Commons IO의 BOMInputStream 사용).
- UTF-16: "UTF-16"로 열면 BOM을 보고 엔디안을 인식하며, BOM은 일반적으로 데이터로 반환되지 않습니다. "UTF-16LE/BE"는 고정 엔디안이며 BOM을 쓰지 않습니다.
- 줄바꿈: BufferedWriter.newLine()은 System.lineSeparator()를 사용합니다. CRLF를 강제하려면 "\r\n"을 직접 쓰세요.
```java
import java.io.*;
import java.nio.charset.StandardCharsets;

class Main {
    public static void main(String[] args) throws Exception {
        // UTF-8 (BOM 없음)
        try (Writer w = new OutputStreamWriter(new FileOutputStream("but-utf8.txt"), StandardCharsets.UTF_8)) {
            w.write("but-\r\n");
        }

        // UTF-8 (BOM 포함: \uFEFF 수동 기록)
        try (Writer w = new OutputStreamWriter(new FileOutputStream("but-utf8-bom.txt"), StandardCharsets.UTF_8)) {
            w.write('\uFEFF');
            w.write("but-\r\n");
        }

        // UTF-16LE (BOM 없이 고정 엔디안)
        try (Writer w = new OutputStreamWriter(new FileOutputStream("but-utf16le.txt"), StandardCharsets.UTF_16LE)) {
            w.write("but-\r\n");
        }

        // UTF-16 (BOM 포함, 엔디안 자동)
        try (Writer w = new OutputStreamWriter(new FileOutputStream("but-utf16.txt"), StandardCharsets.UTF_16)) {
            w.write("but-\r\n");
        }

        // 바이트 덤프
        try (InputStream in = new FileInputStream("but-utf16.txt")) {
            int b; while ((b = in.read()) != -1) System.out.println(b);
        }
    }
}
```


## Python에서의 인코딩 제어
Python의 텍스트 I/O는 io.TextIOWrapper가 담당하며, open(..., encoding=...)으로 명시합니다.
핵심 포인트
- 기본 인코딩: 환경/버전에 따라 달라질 수 있으므로 항상 encoding=을 명시하세요(실무 권장: utf-8).
- UTF-8: encoding="utf-8"은 BOM 없이 씁니다. encoding="utf-8-sig"는 쓰기 시 BOM을 붙이고, 읽기 시 있으면 자동 제거합니다.
- UTF-16: encoding="utf-16"은 BOM을 쓰고 읽을 때 BOM 기준으로 엔디안을 자동 인식합니다. "utf-16-le/be"는 BOM 없이 고정 엔디안.
- 줄바꿈: newline=None(기본)은 읽기에서 다양한 줄바꿈을 \n으로 통일하고, 쓰기에서 \n을 OS 기본 줄바꿈으로 변환합니다. 변환을 원치 않으면 newline="", CRLF를 강제하려면 newline="\r\n".
```python
# UTF-8 (BOM 없음)
with open("but-utf8.txt", "w", encoding="utf-8", newline="\r\n") as f:
    f.write("but-")
    f.write("\n")  # 위에서 CRLF로 강제했으므로 실제로는 \r\n 기록
```
```python
# UTF-8 (BOM 포함)
with open("but-utf8-bom.txt", "w", encoding="utf-8-sig", newline="\r\n") as f:
    f.write("but-\n")
```
```python
# UTF-16 (BOM 포함, 엔디안 자동)
with open("but-utf16.txt", "w", encoding="utf-16", newline="") as f:
    f.write("but-\r\n")
```
```python
# 원시 바이트 출력
with open("but-utf16.txt", "rb") as f:
    for b in f.read():
        print(b)
```
## 요약 비교
| 항목                | C#                                                    | C++                                                | Java                                                   | Python                                                 |
|---------------------|-------------------------------------------------------|----------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
| **기본 텍스트 인코딩** | UTF-8 (BOM 없음, .NET Core/5+ 기준)                     | 없음(바이트 스트림, 인코딩 직접 처리)               | Java 18+ UTF-8, 그 외 플랫폼 기본                       | 환경/버전 따라 다름 → `encoding=` 명시 권장             |
| **UTF-8 BOM 기본**    | 기본 안 붙음 (`UTF8Encoding(true)`로 붙일 수 있음)       | 기본 안 붙음, 필요 시 `EF BB BF` 직접 기록           | 기본 안 붙음, BOM 쓰려면 `\uFEFF` 수동 기록             | `utf-8`은 안 붙음, `utf-8-sig`는 BOM 붙임               |
| **UTF-16 쓰기**       | `Encoding.Unicode` = UTF-16LE + BOM                   | BOM·바이트 직접 기록 필요                           | `"UTF-16"`은 BOM 포함, `"UTF-16LE/BE"`는 무BOM          | `encoding="utf-16"`은 BOM 포함, `utf-16-le/be`는 무BOM  |
| **UTF-8 BOM 읽기**    | BOM 자동 감지·무시                                    | 직접 처리 필요                                     | 자동 제거 안 함(문자 `\uFEFF`로 읽힘)                   | `utf-8`은 남음, `utf-8-sig`는 제거                      |
| **줄바꿈 기본**       | `WriteLine` → OS 기본(`\r\n` on Windows)               | `'\n'` 그대로 기록, CRLF 원하면 직접 `\r\n` 사용     | `newLine()` → OS 기본, `'\n'`은 LF                     | `newline=None` 변환, `newline="\n"` 등 지정 가능         |


팁: BOM이 필요한 경우는 주로 “구형 Windows 툴/에디터와의 호환” 목적입니다. 
현대 툴 체인에서는 UTF-8 BOM 없는 파일이 가장 이식성이 좋습니다.


## 실무 가이드
- 명시가 왕도: 항상 인코딩을 코드를 통해 명시하세요.
- C#: new StreamWriter(path, false, Encoding.UTF8) 또는 new UTF8Encoding(true)
- Java: new InputStreamReader(..., StandardCharsets.UTF_8)
- Python: open(..., encoding="utf-8")
- UTF-8 우선: 특별한 이유가 없으면 UTF-8(BOM 없음)을 기본으로 삼으세요.
- BOM 대응 읽기: “출처 불명”의 파일을 읽을 땐 BOM 탐지/제거 전략을 두세요.
- C#: StreamReader 기본 동작으로 충분한 경우가 많음.
- Java: UTF-8 BOM은 수동 제거(\uFEFF 체크) 또는 라이브러리 사용.
- Python: encoding="utf-8-sig"로 안전하게 시작.
- 줄바꿈 일관성: 크로스플랫폼 저장물은 \n 고정(툴이 CRLF를 요구하면 그때만 변환).
- C#: writer.NewLine = "\n";
- Java: w.write("\n") 또는 빌드 단계에서 정규화
- Python: newline="\n"
- C++는 변환 라이브러리 사용: ICU, iconv 등 검증된 라이브러리로 유니코드 변환을 맡기고, 스트림은 바이트만 다루게 하세요.
