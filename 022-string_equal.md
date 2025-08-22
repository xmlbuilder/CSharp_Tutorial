# 🧵 C#에서의 문자열 비교
## ✅ == 연산자
- C#에서는 string이 참조 타입이지만, == 연산자는 값 비교를 수행하도록 오버로드되어 있음.
- 즉, "hello" == "hello"는 내용이 같으면 true를 반환.
- 하지만 대소문자 구분은 기본적으로 적용됨.
```csharp
string a = "Windows";
string b = "windows";
Console.WriteLine(a == b); // False
```

## ✅ String.Compare
- 대소문자 무시 옵션을 줄 수 있어 더 유연함.
- String.Compare(a, b, true) → true는 대소문자 무시.
Console.WriteLine(String.Compare(a, b, true) == 0); // True


## ✅ 메모리 공유
- C#은 문자열 interning을 통해 동일한 리터럴 문자열은 같은 메모리 주소를 공유함.
- 예: "hello"와 "hello"는 같은 주소를 참조함.
- 하지만 new string(...)으로 생성하면 다른 참조가 될 수 있음.

## ☕ Java에서의 문자열 비교
### ❌ == 연산자
- Java에서 ==는 참조 비교를 수행함.
- 즉, 두 문자열이 같은 객체를 가리킬 때만 true.
```java
String a = "Windows";
String b = "Windows";
System.out.println(a == b); // true (interned)

String c = new String("Windows");
System.out.println(a == c); // false (다른 객체)
```

### ✅ .equals() 메서드
- 문자열의 내용 비교를 위해 사용.
- 대소문자 구분함.
```java
System.out.println(a.equals(b)); // true
System.out.println(a.equals("windows")); // false
```

### ✅ .equalsIgnoreCase()
- 대소문자 무시하고 비교.
```java
System.out.println(a.equalsIgnoreCase("windows")); // true
```


## 💻 C++에서의 문자열 비교
### ✅ == 연산자
- C++의 std::string은 값 타입이므로 ==는 내용 비교를 수행함.
- 참조가 아닌 값 자체를 비교함.
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    string a = "Windows";
    string b = "windows";

    if (a == b) cout << "Same\n";
    else cout << "Different\n"; // 출력됨

    // 대소문자 무시하려면 별도 처리 필요
}
```

### ❌ 메모리 공유 없음
- C++의 std::string은 값을 복사하므로, 같은 문자열이라도 다른 메모리에 존재함.
- 문자열 interning 같은 최적화는 기본적으로 없음.

## 🔍 비교 요약
| 언어 | == 의미 | 대소문자 무시 | 메모리 공유 |
|-----|---------|-------------|-----------| 
| C# | 값 비교 (오버로드됨) | String.Compare(..., true) | 있음 (interning) | 
| Java | 참조 비교 | .equalsIgnoreCase() | 있음 (interning) | 
| C++ | 값 비교 | 직접 구현 필요 | 없음 | 
----

