# 🧠 튜플(Tuple)의 핵심 개념
튜플은 여러 개의 서로 다른 타입의 값을 하나의 묶음으로 표현하는 구조입니다.
클래스나 구조체를 만들지 않고도 간단하게 데이터를 그룹화할 수 있어요.


## 🔍 언어별 튜플 비교
| 언어     | 튜플 지원 여부 | 특징 및 설명 |
|----------|----------------|--------------|
| **C#**   | ✅ 지원 (C# 7.0+) | `ValueTuple` 구조체 기반, 명명된 요소 가능, 구조 분해 지원 |
| **C++**  | ✅ 지원 (C++11+) | `std::tuple`, `std::get`, C++17부터 구조 분해 할당 가능 |
| **Java** | ❌ 직접 지원 없음 | `Pair`, `Record`, 외부 라이브러리(Vavr, javatuples 등)로 대체 |
| **Python** | ✅ 완전 지원 | `(a, b, c)` 형태의 불변 구조, 언팩 및 슬라이싱 자유로움 |
| **Swift** | ✅ 완전 지원 | `(Int, String)` 형태로 명시적 타입 지정 가능, 구조 분해 지원 |
| **Kotlin** | ✅ 지원 | `Pair`, `Triple` 기본 제공, `data class`로 확장 가능 |



## ✅ C# 튜플 사용 예시
``` csharp
(double, int) t1 = (4.5, 3);
Console.WriteLine(t1.Item1); // 4.5

(double Sum, int Count) t2 = (4.5, 3);
Console.WriteLine(t2.Sum); // 4.5
```

- Item1, Item2로 접근하거나
- 이름을 붙여서 Sum, Count처럼 의미 있는 코드 작성 가능
- FindMinMax()처럼 함수에서 여러 값을 반환할 때 매우 유용

## ✅ C++ 튜플 사용 예시
```cpp
auto [x, y, z] = std::make_tuple(1, 2.3, "456");
std::cout << x << ", " << y << ", " << z << std::endl;
```

- std::tuple은 타입 안정성이 뛰어나고
- std::get<index>()로 접근하거나
- C++17부터는 구조 분해 할당도 지원

## ⚠️ Java는 튜플이 없다?
Java는 튜플을 언어 차원에서 직접 지원하지 않습니다.
하지만 대체 방법은 있어요:

### 1. Pair 클래스 (예: Apache Commons, JavaFX)
Pair<Integer, String> pair = new Pair<>(1, "Hello");


### 2. Record (Java 14+)
record MinMax(int min, int max) {}
MinMax result = new MinMax(1, 9);


### 3. 외부 라이브러리
- Vavr, javatuples 등에서 다양한 튜플 타입 제공

## ✨ 요약
- C#과 C++은 튜플을 언어 차원에서 강력하게 지원하며,
→ 함수 반환, 구조 분해, 명명된 요소 등 다양한 방식으로 활용 가능
- Java는 직접적인 튜플은 없지만,
→ Record, Pair, 외부 라이브러리로 비슷한 기능을 구현할 수 있음

---

# 📦 Java record란?
record는 불변(immutable) 데이터 객체를 정의하기 위한 간결한 문법입니다.
자동으로 생성되는 생성자, equals(), hashCode(), toString() 덕분에
보일러플레이트 코드 없이 데이터를 표현할 수 있어요.


## ✅ 기본 문법
```java
public record MinMax(int min, int max) {}
```

이렇게 정의하면 다음이 자동으로 생성됩니다:

- private final 필드
- 생성자 MinMax(int min, int max)
- min() / max() getter
- equals() / hashCode() / toString()

## 🧪 사용 예시
```java
public class Main {
    public static void main(String[] args) {
        MinMax result = new MinMax(-2, 9);
        System.out.println("최솟값: " + result.min());
        System.out.println("최댓값: " + result.max());
    }
}

record MinMax(int min, int max) {}
```

## 출력:
```
최솟값: -2  
최댓값: 9
```

```java
record Response(int code, String message) {}

Response res = new Response(200, "OK");
System.out.println(res.message()); // OK
```

이걸 위해 클래스를 만들고 getMessage() 같은 메서드를 정의할 필요가 없어요.
그냥 선언하고 바로 쓰면 되니까, 코드가 더 읽기 쉬워지고 유지보수도 쉬워집니다.
요즘 언어들이 점점 더 개발자의 의도를 빠르게 표현할 수 있는 방향으로 진화하고 있음


## 🧠 왜 record가 튜플 대체로 쓰일 수 있을까?
튜플처럼 여러 값을 묶어서 반환하고 싶을 때,
Java에서는 record를 사용하면 명확한 타입 이름과 필드 이름을 줄 수 있어
코드의 가독성과 유지보수성이 훨씬 좋아집니다.
예를 들어:

```java
record Point(int x, int y) {}

Point p = new Point(3, 5);
System.out.println(p.x()); // 3
```

튜플처럼 (3, 5)를 표현하면서도
x, y라는 이름 덕분에 의미가 분명해지죠.

## ⚠️ 주의할 점
- record는 불변 객체입니다. 필드를 변경할 수 없어요.
- 상속은 불가능합니다. record는 final이며 다른 클래스를 상속받을 수 없어요.
- 하지만 인터페이스는 구현할 수 있습니다.

## ✨ 요약
| 항목         | 설명 |
|--------------|------|
| 도입 버전     | Java 14 (Preview), Java 16부터 정식 도입 |
| 목적         | 불변 데이터 객체를 간결하게 정의 |
| 자동 생성     | 생성자, `equals()`, `hashCode()`, `toString()` |
| 필드 특성     | `private final`로 불변, Getter는 필드명 그대로 사용 (`x()`, `y()` 등) |
| 상속 여부     | 클래스 상속 불가 (`final`), 인터페이스 구현 가능 |
| 사용 예시     | `record Point(int x, int y) {}` → `Point p = new Point(1, 2);` |
| 튜플 대체 가능 | 명명된 필드로 의미 전달이 명확, 튜플보다 가독성 우수 |

---

