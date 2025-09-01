# DateTime format

## ✅ C# DateTime 포맷 예제
```csharp
var date = new DateTime(2015, 11, 11);

Console.WriteLine(date.ToString("yyyy-MM-dd"));           // 2015-11-11
Console.WriteLine(date.ToString("MM/dd/yyyy"));           // 11/11/2015
Console.WriteLine(date.ToString("dddd, MMMM dd, yyyy"));  // 수요일, 11월 11, 2015
Console.WriteLine(date.ToString("yyyy년 MM월 dd일"));       // 2015년 11월 11일
Console.WriteLine($"It's {date:MMMM dd, yyyy}, make a wish!"); // It's 11월 11, 2015, make a wish!
```

## 🧠 C++ 날짜 포맷 (C++20 기준)
```cpp
#include <iostream>
#include <chrono>
#include <format>

int main() {
    using namespace std::chrono;
    auto date = year{2015}/11/11;

    std::cout << std::format("{:%Y-%m-%d}", date) << "\n";         // 2015-11-11
    std::cout << std::format("{:%m/%d/%Y}", date) << "\n";         // 11/11/2015
    std::cout << std::format("{:%A, %B %d, %Y}", date) << "\n";    // Wednesday, November 11, 2015
}

```
💡 C에서 std::format은 C20 이상에서만 지원되며, <chrono>와 함께 사용하면 날짜 포맷이 매우 유연해집니다.


## ☕ Java DateTimeFormatter 예제
```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class Main {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2015, 11, 11);

        System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy-MM-dd")));           // 2015-11-11
        System.out.println(date.format(DateTimeFormatter.ofPattern("MM/dd/yyyy")));           // 11/11/2015
        System.out.println(date.format(DateTimeFormatter.ofPattern("EEEE, MMMM dd, yyyy")));  // Wednesday, November 11, 2015
        System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy년 MM월 dd일")));       // 2015년 11월 11일
    }
}

```

## 🐍 Python strftime 예제
```python
from datetime import datetime

date = datetime(2015, 11, 11)

print(date.strftime("%Y-%m-%d"))           # 2015-11-11
print(date.strftime("%m/%d/%Y"))           # 11/11/2015
print(date.strftime("%A, %B %d, %Y"))      # Wednesday, November 11, 2015
print(date.strftime("%Y년 %m월 %d일"))      # 2015년 11월 11일
```



## 📊 언어별 DateTime 포맷 비교

| 언어     | 포맷 함수 / 클래스              | 포맷 문자열               | 출력 예시                        |
|----------|----------------------------------|----------------------------|----------------------------------|
| C#       | `ToString("...")`               | `"MMMM dd, yyyy"`         | `11월 11, 2015`                  |
| C++      | `std::format("{:%...}")`        | `"{:%A, %B %d, %Y}"`      | `Wednesday, November 11, 2015`  |
| Java     | `DateTimeFormatter.ofPattern()` | `"EEEE, MMMM dd, yyyy"`   | `Wednesday, November 11, 2015`  |
| Python   | `strftime("%...")`              | `"%A, %B %d, %Y"`         | `Wednesday, November 11, 2015`  |


#  로케일(Localization), 타임존(Timezone), 그리고 시간 포함 포맷(Time Format)

로케일(Localization), 타임존(Timezone), 그리고 **시간 포함 포맷(Time Format)**을 제대로 설정하는 건 글로벌 앱이나 시스템에서 정말 중요.

## 🌐 1. 로케일 변경 (Localization)
### ✅ C#
```csharp
var date = new DateTime(2015, 11, 11, 18, 30, 0);
var culture = new System.Globalization.CultureInfo("en-US");
Console.WriteLine(date.ToString("F", culture)); // Wednesday, November 11, 2015 6:30:00 PM
```

### 🧠 C++ (C++20 이상)
```cpp
#include <iostream>
#include <chrono>
#include <format>
#include <locale>

int main() {
    auto date = std::chrono::local_days{std::chrono::year{2015}/11/11};
    std::cout << std::format(std::locale("en_US.UTF-8"), "{:%A, %B %d, %Y}", date);
}

```
⚠️ 로케일 설정은 시스템에 해당 로케일이 설치되어 있어야 작동합니다.

### ☕ Java
```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Locale;

LocalDateTime date = LocalDateTime.of(2015, 11, 11, 18, 30);
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("EEEE, MMMM dd, yyyy h:mm a", Locale.US);
System.out.println(date.format(formatter)); // Wednesday, November 11, 2015 6:30 PM
```

### 🐍 Python
```python
from datetime import datetime
import locale

locale.setlocale(locale.LC_TIME, 'en_US.UTF-8')
date = datetime(2015, 11, 11, 18, 30)
print(date.strftime("%A, %B %d, %Y %I:%M %p"))  # Wednesday, November 11, 2015 06:30 PM

```

## 🕒 2. 타임존 포함 포맷 (Timezone-aware formatting)

### ✅ C#
```csharp
var dateUtc = new DateTime(2015, 11, 11, 9, 30, 0, DateTimeKind.Utc);
var koreaTimeZone = TimeZoneInfo.FindSystemTimeZoneById("Korea Standard Time");
var localTime = TimeZoneInfo.ConvertTimeFromUtc(dateUtc, koreaTimeZone);
Console.WriteLine(localTime.ToString("yyyy-MM-dd HH:mm:ss zzz")); // 2015-11-11 18:30:00 +09:00
```

## ☕ Java
```java
import java.time.ZonedDateTime;
import java.time.ZoneId;
import java.time.format.DateTimeFormatter;

ZonedDateTime date = ZonedDateTime.of(2015, 11, 11, 18, 30, 0, 0, ZoneId.of("Asia/Seoul"));
System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm z"))); // 2015-11-11 18:30 KST
```


## 🐍 Python
```python
from datetime import datetime
import pytz

tz = pytz.timezone("Asia/Seoul")
date = datetime(2015, 11, 11, 18, 30, tzinfo=tz)
print(date.strftime("%Y-%m-%d %H:%M %Z"))  # 2015-11-11 18:30 KST
```

💡 Python은 pytz 또는 zoneinfo (Python 3.9+)를 사용해 타임존을 처리합니다.


## ⏰ 3. 시간까지 포함된 DateTime 포맷 비교

| 언어     | 포맷 문자열                    | 출력 예시                  |
|----------|-------------------------------|----------------------------|
| C#       | `"yyyy-MM-dd HH:mm:ss"`       | `2015-11-11 18:30:00`      |
| C++      | `"{:%Y-%m-%d %H:%M:%S}"`      | `2015-11-11 18:30:00`      |
| Java     | `"yyyy-MM-dd HH:mm:ss"`       | `2015-11-11 18:30:00`      |
| Python   | `"%Y-%m-%d %H:%M:%S"`         | `2015-11-11 18:30:00`      |

---



## 📅 DateTime Format Specifiers (Part 1)
| Specifier | Meaning                        | Sample   | Result                          |
|-----------|--------------------------------|----------|----------------------------------|
| d         | Date                           | {0:d}    | 7/6/2016                         |
| dd        | Day, zero-padded               | {0:dd}   | 06                               |
| ddd       | Short day name                 | {0:ddd}  | Wed                              |
| dddd      | Long day name                  | {0:dddd} | Wednesday                        |
| D         | Long date                      | {0:D}    | Wednesday, July 6, 2016          |
| f         | Full date and time, short      | {0:f}    | Wednesday, July 6, 2016 6:30 PM  |
| ff        | Second fractions, 2 digits     | {0:ff}   | 20                               |
| fff       | Second fractions, 3 digits     | {0:fff}  | 201                              |
| ffff      | Second fractions, 4 digits     | {0:ffff} | 2016                             |
| F         | Full date and time, long       | {0:F}    | Wednesday, July 6, 2016 6:30:14 PM |


## ⏰ DateTime Format Specifiers (Part 2)
| Specifier | Meaning                             | Sample   | Result                             |
|-----------|-------------------------------------|----------|-------------------------------------|
| g         | Default date and time               | {0:g}    | 7/6/2016 6:30 PM                    |
| gg        | Era                                 | {0:gg}   | AD                                  |
| hh        | Hour (2 digits, 12-hour)            | {0:hh}   | 06                                  |
| H         | Hour (24-hour)                      | {0:H}    | 18                                  |
| M         | Month and day                       | {0:M}    | July 6                              |
| m         | Minutes, zero-padded                | {0:m}    | 30                                  |
| MM        | Month, zero-padded                  | {0:MM}   | 07                                  |
| MMM       | Month name                          | {0:MMM}  | Jul                                 |
| MMMM      | Full month name                     | {0:MMMM} | July                                |
| r         | RFC1123 date                        | {0:r}    | Wed, 06 Jul 2016 18:30:14 GMT       |
| s         | Sortable date string                | {0:s}    | 2016-07-06T18:30:14                 |
| t         | Short time                          | {0:t}    | 6:30 PM                             |
| T         | Long time                           | {0:T}    | 6:30:14 PM                          |
| tt        | AM/PM                               | {0:tt}   | PM                                  |
| u         | Universal sortable local time       | {0:u}    | 2016-07-06 18:30:14Z                |
| U         | Universal GMT                       | {0:U}    | Wednesday, July 6, 2016 9:30:14 AM  |
| y         | 2 digit year                        | {0:y}    | 16                                  |
| yy        | 2 digit year                        | {0:yy}   | 16                                  |
| yyyy      | 4 digit year                        | {0:yyyy} | 2016                                |
| zz        | 2 digit timezone offset             | {0:zz}   | +09                                 |
| zzz       | Full time zone offset               | {0:zzz}  | +09:00                              |
---






