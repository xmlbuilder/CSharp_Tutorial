# **C#의 DeflateStream**
데이터를 DEFLATE 알고리즘으로 압축/해제하는 전형적인 패턴입니다.
아래에서 개념, 동작 방식, 그리고 Java·C++·Python에서의 대응 방법까지 정리해 드릴게요.

## 1. DeflateStream 개념
- DEFLATE: LZ77 + 허프만 코딩을 결합한 무손실 압축 알고리즘.
- DeflateStream: .NET에서 DEFLATE 알고리즘을 사용해 스트림을 압축(CompressionMode.Compress)하거나 해제(CompressionMode.Decompress)하는 래퍼 클래스.
- 특징:
- 파일, 메모리, 네트워크 스트림 등 어떤 Stream에도 적용 가능.
- GZip과 달리 헤더/푸터 없이 순수 DEFLATE 데이터만 처리.
- Stream을 감싸서 동작하므로, 내부적으로는 바이트 단위로 읽고 쓰는 구조.

## 2. 첫 번째 예제 (바이트 0~99 압축/해제)
using (Stream s = File.Create("compressed.bin"))
using (Stream ds = new DeflateStream(s, CompressionMode.Compress))
    for (byte i = 0; i < 100; i++)
        ds.WriteByte(i);


- compressed.bin 파일 생성 → DeflateStream으로 감싸서 압축 모드로 씀.
- 0~99 바이트를 순차적으로 기록.
- 내부적으로 DEFLATE 알고리즘이 적용되어 압축된 바이트가 파일에 저장됨.
```csharp
using (Stream s = File.OpenRead("compressed.bin"))
using (Stream ds = new DeflateStream(s, CompressionMode.Decompress))
    for (byte i = 0; i < 100; i++)
        Console.WriteLine(ds.ReadByte());
```

- 파일을 열고 DeflateStream을 해제 모드로 감쌈.
- 압축을 풀면서 0~99를 출력.

### 3. 두 번째 예제 (문자열 압축/해제)
```csharp
string[] words = "The quick fox jumps over the lazy dog".Split();
Random random = new Random();
using (Stream s = File.Create(@"E:\temp\test.bin"))
using (Stream ds = new DeflateStream(s, CompressionMode.Compress))
using (TextWriter w = new StreamWriter(ds))
{
    for (int i = 0; i < 1000; i++)
        await w.WriteAsync(words[random.Next(words.Length)] + " ");
}
```

- 무작위 단어 1000개를 생성해 압축 저장.
- StreamWriter를 DeflateStream 위에 올려서 텍스트를 바로 압축 가능.
```csharp
using (Stream s = File.OpenRead(@"E:\temp\test.bin"))
using (Stream ds = new DeflateStream(s, CompressionMode.Decompress))
using (TextReader r = new StreamReader(ds))
{
    Console.WriteLine(await r.ReadToEndAsync());
}
```

- 압축된 파일을 읽어 DeflateStream으로 해제.
- StreamReader로 감싸서 텍스트를 그대로 읽음.

## 샘플 소스
```csharp
using (Stream s = File.Create ("compressed.bin"))
    using (Stream ds = new DeflateStream (s, CompressionMode.Compress))
        for (byte i = 0; i < 100; i++)
        ds.WriteByte (i);

using (Stream s = File.OpenRead ("compressed.bin"))
    using (Stream ds = new DeflateStream (s, CompressionMode.Decompress))
        for (byte i = 0; i < 100; i++)
        Console.WriteLine (ds.ReadByte()); // Writes 0 to 99

namespace StreamTest
{
    internal class Program
    {
        public static async Task Main(string[] args)
        {
            string[] words = "The quick fox jumps over the lazy dog".Split();
            Random random = new Random();
            using (Stream s = File.Create(@"E:\temp\test.bin"))
            {
                using (Stream ds = new DeflateStream(s, CompressionMode.Compress))
                {
                    using (TextWriter w = new StreamWriter(ds)){
                        for (int i = 0; i < 1000; i++)
                        {
                            await w.WriteAsync(words[random.Next(words.Length)] + " ");
                        }
                    }
                }
            }

            Console.WriteLine(new FileInfo(@"E:\temp\test.bin").Length);
            using (Stream s = File.OpenRead(@"E:\temp\test.bin"))
            {
                using (Stream ds = new DeflateStream(s, CompressionMode.Decompress))
                {
                    using (TextReader r = new StreamReader(ds))
                    {
                        Console.WriteLine(await r.ReadToEndAsync());
                    }
                    
                }
            }
        }
        
    }
}

```


## 4. 다른 언어에서의 대응
| 언어   | DEFLATE 압축 클래스/함수                          | DEFLATE 해제 클래스/함수                           | 표준 라이브러리 여부 | 비고 |
|--------|---------------------------------------------------|-----------------------------------------------------|----------------------|------|
| C#     | `System.IO.Compression.DeflateStream` (Compress)  | `System.IO.Compression.DeflateStream` (Decompress)  | ✔ (.NET 내장)        | GZip 헤더 없이 순수 DEFLATE 처리 |
| Java   | `java.util.zip.DeflaterOutputStream`              | `java.util.zip.InflaterInputStream`                 | ✔ (JDK 내장)         | `Deflater`/`Inflater`로 세부 설정 가능 |
| C++    | `zlib` 라이브러리의 `deflate()`                   | `zlib` 라이브러리의 `inflate()`                      | ✖ (외부 라이브러리)  | 표준 C++에는 없음, zlib 별도 설치 필요 |
| Python | `zlib.compress()` / `zlib.compressobj()`          | `zlib.decompress()` / `zlib.decompressobj()`        | ✔ (표준 라이브러리)  | `gzip` 모듈로 GZip 헤더 포함 버전도 가능 |



## Java 예시
```java
import java.io.*;
import java.util.zip.*;

try (FileOutputStream fos = new FileOutputStream("compressed.bin");
     DeflaterOutputStream dos = new DeflaterOutputStream(fos)) {
    for (int i = 0; i < 100; i++) {
        dos.write(i);
    }
}

try (FileInputStream fis = new FileInputStream("compressed.bin");
     InflaterInputStream iis = new InflaterInputStream(fis)) {
    for (int i = 0; i < 100; i++) {
        System.out.println(iis.read());
    }
}
```


Python 예시

```python
import zlib

# 압축
data = bytes(range(100))
compressed = zlib.compress(data)
with open("compressed.bin", "wb") as f:
    f.write(compressed)

# 해제
with open("compressed.bin", "rb") as f:
    compressed_data = f.read()
decompressed = zlib.decompress(compressed_data)
print(list(decompressed))
```


## 💡 정리
- C# DeflateStream ↔ Java DeflaterOutputStream/InflaterInputStream ↔ Python zlib
- C++은 표준에 없지만 zlib 라이브러리로 동일 구현 가능.
- DEFLATE는 GZip과 달리 헤더가 없으므로, 서로 다른 언어 간 교환 시 순수 DEFLATE 포맷을 사용해야 호환됨.

---


## 📦 포맷 주의사항
- DEFLATE는 GZip과 달리 헤더/푸터가 없음.
- C# DeflateStream은 기본적으로 **zlib 헤더(2바이트) + DEFLATE 데이터 + Adler-32 체크섬(4바이트)**를 포함합니다.
- Java DeflaterOutputStream과 Python zlib.compress()도 기본적으로 같은 zlib 래퍼를 사용하므로 호환됩니다.
- 세 언어 모두 압축 레벨과 포맷을 맞추면 바로 호환 가능.

## 🟦 C# 예제
```csharp
using System;
using System.IO;
using System.IO.Compression;
using System.Text;

class Program {
    static void Main() {
        string text = "Hello DEFLATE cross-language!";

        // 압축
        byte[] inputBytes = Encoding.UTF8.GetBytes(text);
        byte[] compressed;
        using (var ms = new MemoryStream())
        using (var ds = new DeflateStream(ms, CompressionMode.Compress, true))
        {
            ds.Write(inputBytes, 0, inputBytes.Length);
            ds.Close(); // 반드시 닫아야 압축 마무리됨
            compressed = ms.ToArray();
        }
        File.WriteAllBytes("data.deflate", compressed);

        // 해제
        byte[] readBytes = File.ReadAllBytes("data.deflate");
        using (var ms = new MemoryStream(readBytes))
        using (var ds = new DeflateStream(ms, CompressionMode.Decompress))
        using (var sr = new StreamReader(ds, Encoding.UTF8))
        {
            Console.WriteLine(sr.ReadToEnd());
        }
    }
}
```



## 🟨 Java 예제
```java
import java.io.*;
import java.nio.file.*;
import java.util.zip.*;

public class DeflateTest {
    public static void main(String[] args) throws IOException {
        String text = "Hello DEFLATE cross-language!";

        // 압축
        byte[] inputBytes = text.getBytes("UTF-8");
        try (FileOutputStream fos = new FileOutputStream("data.deflate");
             DeflaterOutputStream dos = new DeflaterOutputStream(fos, new Deflater(Deflater.DEFAULT_COMPRESSION, false))) {
            dos.write(inputBytes);
        }

        // 해제
        byte[] compressed = Files.readAllBytes(Paths.get("data.deflate"));
        try (InflaterInputStream iis = new InflaterInputStream(new ByteArrayInputStream(compressed), new Inflater(false));
             InputStreamReader isr = new InputStreamReader(iis, "UTF-8");
             BufferedReader br = new BufferedReader(isr)) {
            System.out.println(br.readLine());
        }
    }
}
```

new Deflater(..., false)와 new Inflater(false) → false는 zlib 래퍼 사용(순수 DEFLATE 아님).
C# DeflateStream 기본값과 동일.


## 🐍 Python 예제
```python
import zlib

text = "Hello DEFLATE cross-language!"

# 압축
compressed = zlib.compress(text.encode('utf-8'))
with open("data.deflate", "wb") as f:
    f.write(compressed)

# 해제
with open("data.deflate", "rb") as f:
    compressed_data = f.read()
decompressed = zlib.decompress(compressed_data).decode('utf-8')
print(decompressed)
```


## 🔗 호환 테스트 방법
- C#에서 압축 → Java/Python에서 해제
- Java에서 압축 → C#/Python에서 해제
- Python에서 압축 → C#/Java에서 해제
세 경우 모두 "Hello DEFLATE cross-language!"가 출력되면 성공입니다.

---
