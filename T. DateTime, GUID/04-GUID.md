# GUID(Globally Unique Identifier)
GUID(Globally Unique Identifier)는 시스템 간 고유성을 보장하는 데 핵심적인 요소죠. 
특히 분산 시스템, 데이터베이스 키, 파일 이름, API 토큰 등에서 자주 사용됩니다. 

## 🧠 GUID란?
- 128비트 크기의 고유 식별자
- 일반적으로 16진수 + 하이픈 형식: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
- 충돌 가능성이 거의 없는 고유 값
- UUID(Universally Unique Identifier)와 거의 동일한 개념

## ✅ C#에서의 GUID 사용 요약
```csharp
Guid g = new Guid(); // 빈 GUID: 00000000-0000-0000-0000-000000000000
Guid g1 = Guid.NewGuid(); // 새 GUID 생성
Guid g2 = new Guid("0b214de7-8958-4956-8eed-28f9ba2c47c6"); // 문자열로부터 생성
Guid g4 = Guid.Parse("0b214de7895849568eed28f9ba2c47c6"); // 하이픈 없는 문자열 파싱

// 다양한 출력 형식
Console.WriteLine(g4.ToString("N")); // 32자리 숫자만: 0b214de7895849568eed28f9ba2c47c6
Console.WriteLine(g4.ToString("D")); // 기본 형식: 0b214de7-8958-4956-8eed-28f9ba2c47c6
Console.WriteLine(g4.ToString("B")); // 중괄호 포함: {0b214de7-8958-4956-8eed-28f9ba2c47c6}
Console.WriteLine(g4.ToString("P")); // 괄호 포함: (0b214de7-8958-4956-8eed-28f9ba2c47c6)
Console.WriteLine(g4.ToString("X")); // Hex 형식: {0x...,0x...,...}

```


## 🧠 언어별 GUID 생성 방법
### 🧊 C++ (Boost 또는 OS API 사용)
```cpp
#include <iostream>
#include <boost/uuid/uuid.hpp>
#include <boost/uuid/uuid_generators.hpp>
#include <boost/uuid/uuid_io.hpp>

int main() {
    boost::uuids::uuid guid = boost::uuids::random_generator()();
    std::cout << guid << std::endl; // 예: 0b214de7-8958-4956-8eed-28f9ba2c47c6
}
```

💡 C++ 표준에는 아직 UUID 생성이 포함되지 않았기 때문에 Boost 라이브러리를 사용하는 것이 일반적입니다.


## ☕ Java
```java
import java.util.UUID;

public class Main {
    public static void main(String[] args) {
        UUID guid = UUID.randomUUID();
        System.out.println(guid); // 예: a09aa149-a5e8-4b79-bb18-1e6150891ce1
    }
}
```

- UUID.randomUUID()는 Version 4 UUID (랜덤 기반)를 생성합니다.
- UUID.fromString("...")으로 문자열을 파싱할 수도 있어요.

## 🐍 Python
```python
import uuid

guid1 = uuid.uuid4()  # 랜덤 UUID
guid2 = uuid.UUID("0b214de7-8958-4956-8eed-28f9ba2c47c6")  # 문자열로부터 생성

print(guid1)  # 예: a09aa149-a5e8-4b79-bb18-1e6150891ce1
print(guid1.hex)  # 하이픈 없는 32자리: 0b214de7895849568eed28f9ba2c47c6
```        

uuid1(), uuid3(), uuid5()도 존재하며 각각 시간 기반, 이름 기반(MD5/SHA1) UUID를 생성합니다.


## 📊 GUID 출력 형식 비교

| 언어     | 생성 함수                         | 기본 출력 형식                        | 하이픈 제거 방식                    |
|----------|-----------------------------------|---------------------------------------|-------------------------------------|
| C#       | `Guid.NewGuid()`                  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` | `.ToString("N")`                   |
| C++      | `boost::uuids::random_generator()`| `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` | 직접 문자열 처리 필요              |
| Java     | `UUID.randomUUID()`               | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` | `.toString().replace("-", "")`     |
| Python   | `uuid.uuid4()`                    | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` | `.hex`                             |


---

# 🪟 Windows에서 GUID 생성 방법 (C++ 기준)
## ✅ CoCreateGuid 함수 사용
```cpp
#include <windows.h>
#include <objbase.h>
#include <iostream>

int main() {
    GUID guid;
    HRESULT hr = CoCreateGuid(&guid);
    if (hr == S_OK) {
        printf("{%08x-%04x-%04x-%02x%02x-%02x%02x%02x%02x%02x%02x}\n",
            guid.Data1, guid.Data2, guid.Data3,
            guid.Data4[0], guid.Data4[1],
            guid.Data4[2], guid.Data4[3],
            guid.Data4[4], guid.Data4[5],
            guid.Data4[6], guid.Data4[7]);
    }
}
```

- CoCreateGuid()는 Windows에서 **Version 4 UUID (랜덤 기반)**을 생성합니다.
- 출력은 직접 포맷팅해야 하며, GUID 구조체는 128비트로 구성되어 있어요.

## 📦 GUID 구조체 설명
```cpp
typedef struct _GUID {
  unsigned long  Data1;
  unsigned short Data2;
  unsigned short Data3;
  unsigned char  Data4[8];
} GUID;
```

- 이 구조체는 GUID를 16바이트로 나누어 저장합니다.
- 출력 시에는 하이픈과 16진수 포맷을 조합해야 합니다.

---
