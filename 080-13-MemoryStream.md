## 📌 C# 예제 설명
```csharp
var ms = new MemoryStream();
using (Stream ds = new DeflateStream(ms, CompressionMode.Compress))
{
    ds.Write(data, 0, data.Length);
}
byte[] compressed = ms.ToArray();
```

- MemoryStream: 메모리에 데이터를 저장하는 스트림.
- DeflateStream: DEFLATE 알고리즘으로 압축/해제.
- 압축 후 ms.ToArray()로 압축된 바이트 배열 획득.
```csharp
var ms1 = new MemoryStream(compressed);
using (Stream ds = new DeflateStream(ms1, CompressionMode.Decompress))
{
    for (int i = 0; i < 1000; i += ds.Read(data2, i, 1000 - i)) ;
}
```

- 압축된 바이트 배열을 다시 MemoryStream에 넣고 해제.
- 원본 데이터 복원.

## 🟦 C++ (zlib + 메모리 버퍼)
C++ 표준에는 MemoryStream이 없지만, 메모리 버퍼를 직접 사용하면 동일한 효과를 낼 수 있습니다.
```cpp
#include <iostream>
#include <vector>
#include <zlib.h>
#include <cstdlib>

int main() {
    std::vector<unsigned char> data(1000);
    for (auto &b : data) b = rand() % 256;

    // 압축
    uLongf compressedSize = compressBound(data.size());
    std::vector<unsigned char> compressed(compressedSize);
    compress(compressed.data(), &compressedSize, data.data(), data.size());
    compressed.resize(compressedSize);
    std::cout << "Compressed size: " << compressed.size() << "\n";

    // 해제
    std::vector<unsigned char> decompressed(1000);
    uLongf decompressedSize = decompressed.size();
    uncompress(decompressed.data(), &decompressedSize, compressed.data(), compressed.size());

    // 출력
    for (auto b : decompressed) std::cout << (int)b << " ";
}
```

- compress() / uncompress()는 zlib의 메모리 기반 압축/해제 함수.
- 파일 없이 메모리에서 바로 처리.

## 🟨 Java (ByteArrayOutputStream + DeflaterOutputStream)
```java
import java.io.*;
import java.util.Random;
import java.util.zip.*;

public class MemoryDeflate {
    public static void main(String[] args) throws IOException {
        byte[] data = new byte[1000];
        new Random().nextBytes(data);

        // 압축
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        try (DeflaterOutputStream dos = new DeflaterOutputStream(baos)) {
            dos.write(data);
        }
        byte[] compressed = baos.toByteArray();
        System.out.println(compressed.length);

        // 해제
        ByteArrayInputStream bais = new ByteArrayInputStream(compressed);
        byte[] data2 = new byte[1000];
        try (InflaterInputStream iis = new InflaterInputStream(bais)) {
            int offset = 0, read;
            while ((read = iis.read(data2, offset, data2.length - offset)) > 0) {
                offset += read;
            }
        }

        for (byte b : data2) System.out.print((b & 0xFF) + " ");
    }
}

```

- ByteArrayOutputStream / ByteArrayInputStream이 C# MemoryStream 역할.
- DeflaterOutputStream / InflaterInputStream이 DeflateStream 역할.

## 🐍 Python (zlib + BytesIO)
```python
import zlib
import io
import random

# 원본 데이터
data = bytes([random.randint(0, 255) for _ in range(1000)])

# 압축
compressed = zlib.compress(data)
print(len(compressed))

# 해제
decompressed = zlib.decompress(compressed)

# 출력
print(" ".join(str(b) for b in decompressed))
```

- io.BytesIO를 쓰면 MemoryStream처럼 동작하지만, zlib.compress() / decompress()는 바로 바이트 배열을 처리하므로 굳이 필요 없음.
- 필요 시:
```python
buf = io.BytesIO()
buf.write(zlib.compress(data))
buf.seek(0)
```


## 📊 언어별 비교
| 언어   | 메모리 스트림 클래스 / 버퍼           | 압축 클래스·함수                                   | 해제 클래스·함수                                   | 표준 라이브러리 여부 |
|--------|---------------------------------------|----------------------------------------------------|----------------------------------------------------|----------------------|
| C#     | `MemoryStream`                        | `DeflateStream` (`CompressionMode.Compress`)       | `DeflateStream` (`CompressionMode.Decompress`)     | ✔ (.NET 내장)        |
| C++    | `std::vector` / 메모리 버퍼 직접 사용 | `compress()` (`zlib`)                              | `uncompress()` (`zlib`)                            | ✖ (zlib 외부 필요)   |
| Java   | `ByteArrayOutputStream` / `ByteArrayInputStream` | `DeflaterOutputStream`                             | `InflaterInputStream`                              | ✔ (JDK 내장)         |
| Python | `io.BytesIO` (선택적)                  | `zlib.compress()`                                  | `zlib.decompress()`                                | ✔ (표준 라이브러리)  |


---

# MemoryStream Only
이번에는 압축 없이 MemoryStream만을 사용한 예제를 만들고, 그 개념과 활용법을 깔끔하게 정리.

## 📌 MemoryStream이란?
- .NET의 메모리 기반 스트림 클래스 (System.IO.MemoryStream)
- 데이터를 파일이나 네트워크가 아닌 메모리(RAM) 에 저장하고 읽을 수 있음
- 파일 I/O보다 빠르고, 임시 데이터 처리나 버퍼링에 유용
- byte[] 배열을 내부 버퍼로 사용
- Stream을 상속하므로 다른 스트림 API와 동일하게 사용 가능

## 🟦 C# MemoryStream 예제
```csharp
using System;
using System.IO;
using System.Text;

class Program
{
    static void Main()
    {
        // 1. 메모리 스트림 생성
        using (var ms = new MemoryStream())
        {
            // 2. 데이터 쓰기
            string text = "Hello MemoryStream!";
            byte[] buffer = Encoding.UTF8.GetBytes(text);
            ms.Write(buffer, 0, buffer.Length);

            // 3. 스트림 위치를 처음으로 되돌림
            ms.Position = 0;

            // 4. 데이터 읽기
            byte[] readBuffer = new byte[buffer.Length];
            ms.Read(readBuffer, 0, readBuffer.Length);
            string readText = Encoding.UTF8.GetString(readBuffer);

            Console.WriteLine(readText); // Hello MemoryStream!
        }
    }
}
```


## 🟨 Java에서의 대응 (ByteArrayOutputStream / ByteArrayInputStream)
```java
import java.io.*;
import java.nio.charset.StandardCharsets;

public class MemoryStreamExample {
    public static void main(String[] args) throws IOException {
        // 쓰기
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        String text = "Hello MemoryStream!";
        baos.write(text.getBytes(StandardCharsets.UTF_8));

        // 읽기
        byte[] data = baos.toByteArray();
        ByteArrayInputStream bais = new ByteArrayInputStream(data);
        byte[] readBuffer = new byte[data.length];
        bais.read(readBuffer, 0, readBuffer.length);
        String readText = new String(readBuffer, StandardCharsets.UTF_8);

        System.out.println(readText);
    }
}
```

- ByteArrayOutputStream → C#의 MemoryStream 쓰기 역할
- ByteArrayInputStream → C#의 MemoryStream 읽기 역할

## 🟩 C++에서의 대응 (std::stringstream)
```cpp
#include <iostream>
#include <sstream>
#include <string>

int main() {
    std::stringstream ss;

    // 쓰기
    ss << "Hello MemoryStream!";

    // 읽기
    std::string result;
    ss >> result; // 공백 기준으로 읽음
    std::cout << result << std::endl;

    // 전체 문자열 읽기
    ss.clear();
    ss.seekg(0);
    std::string all;
    std::getline(ss, all);
    std::cout << all << std::endl;
}
```


- std::stringstream은 메모리 기반 문자열 스트림
- 이진 데이터는 std::ostringstream / std::istringstream 또는 std::vector<char>로 처리 가능

## 🐍 Python에서의 대응 (io.BytesIO / io.StringIO)
```python
import io

# BytesIO: 이진 데이터
ms = io.BytesIO()
ms.write("Hello MemoryStream!".encode("utf-8"))

ms.seek(0)
data = ms.read().decode("utf-8")
print(data)

# StringIO: 텍스트 데이터
from io import StringIO
ms_text = StringIO()
ms_text.write("Hello MemoryStream!")
ms_text.seek(0)
print(ms_text.read())
```


- BytesIO → C# MemoryStream과 거의 동일
- StringIO → 텍스트 전용 메모리 스트림

## 📊 언어별 MemoryStream 대응 표
| 언어   | 메모리 스트림 / 버퍼 클래스                          | 특징 및 용도 |
|--------|------------------------------------------------------|--------------|
| C#     | `System.IO.MemoryStream`                             | byte[] 기반 메모리 스트림, `Stream` API와 동일하게 사용 가능 |
| Java   | `ByteArrayOutputStream` / `ByteArrayInputStream`     | 바이트 배열 기반, 출력/입력을 별도 클래스로 구분 |
| C++    | `std::stringstream` / `std::istringstream` / `std::ostringstream` | 문자열 기반 메모리 스트림, 이진 데이터는 `std::vector<char>` 등 사용 |
| Python | `io.BytesIO` / `io.StringIO`                         | BytesIO는 이진 데이터, StringIO는 텍스트 데이터 전용 |


💡 이렇게 보면 MemoryStream은 **"파일 대신 메모리에 쓰고 읽는 가상의 파일"**이라고 이해하면 쉽습니다.

---


## C++ 이진 스트림 유틸 개요
- 목표: C# BinaryReader/Writer와 유사한 API로 기본형과 문자열을 이진으로 읽고 쓰기
- 문자열 포맷: 길이(uint32) + UTF-8 바이트
- 엔디안: 기본 리틀엔디안(옵션으로 빅엔디안), 시스템 엔디안과 다르면 자동 바이트 스왑
- 스트림: 어떤 std::istream/std::ostream에도 동작 (파일, 메모리, 네트워크 등)

단일 헤더 스타일 코드
```cpp
#include <cstdint>
#include <cstring>
#include <array>
#include <algorithm>
#include <stdexcept>
#include <string>
#include <istream>
#include <ostream>

enum class Endianness { Little, Big };

namespace detail {
    constexpr bool kSystemLittle = [] {
        uint16_t x = 1;
        return *reinterpret_cast<unsigned char*>(&x) == 1;
    }();

    template <typename T>
    T bswap(T v) {
        static_assert(std::is_trivially_copyable<T>::value, "bswap requires trivially copyable type");
        std::array<unsigned char, sizeof(T)> b{};
        std::memcpy(b.data(), &v, sizeof(T));
        std::reverse(b.begin(), b.end());
        T out{};
        std::memcpy(&out, b.data(), sizeof(T));
        return out;
    }

    template <typename T>
    T maybe_swap(T v, Endianness order) {
        bool needSwap = (order == Endianness::Little) ? !kSystemLittle : kSystemLittle;
        return needSwap ? bswap(v) : v;
    }
}

class BinaryWriter {
public:
    explicit BinaryWriter(std::ostream& os, Endianness order = Endianness::Little)
        : os_(os), order_(order) {}

    void writeU8(std::uint8_t v)  { writeRaw(v); }
    void writeI8(std::int8_t v)   { writeRaw(v); }
    void writeU16(std::uint16_t v){ writePod(v); }
    void writeI16(std::int16_t v) { writePod(v); }
    void writeU32(std::uint32_t v){ writePod(v); }
    void writeI32(std::int32_t v) { writePod(v); }
    void writeU64(std::uint64_t v){ writePod(v); }
    void writeI64(std::int64_t v) { writePod(v); }
    void writeF32(float v)        { writePod(v); }
    void writeF64(double v)       { writePod(v); }

    // 길이(uint32) + UTF-8 바이트
    void writeString(const std::string& s) {
        if (s.size() > 0xFFFFFFFFull) throw std::length_error("string too long");
        writeU32(static_cast<std::uint32_t>(s.size()));
        os_.write(s.data(), static_cast<std::streamsize>(s.size()));
        if (!os_) throw std::runtime_error("writeString failed");
    }

private:
    template <typename T>
    void writePod(T v) {
        T t = detail::maybe_swap(v, order_);
        os_.write(reinterpret_cast<const char*>(&t), sizeof(T));
        if (!os_) throw std::runtime_error("write failed");
    }
    template <typename T>
    void writeRaw(T v) {
        os_.write(reinterpret_cast<const char*>(&v), sizeof(T));
        if (!os_) throw std::runtime_error("write failed");
    }

    std::ostream& os_;
    Endianness order_;
};

class BinaryReader {
public:
    explicit BinaryReader(std::istream& is, Endianness order = Endianness::Little)
        : is_(is), order_(order) {}

    std::uint8_t  readU8()  { return readRaw<std::uint8_t>(); }
    std::int8_t   readI8()  { return readRaw<std::int8_t>(); }
    std::uint16_t readU16() { return readPod<std::uint16_t>(); }
    std::int16_t  readI16() { return readPod<std::int16_t>(); }
    std::uint32_t readU32() { return readPod<std::uint32_t>(); }
    std::int32_t  readI32() { return readPod<std::int32_t>(); }
    std::uint64_t readU64() { return readPod<std::uint64_t>(); }
    std::int64_t  readI64() { return readPod<std::int64_t>(); }
    float         readF32() { return readPod<float>(); }
    double        readF64() { return readPod<double>(); }

    // 길이(uint32) + UTF-8 바이트
    std::string readString() {
        std::uint32_t len = readU32();
        std::string s(len, '\0');
        if (len > 0) {
            is_.read(s.data(), static_cast<std::streamsize>(len));
            if (!is_) throw std::runtime_error("readString failed");
        }
        return s;
    }

private:
    template <typename T>
    T readPod() {
        T v{};
        is_.read(reinterpret_cast<char*>(&v), sizeof(T));
        if (!is_) throw std::runtime_error("read failed");
        return detail::maybe_swap(v, order_);
    }
    template <typename T>
    T readRaw() {
        T v{};
        is_.read(reinterpret_cast<char*>(&v), sizeof(T));
        if (!is_) throw std::runtime_error("read failed");
        return v;
    }

    std::istream& is_;
    Endianness order_;
};

```

사용 예시
1) 파일 스트림에 쓰고 

```cpp
#include <iostream>
#include <fstream>
#include <bit>
#include <array>
#include <string>
#include <cstring>
#include <type_traits>

class BinaryWriter {
public:
    explicit BinaryWriter(std::ostream& os,
                          std::endian order = std::endian::native)
        : os_(os), order_(order) {}

    void writeRaw(const void* data, std::size_t size) {
        os_.write(reinterpret_cast<const char*>(data), size);
    }

    template <typename T>
    void writePod(const T& value) {
        static_assert(std::is_trivially_copyable_v<T>,
                      "T must be trivially copyable");
        if (order_ == std::endian::native) {
            writeRaw(&value, sizeof(T));
        } else {
            auto bytes = std::bit_cast<std::array<std::byte, sizeof(T)>>(value);
            std::reverse(bytes.begin(), bytes.end());
            writeRaw(bytes.data(), bytes.size());
        }
    }

    void writeU32(std::uint32_t v) { writePod(v); }
    void writeI32(std::int32_t v) { writePod(v); }
    void writeF32(float v) { writePod(v); }

    void writeString(const std::string& s) {
        std::uint32_t len = static_cast<std::uint32_t>(s.size());
        writeU32(len);
        writeRaw(s.data(), len);
    }

private:
    std::ostream& os_;
    std::endian order_;
};

class BinaryReader {
public:
    explicit BinaryReader(std::istream& is,
                          std::endian order = std::endian::native)
        : is_(is), order_(order) {}

    void readRaw(void* data, std::size_t size) {
        is_.read(reinterpret_cast<char*>(data), size);
    }

    template <typename T>
    T readPod() {
        static_assert(std::is_trivially_copyable_v<T>,
                      "T must be trivially copyable");
        T value{};
        readRaw(&value, sizeof(T));
        if (order_ != std::endian::native) {
            auto bytes = std::bit_cast<std::array<std::byte, sizeof(T)>>(value);
            std::reverse(bytes.begin(), bytes.end());
            value = std::bit_cast<T>(bytes);
        }
        return value;
    }

    std::uint32_t readU32() { return readPod<std::uint32_t>(); }
    std::int32_t readI32() { return readPod<std::int32_t>(); }
    float readF32() { return readPod<float>(); }

    std::string readString() {
        std::uint32_t len = readU32();
        std::string s(len, '\0');
        readRaw(s.data(), len);
        return s;
    }

private:
    std::istream& is_;
    std::endian order_;
};

int main() {
    {
        std::ofstream ofs("test.bin", std::ios::binary);
        BinaryWriter writer(ofs, std::endian::little);
        writer.writeU32(123456789);
        writer.writeF32(3.14f);
        writer.writeString("Hello, Binary!");
    }

    {
        std::ifstream ifs("test.bin", std::ios::binary);
        BinaryReader reader(ifs, std::endian::little);
        auto num = reader.readU32();
        auto f = reader.readF32();
        auto str = reader.readString();

        std::cout << "U32: " << num << "\n";
        std::cout << "F32: " << f << "\n";
        std::cout << "String: " << str << "\n";
    }

    return 0;
}
```
---

