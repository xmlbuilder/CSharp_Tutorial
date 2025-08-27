# 🧠 C# - 사용자 정의 객체를 Dictionary Key로 사용하기
## ✅ 핵심 포인트
- Equals()와 GetHashCode()를 반드시 override 해야 함
- Dictionary는 내부적으로 GetHashCode()로 버킷을 찾고, Equals()로 Key 비교함

## 💡 예시: MonthDay 클래스
```csharp
public override bool Equals(object obj)
{
    var other = obj as MonthDay;
    if (other == null) return false;
    return this.Month == other.Month && this.Day == other.Day;
}

public override int GetHashCode()
{
    return Month.GetHashCode() * 31 + Day.GetHashCode();
}
```

## 실제 코드
```csharp
namespace  GrammarTest
{
    public class GrammarTest
    {
        class MonthDay
        {
            public int Day { get; private set; }
            public int Month { get; private set; }
            public MonthDay(int month, int day)
            {
                Month = month;
                Day = day;
            }

            //Key matching
            public override bool Equals(object obj)
            {
                var other = obj as MonthDay;
                if (other == null) throw new ArgumentException();
                return this.Month == other.Month && this.Day == other.Day;
            }

            public override int GetHashCode()
            {
                return Month.GetHashCode() * 31 + Day.GetHashCode();
            }
        }

        public static void Main(String[] args)
        {
            var dict = new Dictionary<MonthDay, string>()
            {
                {new MonthDay(6, 6), "Sample1"},
                {new MonthDay(8, 15), "Sample2"},
                {new MonthDay(10, 3), "Sample3"},
            };
            var md = new MonthDay(8, 15);
            var s = dict[md];
            Console.WriteLine(s);
            //Sample2
        }
    }
}

```

GetHashCode()는 가능한 충돌이 적게끔 설계해야 해요. 31은 흔히 쓰이는 소수 multiplier입니다.


### ⚠️ C#에서 double을 Key로 사용할 때 주의점
```csharp
var dict = new Dictionary<double, string>();
dict[0.1 + 0.2] = "value"; // 0.30000000000000004
Console.WriteLine(dict.ContainsKey(0.3)); // false
```

### 🔍 이유
- double은 부동소수점 오차가 존재하므로 정확한 비교가 어려움
- 0.1 + 0.2 != 0.3 → Dictionary에서 Key로 인식 못함
##### ✅ 해결 방법
- Math.Round() 또는 decimal 사용
- 또는 double을 string으로 변환해서 Key로 사용

```csharp
var dict = new Dictionary<string, string>();
dict[(0.1 + 0.2).ToString("F1")] = "value";
Console.WriteLine(dict["0.3"]); // value
```



## 💻 C++ - 사용자 정의 객체를 std::map의 Key로 사용하기
### ✅ 핵심 포인트
- std::map은 내부적으로 operator<를 사용하여 Key를 정렬하고 비교함
- unordered_map을 사용할 경우에는 ==와 std::hash를 정의해야 함
#### 💡 예시: MonthDay 클래스
```cpp
inline bool operator < (const MonthDay& rhs) const
{
    if (day == rhs.day)
        return (month < rhs.month);
    return (day < rhs.day);
}
```

## 실제 코드
```cpp
class MonthDay
{
public:
    MonthDay(int day, int month)
    {
        this->day = day;
        this->month = month;
    }
    int day {0};
    int month{0};

    inline bool operator < (const MonthDay& rhs) const
    {
        if (day == rhs.day)
        {
            return (month < rhs.month);
        }
        else
        {
            return (day < rhs.day);
        }
    }
};

std::map<MonthDay, std::string> dict = {
    {MonthDay(6, 6), "Sample1"},
    {MonthDay(8, 15), "Sample2"},
    {MonthDay(10, 3), "Sample3"},
};

auto itrFind = dict.find(MonthDay(8, 15));
if(itrFind != dict.end()){
    std::cout << itrFind->second << std::endl; //Sample2
}


```


### ⚠️ C++에서 double을 Key로 사용할 때 주의점
```cpp
std::map<double, std::string> dict;
dict[0.1 + 0.2] = "value"; // 실제 저장된 key는 0.30000000000000004
std::cout << dict.count(0.3) << std::endl; // 0
```

✅ 해결 방법
- std::round() 또는 std::setprecision()으로 정규화
- 또는 std::string으로 변환 후 Key로 사용
```cpp
#include <iomanip>
#include <sstream>

std::ostringstream oss;
oss << std::fixed << std::setprecision(1) << (0.1 + 0.2);
std::string key = oss.str(); // "0.3"
std::map<std::string, std::string> dict;
dict[key] = "value";
```


## ☕ Java - 사용자 정의 객체를 HashMap의 Key로 사용하기
### ✅ 핵심 포인트
- equals()와 hashCode() 반드시 override
```java
@Override
public boolean equals(Object obj) {
    if (!(obj instanceof MonthDay)) return false;
    MonthDay other = (MonthDay) obj;
    return this.month == other.month && this.day == other.day;
}

@Override
public int hashCode() {
    return Objects.hash(month, day);
}
```



## 🐍 Python - 사용자 정의 객체를 dict의 Key로 사용하기
### ✅ 핵심 포인트
```python
- __eq__()와 __hash__()를 정의해야 함
class MonthDay:
    def __init__(self, month, day):
        self.month = month
        self.day = day

    def __eq__(self, other):
        return self.month == other.month and self.day == other.day

    def __hash__(self):
        return hash((self.month, self.day))
```



## 📌 사용자 정의 객체를 Dictionary Key로 사용할 때 언어별 요구사항

| 언어     | 비교 기준        | 해시 기준         | 필수 구현 요소                     |
|----------|------------------|--------------------|------------------------------------|
| C#       | `Equals()`       | `GetHashCode()`    | `override Equals + GetHashCode`   |
| C++      | `operator<`      | `std::hash`        | `operator<` 또는 `== + hash`       |
| Java     | `equals()`       | `hashCode()`       | `override equals + hashCode`      |
| Python   | `__eq__()`       | `__hash__()`       | `define __eq__ + __hash__`        |



---


# 🧠 1. 사용자 정의 비교 함수로 operator< 커스터마이징
std::map은 내부적으로 operator<를 사용하여 key를 정렬하고 비교합니다.  따라서 double의 오차를 감안한 비교 함수를 정의하면 안정적인 동작을 기대할 수 있습니다.
```cpp
struct DoubleCompare {
    bool operator()(double a, double b) const {
        constexpr double epsilon = 1e-9;
        return (a - b) < -epsilon;
    }
};

std::map<double, std::string, DoubleCompare> dict;
dict[0.1 + 0.2] = "value";
std::cout << dict.count(0.3) << std::endl; // 1
```


## 🔍 설명: a < b 대신 (a - b) < -epsilon을 사용함으로써, a와 b가 거의 같을 경우에는 같다고 간주하게 됩니다.

## 🧱 2. operator==를 활용한 래퍼 타입 정의
```cpp
std::unordered_map을 사용할 경우에는 operator==와 std::hash를 정의한 사용자 타입을 만들 수 있습니다.
struct ApproxDouble {
    double value;

    bool operator==(const ApproxDouble& other) const {
        constexpr double epsilon = 1e-9;
        return std::abs(value - other.value) < epsilon;
    }
};

namespace std {
    template <>
    struct hash<ApproxDouble> {
        std::size_t operator()(const ApproxDouble& d) const {
            return std::hash<int>()(static_cast<int>(d.value * 1000)); // 정밀도 조절
        }
    };
}

std::unordered_map<ApproxDouble, std::string> dict;
dict[{0.1 + 0.2}] = "value";
std::cout << dict[{0.3}] << std::endl; // "value"
```


## 🔧 설명: operator==로 근사 비교를 정의하고, hash에서는 정밀도를 조절하여 같은 값으로 간주되도록 처리합니다.

✅ 요약
| 방법                             | 사용 컨테이너         | 핵심 아이디어                          |
|----------------------------------|------------------------|----------------------------------------|
| 사용자 정의 `operator<`         | `std::map`             | 근사 비교를 통한 정렬 기준 설정        |
| 사용자 정의 `operator==` + `hash` | `std::unordered_map`   | 근사 비교와 해시 정규화를 통한 키 비교 |



## ☕ Java에서 double을 Key로 사용할 때의 주의점
🔧 기본 방식: Double 객체 사용
Java에서는 double primitive 타입을 직접 key로 사용할 수 없고, 자동으로 Double 객체로 박싱됩니다.  이때 Double.equals()와 Double.hashCode()는 정확한 값 비교를 수행하므로, 부동소수점 오차가 그대로 반영됩니다.
```java
Map<Double, String> map = new HashMap<>();
map.put(0.1 + 0.2, "value");
System.out.println(map.containsKey(0.3)); // false
```

### ✅ 해결 방법: 근사 비교를 위한 래퍼 클래스 정의
```java
public class ApproxDouble {
    private final double value;
    private static final double EPSILON = 1e-9;

    public ApproxDouble(double value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof ApproxDouble)) return false;
        ApproxDouble other = (ApproxDouble) obj;
        return Math.abs(this.value - other.value) < EPSILON;
    }

    @Override
    public int hashCode() {
        return (int)(value * 1000); // 정밀도 조절
    }
}
```

사용 예시:
```java
Map<ApproxDouble, String> map = new HashMap<>();
map.put(new ApproxDouble(0.1 + 0.2), "value");
System.out.println(map.get(new ApproxDouble(0.3))); // "value"
```



## 📌 요약: Java에서의 대응 방식 (Markdown 표)

| 언어  | 연산자 오버로딩 | 비교 방식       | 해결 방법                          |
|-------|------------------|----------------|------------------------------------|
| C++   | 있음             | `operator<`, `==` | 사용자 정의 비교 연산자 또는 래퍼 |
| Java  | 없음             | `equals`, `hashCode` | 래퍼 클래스 정의로 근사 비교 구현 |




