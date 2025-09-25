# C# `?.` / `?[]` (Null-conditional) 개념과 Java/C++ 대응 방법

## 1) C#의 `?.`, `?[]` 요약

-   **정의**: 피연산자가 `null`이 아닐 때만 멤버 접근(`?.`) 또는 인덱서
    접근(`?[]`)을 수행하고, 그렇지 않으면 **즉시 `null` 반환**.
-   **단락 평가**: 체인 중 하나라도 `null`이면 그 이후는 **평가하지
    않음**.

``` csharp
// 멤버/인덱서 접근
var len   = str?.Length;      // str == null → len == null
var first = arr?[0];          // arr == null → first == null

// 체이닝
A?.B?.Do(C);                  // A==null이면 B 평가 안 함, A나 B가 null이면 Do 호출 안 함

// null 병합(??)과 조합
var name = GetProduct()?.Name ?? DefaultName;
```

------------------------------------------------------------------------

## 2) Java에서의 대응

Java에는 `?.` / `?[]` **연산자 자체가 없으므로** 다음 패턴을 사용합니다.

### (A) 명시적 null 체크 (가장 단순/명확)

``` java
String s = getMessage();
Integer len = (s != null) ? s.length() : null;

List<Integer> list = getList();
Integer first = (list != null && !list.isEmpty()) ? list.get(0) : null;
```

### (B) `Optional` 체이닝 (Java 8+)

``` java
String name = Optional.ofNullable(getProduct())
                      .map(Product::getName)
                      .orElse(DEFAULT_NAME);

// 인덱스 접근에도 응용
Integer first = Optional.ofNullable(getList())
                        .filter(l -> !l.isEmpty())
                        .map(l -> l.get(0))
                        .orElse(null);
```

-   `Optional`은 **체이닝/단락**을 표현하기 좋음.
-   단, 모든 도메인 모델에 `Optional`을 남용하는 것은 권장되지 않음(필드
    타입으로는 지양).

### (C) 안전한 이벤트 호출/콜백

``` java
Consumer<String> handler = ...;
if (handler != null) handler.accept("msg");
```

> C#의 `evt?.Invoke(...)`와 같은 축약 문법은 없고, if-check로 대체.

------------------------------------------------------------------------

## 3) C++에서의 대응

C++에도 `?.` / `?[]` **연산자 자체는 없음**. 대신 다음을 씁니다.

### (A) 포인터/스마트 포인터 null 체크

``` cpp
std::unique_ptr<Product> p = getProduct();
const char* name = (p ? p->name() : nullptr);
```

### (B) `std::optional` 체이닝 (C++17+)

``` cpp
std::optional<Product> getProduct();

std::string name =
    getProduct()
      .transform([](const Product& p){ return p.name(); })  // C++23
      .value_or(DEFAULT_NAME);

// C++17 호환 방식
std::string name =
    getProduct() ? getProduct()->name() : DEFAULT_NAME;
```

### (C) 인덱스 접근

``` cpp
std::vector<int>* v = getVectorPtr();
std::optional<int> first =
    (v && !v->empty()) ? std::optional<int>((*v)[0]) : std::nullopt;
```

### (D) 안전한 콜백/함수자 호출

``` cpp
std::function<void(const std::string&)> cb;
if (cb) cb("msg");
```

------------------------------------------------------------------------

## 4) 세 언어 비교 표

| 개념          | C#                       | Java                                                            | C++                                                                 |
|---------------|--------------------------|-----------------------------------------------------------------|---------------------------------------------------------------------|
| 멤버 접근     | `a?.x`                   | `(a != null) ? a.x : null`                                      | `if (ptr) ptr->x;` / `opt ? opt->x : ...`                          |
| 인덱서/배열 접근 | `a?[i]`                  | `(a != null && i<a.length) ? a[i] : null`                       | `if (vec && i<vec->size()) (*vec)[i];`                             |
| 체이닝 & 단락 | `A?.B?.Do(C)`            | `Optional.ofNullable(A).map(A::getB).ifPresent(b -> b.do(C))`   | `ptr && ptr->b ? ptr->b->do(C) : (void)0` / `optional` 조합        |
| null일 때 기본값 | `a?.x ?? defaultValue`   | `Optional.ofNullable(a).map(x -> x.f()).orElse(defaultValue)`   | `opt.transform(...).value_or(defaultValue)` (C++23) / `opt ? ... : defaultValue` |
| 이벤트/콜백 호출 | `evt?.Invoke(args)`      | `if (handler != null) handler.accept(args);`                    | `if (cb) cb(args);`                                                 |


------------------------------------------------------------------------

## 5) 실전 팁

-   **C#**: `?.`/`?[]`와 `??` 조합으로 **가독성**과 **안전성**을 동시에
    확보.
-   **Java**: 간단한 곳은 **명시적 null 체크**가 가장 읽기 쉬움. 여러
    단계 체인은 `Optional`이 깔끔함.
-   **C++**: 포인터/`std::optional`을 상황에 맞게 사용. 체이닝은 `if`
    체크가 직관적이며, C++23의 `optional::transform`/`and_then`로 더
    함수형 스타일도 가능.

------------------------------------------------------------------------

## 6) C# 예제와 동등한 Java/C++ 예시

### C

``` csharp
var name = GetProduct()?.Name ?? DefaultName;
var first = customers?[0];
```

### Java

``` java
String name = Optional.ofNullable(getProduct())
                      .map(Product::getName)
                      .orElse(DEFAULT_NAME);

Customer first = (customers != null && !customers.isEmpty()) ? customers.get(0) : null;
```

### C++

``` cpp
std::string name =
    getProduct().has_value() ? getProduct()->name() : DEFAULT_NAME;

Customer* first =
    (customers && !customers->empty()) ? &(*customers)[0] : nullptr;
```

------------------------------------------------------------------------

### 결론

-   C#의 `?.`/`?[]`는 **언어 차원의 null-safe 접근 연산자**.
-   Java/C++는 **연산자 대체는 없고**, `Optional`/`std::optional` 또는
    **명시적 null 체크**로 동일한 의도를 구현합니다.
