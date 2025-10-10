# C# `typeof` / `GetType()` vs C++ `decltype` / `typeid`

- C#의 `typeof`와 `GetType()`은 **타입 메타데이터** 를 얻는 수단(런타임 반사 중심)이고,  
- C++의 `decltype`은 **표현식의 타입을 문맥에 복사** 하는 수단(컴파일 타임 추론)입니다.  
- C++에서 런타임 타입을 얻고 싶다면 `typeid`(RTTI)가 대응합니다.

---

## 1) 한눈에 보는 비교

| 구분 | C# `typeof(T)` | C# `obj.GetType()` | C++ `decltype(expr)` | C++ `typeid(expr)` |
|---|---|---|---|---|
| **목적** | 컴파일 시점에 타입 핸들(`System.Type`) 얻기 | **런타임** 실제 인스턴스의 정확한 타입 얻기 | **컴파일 시점** 표현식의 타입을 _그대로_ 복제 | **런타임** 타입 정보(`std::type_info`) 얻기 |
| **평가 시점** | 컴파일 타임(평가 없음) | 런타임(객체가 필요) | 컴파일 타임(unevaluated operand) | 런타임(다형성일 때 동적 타입도 가능) |
| **널 안전성** | 널과 무관(항상 안전) | `obj == null`이면 `NullReferenceException` | 표현식 평가 안 함 → 안전 | 표현식은 평가됨\* |
| **다형성 반영** | 반영 안 함(정적 T) | **반영함**(동적 타입) | 반영 안 함(정적 규칙) | glvalue + 가상 소멸자 등 **반영함** |
| **대표 사용처** | 제네릭 메타데이터, 리플렉션 | 런타임 로깅/디버깅/스위치 | 템플릿 반환형/완전 전달 | RTTI 기반 디스패치/디버깅 |
| **예시** | `typeof(List<int>)` | `obj.GetType()` | `decltype(x+y)` | `typeid(*p)` |

\* `typeid`는 피연산자가 다형적(glvalue)일 때만 동적 타입을 제공합니다. 비다형성은 정적 타입.

---

## 2) C# `typeof`와 `GetType()` 자세히

### 핵심 차이
- `typeof(T)` : **정적 타입** T의 메타데이터(`System.Type`)를 얻습니다. _항상 안전_ 하며 객체가 필요 없습니다.
- `obj.GetType()` : **객체의 실제(동적) 타입** 을 돌려줍니다. `null`이면 예외가 발생합니다.

```csharp
Base b = new Derived();
Console.WriteLine(typeof(Base));  // Base
Console.WriteLine(b.GetType());   // Derived (동적 타입)
```

### 제네릭과의 조합
```csharp
static bool IsExact<T>(object o)
    => o != null && o.GetType() == typeof(T);
```

### 배열/타입 유추 예시
```csharp
var items = new[] { 1, 2.0, 3, 4, 5, 5 };
Type t = items.GetType();
Console.WriteLine(t);                            // System.Double[]
Console.WriteLine(t == typeof(int[]));           // False
Console.WriteLine(t == typeof(double[]));        // True
```

> 혼합 리터럴 배열은 더 넓은 형식(double)로 승격되어 `double[]`가 됩니다.

### 자주 쓰는 패턴
```csharp
// 오픈/클로즈드 제네릭 타입 메타데이터
var open  = typeof(List<>);
var close = typeof(List<int>);

// null 안전 비교
bool IsList(object x) =>
    x is not null && x.GetType().IsGenericType
                 && x.GetType().GetGenericTypeDefinition() == typeof(List<>);
```

---

## 3) C++ `decltype` 핵심 규칙 (요약)

`decltype(e)`는 **e의 “식 범주”까지 고려한 타입** 을 그대로 복제합니다. (평가되지 않음)

```cpp
int x = 0;
int& rx = x;
const int cx = x;

decltype(x)    a = x;   // int
decltype((x))  b = x;   // int&   ← 괄호가 붙으면 lvalue → T&
decltype(rx)   c = x;   // int&   ← 이름은 선언된 타입을 그대로
decltype((cx)) d = cx;  // const int&
decltype(std::move(x)) e = std::move(x); // int&&
```

### 템플릿 반환형 및 완전 전달(perfect forwarding)
```cpp
#include <utility>

template<class F, class... Args>
auto invoke(F&& f, Args&&... args)
    -> decltype(std::forward<F>(f)(std::forward<Args>(args)...))
{
    return std::forward<F>(f)(std::forward<Args>(args)...);
}
```

### `auto` vs `decltype(auto)`
```cpp
int x = 0;
int& ref = x;

auto a = ref;          // int        (참조 소실)
decltype(auto) b = ref; // int&       (식 그대로 보존)
```

> 참조/const 속성을 보존하려면 `decltype` 또는 `decltype(auto)`를 사용하세요.

---

## 4) C++ 런타임 타입: `typeid` (RTTI)

```cpp
#include <typeinfo>
#include <iostream>

struct B { virtual ~B() = default; };
struct D : B {};

int main() {
    B* p = new D;
    std::cout << (typeid(*p).name()) << '\n';    // 동적 타입 D
    std::cout << std::boolalpha
              << (typeid(p) == typeid(B*)) << '\n'; // true: 정적 타입
    delete p;
}
```

- 다형적 타입의 **glvalue**에 `typeid`를 적용하면 **동적 타입** 을 제공합니다.
- 비다형성 타입/비참조에는 정적 타입만 얻습니다.

---

## 5) 작업별 선택 가이드

| 하고 싶은 일 | C#에서 | C++에서 |
|---|---|---|
| 정적 타입의 메타데이터 얻기 | `typeof(T)` | `typeid(T)` |
| 객체의 **동적 타입** 얻기 | `obj.GetType()` | `typeid(*p)` (p는 다형적이어야 안전) |
| 식의 타입을 **그대로 복제**해 선언 | (해당 없음) | `decltype(expr)` / `decltype(auto)` |
| 타입 소거/추론으로 변수 선언 | `var x = expr;` | `auto x = expr;` |
| 참조/const 보존 반환 | (주로 ref/out로 모델링) | `decltype(auto)` + `std::forward` |

---

## 6) 주의할 함정들

### C#
- `obj.GetType()`은 `obj == null`이면 예외. `typeof`는 항상 안전.
- `typeof(T)`는 **정적 T**. 객체의 구체 하위 타입을 반영하지 않습니다.
- 박싱된 값형식은 `GetType()`으로 정확한 런타임 타입을 얻습니다. (`int` → `System.Int32`)

### C++
- `decltype((x))`와 `decltype(x)`는 **다릅니다**. 괄호 하나로 참조가 생길 수 있음.
- `auto`는 참조/const를 **소거**합니다. 보존하려면 `decltype(auto)` 또는 `std::add_lvalue_reference_t` 등을 사용.
- `typeid`는 구현마다 `name()`이 난독화되어 보일 수 있습니다(디마글링 필요).

---

## 7) 예시로 보는 매핑

### C# 예시를 C++로 비슷하게 생각해 보기
```csharp
var items = new[] { 1, 2.0, 3, 4, 5, 5 }; // double[] 로 승격
Console.WriteLine(items.GetType());       // System.Double[]
```
```cpp
#include <vector>
int main() {
    // 정수와 실수를 함께 초기화 → 더 넓은 형식 double 로 변환
    std::vector<double> items{1, 2.0, 3, 4, 5, 5};
    using T = decltype(items)::value_type; // T == double
}
```

---

## 8) 결론

- **C#**: `typeof`(정적), `GetType()`(동적)로 **타입 메타데이터**를 다룹니다. 이벤트/리플렉션 친화적.
- **C++**: `decltype`(정적 복제), `typeid`(동적/정적 RTTI)로 **추론/반사**를 각각 처리합니다. 템플릿, 완전 전달, 식 범주 보존에 핵심.

> 기억법: **“C#은 메타데이터, C++은 식의 본질”**  
> 런타임 타입이 필요하면 `GetType()`/`typeid`, 식의 정확한 타입이 필요하면 `decltype`/`decltype(auto)`.
