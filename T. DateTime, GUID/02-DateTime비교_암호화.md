#  DateTime 비교와 시간 연산 예제

C#에서의 DateTime 비교와 시간 연산 예제

## 🕒 언어별 시간 비교 및 연산 예시
### ✅ C# 예시 (이미 제공하신 코드)
```csharp
var dt1 = new DateTime(2017, 12, 24, 12, 30, 23);
var dtnew = DateTime.Now;
if (dtnew > dt1)
{
    Console.WriteLine("License is expired");
}

var future = DateTime.Now + new TimeSpan(365, 0, 0, 0);
Console.WriteLine("{0:MMMM dd, yyyy}", future);

var date1 = new DateTime(2009, 10, 22, 1, 30, 20);
var date2 = new DateTime(2009, 10, 22, 2, 40, 56);
TimeSpan diff = date2 - date1;
Console.WriteLine($"{diff.Days} - {diff.Hours} - {diff.Minutes} - {diff.Seconds}");
```

🧠 C++ 예시 (C++20 기준)
```cpp
#include <iostream>
#include <chrono>
#include <format>

int main() {
    using namespace std::chrono;

    auto dt1 = sys_days{year{2017}/12/24} + hours{12} + minutes{30} + seconds{23};
    auto now = system_clock::now();

    if (now > dt1) {
        std::cout << "License is expired\n";
    }

    auto future = now + days{365};
    std::time_t future_time = system_clock::to_time_t(future);
    std::cout << std::format("{:%B %d, %Y}", *std::localtime(&future_time)) << "\n";

    auto date1 = sys_days{year{2009}/10/22} + hours{1} + minutes{30} + seconds{20};
    auto date2 = sys_days{year{2009}/10/22} + hours{2} + minutes{40} + seconds{56};
    auto diff = duration_cast<seconds>(date2 - date1);

    int total_seconds = diff.count();
    int hours = total_seconds / 3600;
    int minutes = (total_seconds % 3600) / 60;
    int seconds = total_seconds % 60;
    std::cout << "0 - " << hours << " - " << minutes << " - " << seconds << "\n";
}

```

## ☕ Java 예시 (Java 8 이상)
```java
import java.time.*;

public class Main {
    public static void main(String[] args) {
        LocalDateTime dt1 = LocalDateTime.of(2017, 12, 24, 12, 30, 23);
        LocalDateTime now = LocalDateTime.now();

        if (now.isAfter(dt1)) {
            System.out.println("License is expired");
        }

        LocalDateTime future = now.plusDays(365);
        System.out.println(future.format(java.time.format.DateTimeFormatter.ofPattern("MMMM dd, yyyy")));

        LocalDateTime date1 = LocalDateTime.of(2009, 10, 22, 1, 30, 20);
        LocalDateTime date2 = LocalDateTime.of(2009, 10, 22, 2, 40, 56);
        Duration diff = Duration.between(date1, date2);

        long seconds = diff.getSeconds();
        long hours = seconds / 3600;
        long minutes = (seconds % 3600) / 60;
        long secs = seconds % 60;
        System.out.println("0 - " + hours + " - " + minutes + " - " + secs);
    }
}
```


## 🐍 Python 예시
```python
from datetime import datetime, timedelta

dt1 = datetime(2017, 12, 24, 12, 30, 23)
dtnew = datetime.now()

if dtnew > dt1:
    print("License is expired")

future = dtnew + timedelta(days=365)
print(future.strftime("%B %d, %Y"))

date1 = datetime(2009, 10, 22, 1, 30, 20)
date2 = datetime(2009, 10, 22, 2, 40, 56)
diff = date2 - date1

hours = diff.seconds // 3600
minutes = (diff.seconds % 3600) // 60
seconds = diff.seconds % 60
print(f"{diff.days} - {hours} - {minutes} - {seconds}")
```


## 📌 언어별 DateTime 기능 요약 비교

| 기능 항목         | C# (`System`)         | C++ (`<chrono>`)             | Java (`java.time`)           | Python (`datetime`)          |
|------------------|------------------------|-------------------------------|-------------------------------|-------------------------------|
| 현재 시간        | `DateTime.Now`         | `system_clock::now()`         | `LocalDateTime.now()`         | `datetime.now()`              |
| 시간 비교        | `dtnew > dt1`          | `now > dt1`                   | `isAfter()`                   | `>`                           |
| 시간 덧셈        | `+ TimeSpan(...)`      | `+ days{}`                    | `plusDays()`                  | `+ timedelta()`               |
| 포맷 출력        | `ToString(...)`        | `std::format(...)`            | `DateTimeFormatter`           | `strftime()`                  |
| 시간 차이 계산   | `TimeSpan`             | `duration_cast<>()`           | `Duration.between()`          | `date2 - date1`               |

---

# 🌍 언어별 타임존 처리 & 날짜 파싱 비교
| 기능 항목       | C# (`System`)                          | C++ (`<chrono>` + `<date>` 라이브러리) | Java (`java.time`)                      | Python (`datetime`, `pytz`, `zoneinfo`) |
|----------------|----------------------------------------|----------------------------------------|----------------------------------------|------------------------------------------|
| 타임존 클래스   | `TimeZoneInfo`, `DateTimeOffset`       | `tzdb`, `zoned_time` (Howard Hinnant)  | `ZoneId`, `ZonedDateTime`              | `timezone`, `pytz`, `zoneinfo`           |
| 현재 시간 (UTC) | `DateTime.UtcNow`                      | `system_clock::now()`                  | `Instant.now()`                         | `datetime.utcnow()`                      |
| 타임존 지정     | `TimeZoneInfo.FindSystemTimeZoneById()`| `make_zoned(...)`                      | `ZonedDateTime.of(..., ZoneId.of(...))`| `datetime.now(zoneinfo.ZoneInfo(...))`   |
| 타임존 변환     | `TimeZoneInfo.ConvertTime(...)`        | `zoned_time` 변환                      | `withZoneSameInstant(...)`             | `astimezone(...)`                        |
| 날짜 파싱       | `DateTime.Parse()`, `TryParse()`       | `std::chrono::parse()` (C++20)         | `LocalDate.parse()`, `DateTimeFormatter`| `datetime.strptime()`                    |
| 포맷 출력       | `ToString("yyyy-MM-dd")`               | `std::format("{:%Y-%m-%d}", ...)`      | `format(DateTimeFormatter.ofPattern())`| `strftime("%Y-%m-%d")`                   |



## 🔍 예시 코드 스니펫
### ✅ C# 타임존 & 파싱
```csharp
var tz = TimeZoneInfo.FindSystemTimeZoneById("Korea Standard Time");
var localTime = TimeZoneInfo.ConvertTimeFromUtc(DateTime.UtcNow, tz);

var parsedDate = DateTime.Parse("2025-09-01");
```

## 🧠 C++ (Howard Hinnant's date library)
```cpp
#include "date/tz.h"
auto tz = date::locate_zone("Asia/Seoul");
auto zoned = date::make_zoned(tz, std::chrono::system_clock::now());

std::istringstream in{"2025-09-01"};
date::sys_days parsed;
in >> date::parse("%Y-%m-%d", parsed);
```

## ☕ Java
```java
ZoneId seoulZone = ZoneId.of("Asia/Seoul");
ZonedDateTime nowInSeoul = ZonedDateTime.now(seoulZone);

LocalDate parsedDate = LocalDate.parse("2025-09-01");
```

## 🐍 Python
```python
from datetime import datetime
from zoneinfo import ZoneInfo

now_in_seoul = datetime.now(ZoneInfo("Asia/Seoul"))
parsed_date = datetime.strptime("2025-09-01", "%Y-%m-%d")
```
---

# Lincense 발급기 만들기 예제
"License 발급기"를 만든다고 가정하고, 오늘 날짜가 특정한 시작일(Start)과 종료일(End) 사이에 있는지를 확인하는 코드


## ✅ C# 예시
```csharp
using System;

class Program
{
    static void Main()
    {
        DateTime now = DateTime.Now;
        DateTime start = new DateTime(2025, 8, 1);
        DateTime end = new DateTime(2025, 9, 30);

        if (now >= start && now <= end)
        {
            Console.WriteLine("✅ License is valid.");
        }
        else
        {
            Console.WriteLine("❌ License is invalid.");
        }
    }
}
```


## 🧠 C++ 예시 (C++20 기준)
```cpp
#include <iostream>
#include <chrono>

int main() {
    using namespace std::chrono;

    auto now = system_clock::now();
    auto start = sys_days{year{2025}/8/1};
    auto end = sys_days{year{2025}/9/30};

    if (now >= start && now <= end) {
        std::cout << "✅ License is valid.\n";
    } else {
        std::cout << "❌ License is invalid.\n";
    }
}
```


## ☕ Java 예시
```java
import java.time.*;

public class LicenseChecker {
    public static void main(String[] args) {
        LocalDate today = LocalDate.now();
        LocalDate start = LocalDate.of(2025, 8, 1);
        LocalDate end = LocalDate.of(2025, 9, 30);

        if (!today.isBefore(start) && !today.isAfter(end)) {
            System.out.println("✅ License is valid.");
        } else {
            System.out.println("❌ License is invalid.");
        }
    }
}
```


## 🐍 Python 예시
```python
from datetime import datetime

now = datetime.now()
start = datetime(2025, 8, 1)
end = datetime(2025, 9, 30)

if start <= now <= end:
    print("✅ License is valid.")
else:
    print("❌ License is invalid.")
```

## 📌 License 유효성 체크 — 언어별 핵심 비교 요약

| 언어   | 현재 시간 구하기         | 유효 기간 비교 방식                         |
|--------|---------------------------|---------------------------------------------|
| C#     | `DateTime.Now`            | `now >= start && now <= end`               |
| C++    | `system_clock::now()`     | `now >= start && now <= end`               |
| Java   | `LocalDate.now()`         | `!isBefore(start) && !isAfter(end)`        |
| Python | `datetime.now()`          | `start <= now <= end`                      |


---

# License Encoding

좋은 아이디어예요 JungHwan님! 라이선스 발급기에서 Start와 End 시간을 특정 문자열과 조합해 인코딩하고, 그 문자열을 다시 디코딩해서 유효 기간을 확인하는 방식

## 🔐 기본 아이디어
- Start와 End를 문자열로 변환
- 특정 키워드나 사용자 정보와 조합
- Base64 또는 간단한 암호화 방식으로 인코딩
- 디코딩 후 다시 날짜로 파싱하여 유효성 확인

## ✅ C# 예시
```csharp
using System;
using System.Text;

class Program
{
    static void Main()
    {
        DateTime start = new DateTime(2025, 8, 1);
        DateTime end = new DateTime(2025, 9, 30);
        string user = "JungHwan";

        string raw = $"{user}|{start:yyyy-MM-dd}|{end:yyyy-MM-dd}";
        string encoded = Convert.ToBase64String(Encoding.UTF8.GetBytes(raw));
        Console.WriteLine($"Encoded: {encoded}");

        string decoded = Encoding.UTF8.GetString(Convert.FromBase64String(encoded));
        var parts = decoded.Split('|');
        DateTime parsedStart = DateTime.Parse(parts[1]);
        DateTime parsedEnd = DateTime.Parse(parts[2]);

        Console.WriteLine($"Decoded Start: {parsedStart}, End: {parsedEnd}");
    }
}
```

## 🧠 C++ 예시 (Base64는 외부 라이브러리 필요, 여기선 단순 문자열로 처리)
```cpp
#include <iostream>
#include <sstream>
#include <string>

int main() {
    std::string user = "JungHwan";
    std::string start = "2025-08-01";
    std::string end = "2025-09-30";

    std::string raw = user + "|" + start + "|" + end;
    std::cout << "Encoded (raw): " << raw << "\n";

    std::istringstream iss(raw);
    std::string part;
    getline(iss, part, '|'); std::string decodedUser = part;
    getline(iss, part, '|'); std::string decodedStart = part;
    getline(iss, part, '|'); std::string decodedEnd = part;

    std::cout << "Decoded Start: " << decodedStart << ", End: " << decodedEnd << "\n";
}

```

## ☕ Java 예시
```java
import java.util.Base64;

public class LicenseEncoder {
    public static void main(String[] args) {
        String user = "JungHwan";
        String start = "2025-08-01";
        String end = "2025-09-30";

        String raw = user + "|" + start + "|" + end;
        String encoded = Base64.getEncoder().encodeToString(raw.getBytes());
        System.out.println("Encoded: " + encoded);

        String decoded = new String(Base64.getDecoder().decode(encoded));
        String[] parts = decoded.split("\\|");
        System.out.println("Decoded Start: " + parts[1] + ", End: " + parts[2]);
    }
}
```


## 🐍 Python 예시
```python
import base64

user = "JungHwan"
start = "2025-08-01"
end = "2025-09-30"

raw = f"{user}|{start}|{end}"
encoded = base64.b64encode(raw.encode()).decode()
print("Encoded:", encoded)

decoded = base64.b64decode(encoded).decode()
parts = decoded.split("|")
print("Decoded Start:", parts[1], "End:", parts[2])
```


## 📌 문자열 인코딩/디코딩 & 날짜 파싱 — 언어별 요약 비교

| 언어   | 인코딩 함수                  | 디코딩 함수                   | 날짜 파싱 함수            |
|--------|------------------------------|-------------------------------|----------------------------|
| C#     | `Convert.ToBase64String()`   | `Convert.FromBase64String()`  | `DateTime.Parse()`         |
| C++    | —                            | `std::istringstream`          | —                          |
| Java   | `Base64.getEncoder()`        | `Base64.getDecoder()`         | `LocalDate.parse()`        |
| Python | `base64.b64encode()`         | `base64.b64decode()`          | `datetime.strptime()`      |

## 🔍 언어 간 문자열 호환 여부를 결정하는 요소
### 1. 문자열 조합 방식
- 예: "JungHwan|2025-08-01|2025-09-30"
→ 이처럼 단순한 문자열 조합은 대부분의 언어에서 동일하게 처리됨.
### 2. 날짜 포맷
- C#은 "yyyy-MM-dd"를 기본으로 쓰지만, Java나 Python은 로케일에 따라 "MM/dd/yyyy" 또는 "dd-MM-yyyy"로 다르게 해석될 수 있음.
- 포맷을 명시적으로 지정하면 호환성이 높아짐.
### 3. Base64 인코딩
- Base64는 표준이기 때문에 언어가 달라도 결과는 동일해야 함.
- 예: "JungHwan|2025-08-01|2025-09-30" → Base64 인코딩하면
SnVuZ0h3YW58MjAyNS0wOC0wMXwyMDI1LTA5LTMw
→ 이건 C#, Java, Python 모두 동일하게 나옴.
### 4. 문자 인코딩 (UTF-8 vs UTF-16 등)
- 대부분의 언어는 기본적으로 UTF-8을 사용하지만, C#은 내부적으로 UTF-16을 사용함.
- Base64 인코딩 전에 UTF-8로 명시하면 호환성 확보 가능.
### 5. 줄바꿈, 공백, 특수문자 처리
- 문자열 끝에 \n, \r, 공백 등이 포함되면 인코딩 결과가 달라질 수 있음.
- 항상 Trim() 또는 .strip() 같은 정리 작업을 해주는 게 좋음.

## ✅ 호환성을 높이는 팁

| 항목             | 권장 방식                          |
|------------------|------------------------------------|
| 날짜 포맷        | `yyyy-MM-dd` (ISO 8601 표준)       |
| 문자열 인코딩    | UTF-8 명시                         |
| 구분자 사용       | 표준 문자 (`|`, `:` 등) 사용       |
| 인코딩 방식       | Base64 (RFC 4648 표준) 사용        |
| 디코딩 시 처리    | 공백 제거, 예외 처리 포함          |
| 로케일 설정       | 고정된 로케일 (`en-US` 등) 사용    |
| 시간대 처리       | UTC 기준 또는 명시적 타임존 지정   |


## 🔐 예시 조건
- Start: 2025-08-01
- End: 2025-09-30
- Key: "JJH"
- Raw Format: "JJH|2025-08-01|2025-09-30"

## ✅ 인코딩 결과 (Base64)
```
SkpIfDIwMjUtMDgtMDF8MjAyNS0wOS0zMA==
```

이 문자열은 "JJH|2025-08-01|2025-09-30"을 UTF-8로 인코딩한 후 Base64로 변환한 결과입니다. 
어떤 언어에서든 아래처럼 처리하면 같은 결과가 나와요:

## Python 예시
```python
import base64
raw = "JJH|2025-08-01|2025-09-30"
encoded = base64.b64encode(raw.encode()).decode()
print(encoded)  # SkpIfDIwMjUtMDgtMDF8MjAyNS0wOS0zMA==
```

## Java 예시
```java
String raw = "JJH|2025-08-01|2025-09-30";
String encoded = Base64.getEncoder().encodeToString(raw.getBytes(StandardCharsets.UTF_8));
// SkpIfDIwMjUtMDgtMDF8MjAyNS0wOS0zMA==
```


## C# 예시
```csharp
string raw = "JJH|2025-08-01|2025-09-30";
string encoded = Convert.ToBase64String(Encoding.UTF8.GetBytes(raw));
// SkpIfDIwMjUtMDgtMDF8MjAyNS0wOS0zMA==
```



## 📥 디코딩 시
디코딩하면 다시 "JJH|2025-08-01|2025-09-30"로 복원되며, 이를 Split('|')로 나누면 키와 날짜를 다시 추출할 수 있어요.

---


# 🔐 SHA256 서명 처리 흐름
## 1. 라이선스 문자열 생성
JJH|2025-08-01|2025-09-30


## 2. SHA256 해시 생성
- 이 문자열을 UTF-8로 인코딩한 후 SHA256 해시 함수를 적용
- 결과는 64자리의 고유한 16진수 문자열
## 3. 서명 포함
- 최종 라이선스 문자열에 해시값을 덧붙임
JJH|2025-08-01|2025-09-30|<SHA256 해시값>


## 4. 검증 시점
- 클라이언트가 제출한 라이선스 문자열에서 앞부분을 추출
- 다시 SHA256으로 해시 계산
- 제출된 해시값과 비교 → 일치하면 위변조 없음

## 🧪 예시 코드 (Python 기준)
```python
import hashlib

raw = "JJH|2025-08-01|2025-09-30"
hash_value = hashlib.sha256(raw.encode()).hexdigest()

license_string = f"{raw}|{hash_value}"
print("License with signature:", license_string)
```

🔍 출력 예시
```
JJH|2025-08-01|2025-09-30|e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
```


## ✅ SHA256 서명 방식의 장점

| 항목             | 설명                                                   |
|------------------|--------------------------------------------------------|
| 위변조 방지       | 라이선스 내용이 변경되면 해시값도 완전히 달라져 검증 가능 |
| 경량 처리         | 암호화보다 빠르고 계산 비용이 적어 실시간 처리에 적합     |
| 언어 호환성       | 거의 모든 프로그래밍 언어에서 동일한 방식으로 지원됨       |
| 단방향 안전성     | 원래 문자열을 복원할 수 없어 보안성이 높음                |
| 간단한 검증 로직  | 해시 재계산 후 비교만으로 유효성 확인 가능                |



## 📌 주의할 점
- SHA256은 단방향 해시이므로 원래 문자열을 복원할 수는 없음
- 보안 강화를 위해 비밀 키를 포함한 HMAC-SHA256 방식도 고려 가능


## 📌 공통 입력값
- StartTime: 2025-08-01
- EndTime: 2025-09-30
- ID_NAME: JungHwan
- Key Prefix: JJH
- Raw Format: JJH|2025-08-01|2025-09-30|JungHwan
- SHA256: 해시값은 위 문자열을 기반으로 생성

## ✅ C# 예시
```csharp
using System;
using System.Text;
using System.Security.Cryptography;

class Program
{
    static void Main()
    {
        string raw = "JJH|2025-08-01|2025-09-30|JungHwan";
        using var sha256 = SHA256.Create();
        byte[] hashBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(raw));
        string hash = BitConverter.ToString(hashBytes).Replace("-", "").ToLower();

        string full = $"{raw}|{hash}";
        string encoded = Convert.ToBase64String(Encoding.UTF8.GetBytes(full));
        Console.WriteLine("Encoded:", encoded);

        string decoded = Encoding.UTF8.GetString(Convert.FromBase64String(encoded));
        Console.WriteLine("Decoded:", decoded);
    }
}
```

## 🧠 C++ 예시 (OpenSSL 필요)
```cpp
#include <iostream>
#include <sstream>
#include <iomanip>
#include <openssl/sha.h>
#include <string>
#include <vector>

std::string sha256(const std::string& input) {
    unsigned char hash[SHA256_DIGEST_LENGTH];
    SHA256((const unsigned char*)input.c_str(), input.size(), hash);
    std::ostringstream oss;
    for (int i = 0; i < SHA256_DIGEST_LENGTH; ++i)
        oss << std::hex << std::setw(2) << std::setfill('0') << (int)hash[i];
    return oss.str();
}

std::string base64_encode(const std::string& input); // 구현 필요

int main() {
    std::string raw = "JJH|2025-08-01|2025-09-30|JungHwan";
    std::string hash = sha256(raw);
    std::string full = raw + "|" + hash;

    std::cout << "Raw: " << full << "\n";
    // Base64 인코딩은 외부 라이브러리 또는 직접 구현 필요
}
```


## ☕ Java 예시
```java
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.util.Base64;

public class LicenseEncoder {
    public static void main(String[] args) throws Exception {
        String raw = "JJH|2025-08-01|2025-09-30|JungHwan";
        MessageDigest digest = MessageDigest.getInstance("SHA-256");
        byte[] hashBytes = digest.digest(raw.getBytes(StandardCharsets.UTF_8));
        StringBuilder hash = new StringBuilder();
        for (byte b : hashBytes) {
            hash.append(String.format("%02x", b));
        }

        String full = raw + "|" + hash.toString();
        String encoded = Base64.getEncoder().encodeToString(full.getBytes(StandardCharsets.UTF_8));
        System.out.println("Encoded: " + encoded);

        String decoded = new String(Base64.getDecoder().decode(encoded), StandardCharsets.UTF_8);
        System.out.println("Decoded: " + decoded);
    }
}
```


## 🐍 Python 예시
```python
import hashlib
import base64

raw = "JJH|2025-08-01|2025-09-30|JungHwan"
hash_value = hashlib.sha256(raw.encode()).hexdigest()
full = f"{raw}|{hash_value}"

encoded = base64.b64encode(full.encode()).decode()
print("Encoded:", encoded)

decoded = base64.b64decode(encoded).decode()
print("Decoded:", decoded)
```


## 🔍 결과 예시 (공통)
```
JJH|2025-08-01|2025-09-30|JungHwan|e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
```

Base64 인코딩 결과 (예시):
```
SkpIfDIwMjUtMDgtMDF8MjAyNS0wOS0zMHxKdW5nSHdhbnxlM2IwYzQ0Mjk4ZmMxYzE0OWFmYmY0Yzg5OTZmYjkyNDI3YWU0MWU0NjQ5YjkzNGNhNDk1OTkxYjc4NTJiODU1
```



# 전체 로직 
"JJH|StartTime|EndTime|ID_NAME|SHA256" 구조의 라이선스 문자열을 생성하고, SHA256 해시를 붙인 후 검증까지 수행하는 전체 로직

## ✅ C# 예시
``` csharp
using System;
using System.Text;
using System.Security.Cryptography;

class Program
{
    static string GenerateLicense(string start, string end, string id)
    {
        string raw = $"JJH|{start}|{end}|{id}";
        using var sha256 = SHA256.Create();
        byte[] hashBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(raw));
        string hash = BitConverter.ToString(hashBytes).Replace("-", "").ToLower();
        string full = $"{raw}|{hash}";
        return Convert.ToBase64String(Encoding.UTF8.GetBytes(full));
    }

    static bool VerifyLicense(string encoded)
    {
        string decoded = Encoding.UTF8.GetString(Convert.FromBase64String(encoded));
        var parts = decoded.Split('|');
        if (parts.Length != 5) return false;

        string raw = string.Join("|", parts[0], parts[1], parts[2], parts[3]);
        string providedHash = parts[4];

        using var sha256 = SHA256.Create();
        byte[] hashBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(raw));
        string expectedHash = BitConverter.ToString(hashBytes).Replace("-", "").ToLower();

        return expectedHash == providedHash;
    }

    static void Main()
    {
        string license = GenerateLicense("2025-08-01", "2025-09-30", "JungHwan");
        Console.WriteLine("Encoded License: " + license);
        Console.WriteLine("Valid: " + VerifyLicense(license));
    }
}

```

## ☕ Java 예시

```java
import java.nio.charset.StandardCharsets;
import java.security.MessageDigest;
import java.util.Base64;

public class LicenseVerifier {
    public static String generateLicense(String start, String end, String id) throws Exception {
        String raw = "JJH|" + start + "|" + end + "|" + id;
        MessageDigest digest = MessageDigest.getInstance("SHA-256");
        byte[] hashBytes = digest.digest(raw.getBytes(StandardCharsets.UTF_8));
        StringBuilder hash = new StringBuilder();
        for (byte b : hashBytes) hash.append(String.format("%02x", b));
        String full = raw + "|" + hash;
        return Base64.getEncoder().encodeToString(full.getBytes(StandardCharsets.UTF_8));
    }

    public static boolean verifyLicense(String encoded) throws Exception {
        String decoded = new String(Base64.getDecoder().decode(encoded), StandardCharsets.UTF_8);
        String[] parts = decoded.split("\\|");
        if (parts.length != 5) return false;

        String raw = String.join("|", parts[0], parts[1], parts[2], parts[3]);
        String providedHash = parts[4];

        MessageDigest digest = MessageDigest.getInstance("SHA-256");
        byte[] hashBytes = digest.digest(raw.getBytes(StandardCharsets.UTF_8));
        StringBuilder expectedHash = new StringBuilder();
        for (byte b : hashBytes) expectedHash.append(String.format("%02x", b));

        return expectedHash.toString().equals(providedHash);
    }

    public static void main(String[] args) throws Exception {
        String license = generateLicense("2025-08-01", "2025-09-30", "JungHwan");
        System.out.println("Encoded License: " + license);
        System.out.println("Valid: " + verifyLicense(license));
    }
}

```

## 🐍 Python 예시
```python
import hashlib
import base64

def generate_license(start, end, id_name):
    raw = f"JJH|{start}|{end}|{id_name}"
    hash_value = hashlib.sha256(raw.encode()).hexdigest()
    full = f"{raw}|{hash_value}"
    return base64.b64encode(full.encode()).decode()

def verify_license(encoded):
    decoded = base64.b64decode(encoded).decode()
    parts = decoded.split("|")
    if len(parts) != 5:
        return False
    raw = "|".join(parts[:4])
    provided_hash = parts[4]
    expected_hash = hashlib.sha256(raw.encode()).hexdigest()
    return expected_hash == provided_hash

license = generate_license("2025-08-01", "2025-09-30", "JungHwan")
print("Encoded License:", license)
print("Valid:", verify_license(license))
```


## 🧠 C++ 예시 (OpenSSL 필요)
```cpp
#include <iostream>
#include <sstream>
#include <iomanip>
#include <openssl/sha.h>
#include <string>
#include <vector>
#include <cstring>
#include <cassert>
#include <openssl/bio.h>
#include <openssl/evp.h>

std::string sha256(const std::string& input) {
    unsigned char hash[SHA256_DIGEST_LENGTH];
    SHA256((const unsigned char*)input.c_str(), input.size(), hash);
    std::ostringstream oss;
    for (int i = 0; i < SHA256_DIGEST_LENGTH; ++i)
        oss << std::hex << std::setw(2) << std::setfill('0') << (int)hash[i];
    return oss.str();
}

std::string base64_encode(const std::string& input) {
    BIO *bio, *b64;
    BUF_MEM *bufferPtr;
    b64 = BIO_new(BIO_f_base64());
    bio = BIO_new(BIO_s_mem());
    bio = BIO_push(b64, bio);
    BIO_set_flags(bio, BIO_FLAGS_BASE64_NO_NL);
    BIO_write(bio, input.c_str(), input.length());
    BIO_flush(bio);
    BIO_get_mem_ptr(bio, &bufferPtr);
    std::string result(bufferPtr->data, bufferPtr->length);
    BIO_free_all(bio);
    return result;
}

std::string base64_decode(const std::string& input) {
    BIO *bio, *b64;
    char buffer[input.length()];
    memset(buffer, 0, sizeof(buffer));
    b64 = BIO_new(BIO_f_base64());
    bio = BIO_new_mem_buf(input.c_str(), input.length());
    bio = BIO_push(b64, bio);
    BIO_set_flags(bio, BIO_FLAGS_BASE64_NO_NL);
    int len = BIO_read(bio, buffer, input.length());
    BIO_free_all(bio);
    return std::string(buffer, len);
}

bool verify_license(const std::string& encoded) {
    std::string decoded = base64_decode(encoded);
    std::istringstream iss(decoded);
    std::string part, raw, provided_hash;
    for (int i = 0; i < 4; ++i) {
        getline(iss, part, '|');
        raw += part + "|";
    }
    getline(iss, provided_hash, '|');
    raw.pop_back(); // remove last '|'
    return sha256(raw) == provided_hash;
}

int main() {
    std::string raw = "JJH|2025-08-01|2025-09-30|JungHwan";
    std::string hash = sha256(raw);
    std::string full = raw + "|" + hash;
    std::string encoded = base64_encode(full);
    std::cout << "Encoded License: " << encoded << "\n";
    std::cout << "Valid: " << (verify_license(encoded) ? "true" : "false") << "\n";
}
```
---


