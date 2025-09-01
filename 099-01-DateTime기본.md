# DateTime 기본
DateTime은 프로그래밍에서 날짜와 시간을 다룰 때 가장 핵심적인 타입 중 하나.

## 🕒 C# DateTime 요약
```csharp
class Program
{
    static void Main(string[] args)
    {
        var dt1 = new DateTime(2020, 1, 1);
        var dt2 = DateTime.Now;

        Console.WriteLine(dt1);
        //01-01(수) 오전 12:00:00

        Console.WriteLine(dt2);
        //12-16(월) 오후 2:34:34
    }
}

```

```csharp
var dt1 = new DateTime(2020, 1, 1);      // 특정 날짜 생성
var dt2 = DateTime.Now;                 // 현재 날짜/시간

Console.WriteLine(dt1);                 // 2020-01-01 오전 12:00:00
Console.WriteLine(dt2);                 // 현재 시스템 시간
```

- DateTime.Now: 현재 시스템의 날짜와 시간
- DateTime.Today: 시간은 00:00:00으로 설정된 오늘 날짜
- DateTime.UtcNow: UTC 기준 현재 시간
- AddDays(), AddHours() 등으로 시간 연산 가능
- ToString("yyyy-MM-dd HH:mm:ss")으로 포맷 지정 가능

## 💻 C++ 날짜/시간 처리
C은 <chrono> 라이브러리를 사용하며, C#처럼 직관적이지는 않지만 C20부터는 많이 개선됐어요.
```cpp
#include <iostream>
#include <chrono>
#include <ctime>

int main() {
    auto now = std::chrono::system_clock::now();              // 현재 시간
    std::time_t now_time = std::chrono::system_clock::to_time_t(now);
    std::cout << std::ctime(&now_time);                       // 문자열로 출력
}
```

- std::chrono::system_clock: 시스템 시간
- std::time_t: C 스타일 시간 표현
- std::tm: 날짜/시간 구조체
- 포맷팅은 strftime() 또는 std::format (C++20) 사용

## ☕ Java LocalDateTime / Date
```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class Main {
    public static void main(String[] args) {
        LocalDateTime dt1 = LocalDateTime.of(2020, 1, 1, 0, 0);
        LocalDateTime dt2 = LocalDateTime.now();

        System.out.println(dt1);  // 2020-01-01T00:00
        System.out.println(dt2);  // 현재 시간

        // 포맷 지정
        DateTimeFormatter fmt = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        System.out.println(dt2.format(fmt));
    }
}

```

- LocalDateTime: 날짜와 시간 (타임존 없음)
- ZonedDateTime: 타임존 포함
- DateTimeFormatter: 포맷 지정
- Duration, Period로 시간 차 계산 가능

## 🐍 Python datetime
```python
from datetime import datetime

dt1 = datetime(2020, 1, 1)
dt2 = datetime.now()

print(dt1)  # 2020-01-01 00:00:00
print(dt2)  # 현재 시간

# 포맷 지정
print(dt2.strftime("%Y-%m-%d %H:%M:%S"))
```

- datetime.now(): 현재 시간
- datetime.utcnow(): UTC 기준 시간
- strftime(): 포맷 지정
- timedelta: 시간 연산

## 📊 언어별 DateTime 비교

| 언어   | 현재 시간                      | 특정 날짜 생성              | 포맷 지정 방식                  | 시간 연산 방식                  |
|--------|-------------------------------|-----------------------------|--------------------------------|---------------------------------|
| C#     | `DateTime.Now`                | `new DateTime(...)`         | `ToString("yyyy-MM-dd")`       | `AddDays()`, `AddHours()`       |
| C++    | `chrono::system_clock::now()` | `std::tm`, `chrono`         | `strftime()`, `std::format`    | `duration`, `time_point`        |
| Java   | `LocalDateTime.now()`         | `LocalDateTime.of(...)`     | `DateTimeFormatter`            | `plusDays()`, `minusHours()`    |
| Python | `datetime.now()`              | `datetime(...)`             | `strftime()`                   | `timedelta`                     |

---


