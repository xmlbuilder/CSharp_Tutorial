# Currency Formatting
.NET의 string.Format을 활용한 통화 포맷팅 예제.

## 💰 .NET (C#)
```csharp
using System.Globalization;


NumberFormatInfo nfi = new CultureInfo("en-US", false).NumberFormat;

string.Format("{0:c}", 112.236677);       // $112.23
string.Format("{0:C1}", 112.236677);      // $112.2
string.Format("{0:C3}", 112.236677);      // $112.237
string.Format("{0:C4}", 112.236677);      // $112.2367
string.Format("{0:C9}", 112.236677);      // $112.236677000
string.Format(new CultureInfo("en-US"), "{0:c}", 112.236677); // $112.24
```

- C 포맷 문자열은 통화 형식
- 숫자 뒤의 숫자는 소수점 자리수

## 🧮 C++
```cpp
C에서는 기본적으로 std::put_money 또는 std::format (C20 이상) 또는 std::stringstream을 활용해 통화 포맷을 구현할 수 있어. 하지만 지역화된 통화 기호는 직접 처리해야 할 수도 있어.
#include <iostream>
#include <iomanip>
#include <locale>
#include <sstream>

int main() {
    double amount = 112.236677;

    std::stringstream ss;
    ss.imbue(std::locale("en_US.UTF-8")); // 시스템에 따라 locale 설치 필요
    ss << std::showbase << std::put_money(amount * 100); // 단위는 센트

    std::cout << ss.str() << std::endl; // $112.24
}
```


- put_money는 센트 단위로 입력해야 함
- 소수점 자리수 조절은 직접 std::fixed와 std::setprecision으로 처리 가능

## ☕ Java
Java는 NumberFormat 클래스를 통해 통화 포맷을 쉽게 처리할 수 있어.

```java
import java.text.NumberFormat;
import java.util.Locale;

public class CurrencyFormatExample {
    public static void main(String[] args) {
        double amount = 112.236677;

        NumberFormat nf = NumberFormat.getCurrencyInstance(Locale.US);
        System.out.println(nf.format(amount)); // $112.24

        // 소수점 자리수 조절
        nf.setMinimumFractionDigits(3);
        nf.setMaximumFractionDigits(3);
        System.out.println(nf.format(amount)); // $112.237
    }
}
```
- getCurrencyInstance로 통화 형식 지정
- setMinimumFractionDigits와 setMaximumFractionDigits로 자리수 조절


## 🐍 Python
Python은 locale 모듈이나 babel 라이브러리를 활용해 통화 포맷을 처리할 수 있어.
기본 locale 사용
```python
import locale

locale.setlocale(locale.LC_ALL, 'en_US.UTF-8')
amount = 112.236677
formatted = locale.currency(amount, grouping=True)
print(formatted)  # $112.24
```

babel 라이브러리 사용 (더 유연함)

```python
from babel.numbers import format_currency

amount = 112.236677
print(format_currency(amount, 'USD', locale='en_US'))         # $112.24
print(format_currency(amount, 'USD', locale='en_US', format=u'¤#,##0.000'))  # $112.237
print(format_currency(amount, 'USD', locale='en_US', format=u'¤#,##0.000000000'))  # $112.236677000

```
- ¤는 통화 기호 자리
- format_currency는 포맷 문자열로 소수점 자리수 조절 가능

### ✨ 실전에서 유용한 이유
| 방식 | 결과 예시 | 유연성 | 
| '$#,##0.00' | 항상 $112.24 | ❌ 고정 | 
| '¤#,##0.00' | $112.24, ₩112, €112.24 | ✅ 지역에 따라 자동 | 
즉, ¤는 **국제화(i18n)**와 **지역화(l10n)**를 고려한 설계에서 아주 중요한 역할을 해.



## 📊 비교 요약
| 언어 | 통화 포맷 클래스/함수 | 소수점 자리수 조절 방법 |
|------|--------------------|----------------------| 
| C# (.NET) | string.Format("{0:Cn}") | C1, C3, C4, C9 등으로 지정 | 
| C++ | std::put_money, stringstream | std::fixed, std::setprecision | 
| Java | NumberFormat.getCurrencyInstance() | setMinimumFractionDigits() 등 | 
| Python | locale.currency() 또는 babel.format_currency() | 포맷 문자열로 지정 (¤#,##0.000) | 
----











