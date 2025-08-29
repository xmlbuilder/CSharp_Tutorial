# 직렬화

C# 코드처럼 객체를 바이트 배열로 직렬화하고 다시 역직렬화하는 기능은 C++, Java, Python에서도 각각의 방식으로 구현할 수 있음.
 아래에 각 언어별 유사한 기능을 소개.

## 🧠 C# 방식 요약
C#에서는 Marshal 클래스를 사용해 비관리 객체를 바이트 배열로 변환하고 다시 복원할 수 있어요.
이 방식은 주로 구조체(struct)나 고정된 메모리 레이아웃을 가진 객체에 적합합니다.

## 🧵 C++: memcpy 또는 std::stringstream 사용
### 1. memcpy 방식 (POD 타입에 적합)
```cpp
#include <cstring>

struct MyStruct {
    int a;
    float b;
};

std::vector<uint8_t> StructToBytes(const MyStruct& obj) {
    std::vector<uint8_t> bytes(sizeof(MyStruct));
    std::memcpy(bytes.data(), &obj, sizeof(MyStruct));
    return bytes;
}

MyStruct BytesToStruct(const std::vector<uint8_t>& bytes) {
    MyStruct obj;
    std::memcpy(&obj, bytes.data(), sizeof(MyStruct));
    return obj;
}
```


### 2. boost::serialization 또는 cereal 라이브러리 사용
복잡한 클래스나 포인터가 포함된 객체는 Boost.Serialization 또는 Cereal 같은 라이브러리를 사용하는 것이 안전하고 유연합니다.

## ☕ Java: ObjectOutputStream / ObjectInputStream 사용
```java
import java.io.*;

public class Serializer {
    public static byte[] objectToBytes(Object obj) throws IOException {
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream out = new ObjectOutputStream(bos);
        out.writeObject(obj);
        return bos.toByteArray();
    }

    public static Object bytesToObject(byte[] bytes) throws IOException, ClassNotFoundException {
        ByteArrayInputStream bis = new ByteArrayInputStream(bytes);
        ObjectInputStream in = new ObjectInputStream(bis);
        return in.readObject();
    }
}
```

⚠️ 객체는 Serializable 인터페이스를 구현해야 합니다.


## 🐍 Python: pickle 또는 struct 모듈 사용

### 1. pickle (가장 일반적인 직렬화 방법)
```python
import pickle

def object_to_bytes(obj):
    return pickle.dumps(obj)

def bytes_to_object(data):
    return pickle.loads(data)

```

### 2. struct (C 스타일 구조체에 적합)
```python
import struct

# 예: int와 float를 포함한 구조체
def pack_struct(i, f):
    return struct.pack('if', i, f)

def unpack_struct(data):
    return struct.unpack('if', data)
```
###  🧪 예제: 정수와 실수를 바이트로 변환하고 다시 복원하기
```python
import struct

# 구조체처럼 사용할 값
i = 42          # 정수
f = 3.14        # 실수

# 1. 바이트로 변환 (pack)
packed = struct.pack('if', i, f)
print("바이트 배열:", packed)

# 2. 다시 원래 값으로 복원 (unpack)
unpacked = struct.unpack('if', packed)
print("복원된 값:", unpacked)
```

### 🔍 출력 예시
```
바이트 배열: b'*\x00\x00\x00\xc3\xf5H@'
복원된 값: (42, 3.140000104904175)
```

### 📦 설명
- struct.pack('if', i, f)
→ 'i'는 4바이트 정수, 'f'는 4바이트 부동소수점(float)을 의미해요.
→ 이 함수는 두 값을 바이트 배열로 변환해요.
- struct.unpack('if', packed)
→ 바이트 배열을 다시 (int, float) 튜플로 복원해요.




## 🎯 요약 비교
| 언어    | 방법                          | 주요 API / 기술                     | 특징 및 제한사항                                      |
|---------|-------------------------------|-------------------------------------|--------------------------------------------------------|
| **C#**  | `Marshal` 기반 직렬화         | `Marshal.StructureToPtr`, `PtrToStructure` | 비관리 객체(struct 등)에 적합. 메모리 직접 제어 필요. |
| **C++** | 메모리 복사 또는 라이브러리   | `memcpy`, `boost::serialization`, `cereal` | POD 타입은 `memcpy`, 복잡한 객체는 라이브러리 사용.   |
| **Java**| 표준 직렬화                   | `ObjectOutputStream`, `ObjectInputStream` | `Serializable` 구현 필요. JVM 내부 포맷 사용.         |
| **Python**| 동적 직렬화 또는 구조체 포맷| `pickle`, `struct`                  | 유연하고 간단. `pickle`은 거의 모든 객체 지원.         |

---


