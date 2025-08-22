# String Equality 비교 --- C# vs Java vs C++

아래 내용은 **값(content) 비교**와 **참조(reference) 비교**를 중심으로,
문자열 상수 풀(인터닝)까지 함께 정리합니다.\
질문 코드의 포인트(인터닝/복사/참조비교)도 각 언어에 대응시켜
설명합니다.

------------------------------------------------------------------------

## 1) 핵심 개념 한눈에

| 구분             | C#                                                  | Java                           | C++ |
|------------------------------|-----------------------------------------------------|--------------------------------|---------------|
| 값 비교 기본     | `==`가 **내용 비교** (연산자 오버로드)              | `equals()`가 **내용 비교**     | `std::string`의 `==`가 **내용 비교** |
| 참조 비교        | `ReferenceEquals(a,b)` 또는 `(object)a == (object)b` | `==` (참조/포인터 비교)        | 객체 주소 비교: `&s1 == &s2` (동일 객체 여부) |
| 문자열 리터럴 풀 | 있음. 리터럴 자동 인터닝 (`string.Intern`)          | 있음. 리터럴 자동 인터닝 (`String.intern()`) | 표준 **보장 없음** (컴파일러가 상수 병합할 수도, 안 할 수도) |
| 대소문자 무시    | `Equals(x,y,StringComparison.OrdinalIgnoreCase)`    | `equalsIgnoreCase()`(간단), `Collator`(로캘) | 표준 라이브러리 직접 지원 없음(직접 변환/비교 또는 라이브러리 사용) |
| 주의점           | `String.Copy`는 .NET Core에서 **비권장**(obsolete). 참조 비교는 인터닝에 영향 받음 | 리터럴은 풀에 intern됨 → `==`가 **우연히** true일 수 있으나, **항상 `equals` 사용** | `const char*` 리터럴의 `==`는 **포인터 비교**. 내용 비교엔 `std::string` 또는 `std::strcmp` |


## 2) 질문 코드(C#) 포인트 해설

``` csharp
object aObj = new object();
object bObj = aObj;
Console.WriteLine(ReferenceEquals(aObj, bObj)); // True (같은 객체 참조)

string a = "hello";
string b = String.Copy(a); // .NET Core/5+에선 obsolete
string c = "hello";

Console.WriteLine(a == b);                 // True (내용 비교)
Console.WriteLine((object)a == (object)b); // False (참조 비교: 다른 인스턴스)
Console.WriteLine(ReferenceEquals(a, b));  // False
Console.WriteLine(ReferenceEquals(a, c));  // True (리터럴 인터닝)
Console.WriteLine((object)a == (object)c); // True (같은 참조)
```

-   **C#에서 `string`의 `==`는 내용 비교**입니다.\
-   **참조 비교**가 필요하면 `ReferenceEquals(a,b)` 또는
    `(object)a==(object)b`로 캐스팅.\
-   **리터럴 인터닝** 때문에 `a`와 `c`는 같은 `"hello"` 인스턴스를
    가리킴 → 참조 비교도 `True`.\
-   `String.Copy`는 **과거**에 "새 인스턴스 강제" 용도로 쓰였으나, 최신
    .NET에서는 의미가 약해지고 **비권장**입니다.

------------------------------------------------------------------------

## 3) Java에서 동일 개념

``` java
String a = "hello";
String b = new String(a);  // 힙에 새 인스턴스
String c = "hello";        // 리터럴 → intern 풀 공유

System.out.println(a.equals(b)); // true (내용 비교)
System.out.println(a == b);      // false (참조 비교)
System.out.println(a == c);      // true (intern 공유로 참조 동일)

String d = new String("hello").intern();
System.out.println(a == d);      // true (intern 강제)
```

-   **Java의 `==`는 참조 비교**. 내용 비교는 `equals()` 사용.\
-   리터럴은 **자동 intern**됨.\
-   `String.intern()`으로 수동 intern 가능.

------------------------------------------------------------------------

## 4) C++에서 동일 개념

``` cpp
std::string a = "hello";
std::string b = a; // 내용 복사
std::string c = "hello";

std::cout << (a == b) << "\n";   // true (내용 비교)
std::cout << (&a == &b) << "\n"; // false (객체 주소 다름)

const char* p1 = "hello";
const char* p2 = "hello";
std::cout << (p1 == p2) << "\n";         // 보장 없음
std::cout << (std::strcmp(p1, p2) == 0); // true (내용 비교)
```

-   `std::string`의 `==`는 **내용 비교**.\
-   C 문자열은 포인터 비교라 **내용 비교 시 `strcmp` 필수**.\
-   리터럴 포인터 동일성은 표준 보장 없음.

------------------------------------------------------------------------

## 5) 대소문자/로캘 비교

| 언어 | 방법 |
|------|---------------------------------------------------------|
| C#   | `string.Equals(a,b,StringComparison.OrdinalIgnoreCase)` |
| Java | `equalsIgnoreCase()`, `Collator`                        |
| C++  | 직접 소문자 변환 후 비교, 또는 Boost/ICU 사용           |


## 6) 상황별 "올바른 선택" 요약

| 목적           | C#                       | Java          | C++                       |
|----------------|--------------------------|---------------|---------------------------|
| 값 비교        | `a==b` 또는 `Equals`     | `a.equals(b)` | `std::string a==b` 또는 `strcmp` |
| 참조 비교      | `ReferenceEquals(a,b)`   | `a==b`        | `&a==&b`                  |
| 리터럴 intern 영향 회피 | 항상 값 비교             | 항상 `equals` | 항상 `std::string`/`strcmp` |


## 7) 질문 코드와의 매핑(정리 표)

| 질문 코드 줄                | 의미        | C# 동작 | Java에 대응        | C++에 대응           |
|-----------------------------|-------------|---------|--------------------|----------------------|
| `ReferenceEquals(aObj,bObj)`| 참조 비교   | True    | `aObj==bObj`       | `&objA==&objB`       |
| `a == b`                    | 값 비교     | True    | `a.equals(b)`      | `std::string a==b`   |
| `(object)a == (object)b`    | 참조 비교   | False   | `a==b` (false)     | `&a==&b`             |
| `ReferenceEquals(a,c)`      | 리터럴 intern | True  | `a==c` (intern 공유 true) | 보장 없음 |

---
