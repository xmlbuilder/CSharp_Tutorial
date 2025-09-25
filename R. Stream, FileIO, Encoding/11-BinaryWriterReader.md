# BinaryWriter / BinaryReader
BinaryWriter / BinaryReader를 사용해 원시 이진 데이터를 스트림에 쓰고 읽는 전형적인 패턴입니다.
이 방식은 텍스트 인코딩이 아닌 바이트 단위로 데이터를 직렬화하기 때문에, 속도가 빠르고 용량이 작으며, 구조체나 객체를 저장할 때 유용합니다.
C++, Java, Python에도 거의 동일한 개념이 존재합니다.

## 📘 C# – BinaryWriter / BinaryReader 개념
- BinaryWriter: 기본 데이터형(int, double, string 등)을 바이트 시퀀스로 변환해 스트림에 씀.
- BinaryReader: 스트림에서 바이트를 읽어 기본 데이터형으로 복원.
- 문자열 처리: Write(string)은 UTF-8 기반 길이+데이터 형식으로 저장.
- 주의: 저장 순서와 읽는 순서가 반드시 동일해야 함.
```csharp
public void SaveData(Stream s)
{
    using var w = new BinaryWriter(s);
    w.Write(Name);
    w.Write(Age);
    w.Write(Height);
    w.Flush();
}

public void LoadData(Stream s)
{
    using var r = new BinaryReader(s);
    Name = r.ReadString();
    Age = r.ReadInt32();
    Height = r.ReadDouble();
}
```


## 📗 C++ – 유사 개념
C++에서는 <fstream>의 write() / read()를 사용해 바이너리 모드로 데이터를 입출력합니다.
```cpp
#include <fstream>
#include <string>

struct Person {
    std::string name;
    int age;
    double height;
};

void SaveData(const Person& p, const std::string& filename) {
    std::ofstream out(filename, std::ios::binary);
    size_t len = p.name.size();
    out.write(reinterpret_cast<const char*>(&len), sizeof(len));
    out.write(p.name.data(), len);
    out.write(reinterpret_cast<const char*>(&p.age), sizeof(p.age));
    out.write(reinterpret_cast<const char*>(&p.height), sizeof(p.height));
}

Person LoadData(const std::string& filename) {
    Person p;
    std::ifstream in(filename, std::ios::binary);
    size_t len;
    in.read(reinterpret_cast<char*>(&len), sizeof(len));
    p.name.resize(len);
    in.read(p.name.data(), len);
    in.read(reinterpret_cast<char*>(&p.age), sizeof(p.age));
    in.read(reinterpret_cast<char*>(&p.height), sizeof(p.height));
    return p;
}

```

- 주의: 문자열은 길이 정보를 직접 저장해야 함.
- 엔디안과 구조체 패딩에 주의.

## 📙 Java – DataOutputStream / DataInputStream
Java의 DataOutputStream / DataInputStream은 C#의 BinaryWriter/Reader와 거의 동일합니다.
```java
import java.io.*;

class Person {
    String name;
    int age;
    double height;
}

void saveData(Person p, String filename) throws IOException {
    try (DataOutputStream out = new DataOutputStream(new FileOutputStream(filename))) {
        out.writeUTF(p.name); // UTF-8 + 길이
        out.writeInt(p.age);
        out.writeDouble(p.height);
    }
}

Person loadData(String filename) throws IOException {
    Person p = new Person();
    try (DataInputStream in = new DataInputStream(new FileInputStream(filename))) {
        p.name = in.readUTF();
        p.age = in.readInt();
        p.height = in.readDouble();
    }
    return p;
}
```

- writeUTF() / readUTF()는 길이+UTF-8 문자열 형식.
- Java는 빅엔디안을 사용하므로, C#·C++과 교환 시 변환 필요.

## 🐍 Python – struct / pickle / io.BytesIO
Python에서는 struct 모듈로 바이너리 포맷을 직접 정의하거나, pickle로 직렬화할 수 있습니다.
```python
import struct

def save_data(name, age, height, filename):
    name_bytes = name.encode('utf-8')
    with open(filename, 'wb') as f:
        f.write(struct.pack('I', len(name_bytes)))  # 문자열 길이 (4바이트)
        f.write(name_bytes)
        f.write(struct.pack('i', age))              # int
        f.write(struct.pack('d', height))           # double

def load_data(filename):
    with open(filename, 'rb') as f:
        name_len = struct.unpack('I', f.read(4))[0]
        name = f.read(name_len).decode('utf-8')
        age = struct.unpack('i', f.read(4))[0]
        height = struct.unpack('d', f.read(8))[0]
    return name, age, height
```

- struct.pack() / unpack()으로 C 구조체 스타일 포맷 지정.
- 기본 엔디안은 리틀엔디안(<), 필요 시 >로 빅엔디안 지정.

## 📊 언어별 Binary Reader/Writer 비교
| 언어   | 쓰기 클래스/함수                          | 읽기 클래스/함수                          | 문자열 처리 방식             | 엔디안 기본 |
|--------|-------------------------------------------|--------------------------------------------|------------------------------|-------------|
| C#     | `BinaryWriter.Write()`                    | `BinaryReader.ReadXXX()`                   | UTF-8 + 길이(7비트 인코딩)    | 리틀엔디안  |
| C++    | `ofstream::write()`                       | `ifstream::read()`                         | 직접 길이 저장 후 바이트 기록 | 구현 의존   |
| Java   | `DataOutputStream.writeXXX()`              | `DataInputStream.readXXX()`                 | UTF-8 + 길이(`writeUTF`)      | 빅엔디안    |
| Python | `struct.pack()` / `file.write()`           | `struct.unpack()` / `file.read()`           | 직접 길이 저장 후 바이트 기록 | 기본 리틀엔디안(`<`) |



아래는 C# ↔ Java ↔ Python 간에 동일한 바이너리 포맷으로 Person 객체를 저장하고 읽을 수 있는 완전 호환 예제입니다.
핵심은 저장 순서와 데이터 형식을 세 언어에서 완전히 동일하게 맞추는 것입니다.

## 📦 바이너리 포맷 설계
| 순서 | 데이터 항목   | 자료형                | 크기(바이트) | 엔디안       | 설명 |
|------|--------------|----------------------|--------------|-------------|------|
| 1    | 이름 길이     | int32 (부호 있음)     | 4            | 리틀엔디안  | UTF-8 인코딩된 이름의 바이트 길이 |
| 2    | 이름 데이터   | byte[]               | 가변         | -           | UTF-8로 인코딩된 이름 문자열 |
| 3    | 나이          | int32 (부호 있음)     | 4            | 리틀엔디안  | 나이 값 |
| 4    | 키(Height)    | double (IEEE 754)    | 8            | 리틀엔디안  | 키 값(미터 또는 cm 단위) |



## 🟦 C# 예제
```csharp
using System;
using System.IO;
using System.Text;

public class Person
{
    public string Name;
    public int Age;
    public double Height;

    public void Save(Stream s)
    {
        using var bw = new BinaryWriter(s, Encoding.UTF8, leaveOpen: true);
        byte[] nameBytes = Encoding.UTF8.GetBytes(Name);
        bw.Write(nameBytes.Length); // int32 length
        bw.Write(nameBytes);        // name data
        bw.Write(Age);              // int32
        bw.Write(Height);           // double
    }

    public static Person Load(Stream s)
    {
        using var br = new BinaryReader(s, Encoding.UTF8, leaveOpen: true);
        int nameLen = br.ReadInt32();
        string name = Encoding.UTF8.GetString(br.ReadBytes(nameLen));
        int age = br.ReadInt32();
        double height = br.ReadDouble();
        return new Person { Name = name, Age = age, Height = height };
    }
}
```


## 🟨 Java 예제
```java
import java.io.*;

class Person {
    String name;
    int age;
    double height;

    void save(OutputStream os) throws IOException {
        DataOutputStream out = new DataOutputStream(os);
        byte[] nameBytes = name.getBytes("UTF-8");
        out.writeInt(Integer.reverseBytes(nameBytes.length)); // 리틀엔디안 변환
        out.write(nameBytes);
        out.writeInt(Integer.reverseBytes(age));
        out.writeLong(Long.reverseBytes(Double.doubleToLongBits(height)));
    }

    static Person load(InputStream is) throws IOException {
        DataInputStream in = new DataInputStream(is);
        int nameLen = Integer.reverseBytes(in.readInt());
        byte[] nameBytes = new byte[nameLen];
        in.readFully(nameBytes);
        String name = new String(nameBytes, "UTF-8");
        int age = Integer.reverseBytes(in.readInt());
        double height = Double.longBitsToDouble(Long.reverseBytes(in.readLong()));
        Person p = new Person();
        p.name = name;
        p.age = age;
        p.height = height;
        return p;
    }
}
```


## 🐍 Python 예제
```python
import struct

class Person:
    def __init__(self, name="", age=0, height=0.0):
        self.name = name
        self.age = age
        self.height = height

    def save(self, file):
        name_bytes = self.name.encode('utf-8')
        file.write(struct.pack('<i', len(name_bytes)))  # int32 little-endian
        file.write(name_bytes)
        file.write(struct.pack('<i', self.age))
        file.write(struct.pack('<d', self.height))      # double little-endian

    @staticmethod
    def load(file):
        name_len = struct.unpack('<i', file.read(4))[0]
        name = file.read(name_len).decode('utf-8')
        age = struct.unpack('<i', file.read(4))[0]
        height = struct.unpack('<d', file.read(8))[0]
        return Person(name, age, height)
```

## 🔗 사용 예 (C# ↔ Java ↔ Python 동일 파일 사용)

- C#에서 저장
```csharp
var p = new Person { Name = "Alice", Age = 30, Height = 165.5 };
using var fs = File.Create("person.bin");
p.Save(fs);
```

- Java에서 읽기
```java
Person p = Person.load(new FileInputStream("person.bin"));
System.out.println(p.name + " " + p.age + " " + p.height);
```

- Python에서 읽기
```python
with open("person.bin", "rb") as f:
    p = Person.load(f)
    print(p.name, p.age, p.height)
```


## 💡 이렇게 하면 C# → Java → Python 간에 완전히 동일한 이진 포맷으로 Person 객체를 주고받을 수 있습니다.
핵심은 리틀엔디안 고정과 문자열 길이+데이터 구조를 동일하게 맞추는 것입니다.



## 📌 현재도 쓰이는 빅엔디안 CPU 사례
| CPU 아키텍처 / 제품군       | 기본 엔디안 | 사용 분야 / 제품 예시                                      | 비고 |
|----------------------------|-------------|------------------------------------------------------------|------|
| IBM z/Architecture         | 빅엔디안    | IBM 메인프레임(zSeries, z15, z16)                          | 금융, 대형 트랜잭션 처리 |
| PowerPC / Power ISA         | 빅엔디안(LE 지원) | 자동차 ECU, 산업용 제어기, 네트워크 장비 (Cisco, NXP QorIQ) | 일부 모델은 리틀엔디안 모드 가능 |
| SPARC (Oracle, Fujitsu)    | 빅엔디안    | 미션 크리티컬 서버, 항공우주, 방위산업                      | Solaris, RTOS 환경 |
| MIPS (BE 모드)              | 빅엔디안(LE 지원) | 라우터, 스위치 (구형 Cisco, Juniper), 임베디드 기기         | 네트워크 장비에서 BE 모드 선호 |
| ARM (BE-8 모드)             | 리틀엔디안 기본, BE 지원 | 고급 네트워크 프로세서, 통신 ASIC                           | ARMv7/ARMv8에서 선택 가능 |
| DSP/네트워크 전용 프로세서  | 다양        | Broadcom, Cavium, NetLogic 등 네트워크 칩셋                 | 패킷 처리 최적화 위해 BE 사용 |


## 💡 왜 아직도 빅엔디안을 쓰나?
- 네트워크 프로토콜: TCP/IP 등 인터넷 표준이 빅엔디안(“network byte order”)을 사용 → 패킷 처리 효율성
- 레거시 호환성: 오래된 펌웨어·OS·미들웨어와의 호환
- 산업·군수·우주 분야: 검증된 하드웨어를 장기간 재사용

## 📍 정리, 
일반 PC·스마트폰 CPU는 거의 전부 리틀엔디안이지만, 
네트워크 장비·메인프레임·임베디드 컨트롤러 세계에서는 빅엔디안이 여전히 살아 있습니다. 
특히 IBM 메인프레임(zSeries)나 일부 네트워크 프로세서는 지금도 빅엔디안이 기본입니다.

---
# 이진 데이터 주고 받기
서로 다른 언어(C#, Java, Python, C++ 등)에서 이진(Binary) 데이터를 주고받을 때는 
문자열 앞에 길이 정보를 명시적으로 기록하는 방식이 가장 안전하고 호환성이 좋습니다.

## 📌 이유
- 문자열 경계 구분
- 바이너리 포맷에는 문자열 끝을 나타내는 \0(null terminator) 같은 구분자가 없을 수 있음.
- 길이를 먼저 써주면, 읽는 쪽에서 정확히 몇 바이트를 문자열로 해석해야 하는지 알 수 있음.
- 다국어·멀티바이트 안전성
- UTF-8, UTF-16 등 가변 길이 인코딩에서는 문자 수와 바이트 수가 다름.
- 길이를 바이트 단위로 저장하면 어떤 언어에서도 동일하게 해석 가능.
- 언어별 기본 포맷 차이 제거
- C# BinaryWriter.Write(string) → 길이 + UTF-8(7비트 인코딩 길이)
- Java writeUTF() → 길이(2바이트) + UTF-8
- Python write() → 길이 정보 없음, 직접 구현 필요
- 이런 차이를 없애려면 길이(int32) + UTF-8 바이트로 고정하는 것이 좋음.
- 추가 데이터와의 구분
- 문자열 뒤에 다른 필드(숫자, 구조체 등)가 이어질 때, 길이 정보가 없으면 어디서 잘라야 할지 모호해짐.

## 📖 권장 포맷 예시
[int32: 문자열 바이트 길이] [byte[]: UTF-8 인코딩 문자열] [다음 데이터...]

- int32: 4바이트, 리틀엔디안(또는 빅엔디안, 합의 필요)
- byte[]: UTF-8 인코딩된 문자열 데이터

## 💡 결론:
다른 언어·플랫폼 간 호환을 위해서는 문자열 앞에 길이(int32 등)를 먼저 쓰고, 그 다음 UTF-8 바이트를 기록하는 방식이 사실상 표준에 가깝습니다.
