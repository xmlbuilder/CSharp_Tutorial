# C# Pattern Matching
C#의 **Pattern Matching(패턴 매칭)**은 객체나 값이 특정 조건이나 형태에 맞는지를 간결하게 검사하고 처리할 수 있게 해주는 기능입니다. 
복잡한 if나 switch 문을 더 읽기 쉽고 안전하게 바꿔주는 도구


## C#
```csharp
/*
    switch 문 및 switch 식을 사용하여 is 입력 식을 다양한 특성과 일치시킬 수 있습니다. 
*/

using System;
namespace  GrammarTest
{
    public class Program
    {
        class Geometry
        {
            
        }
        class Triangle : Geometry
        {
            public int Width { get; set; }
            public int Height { get; set; }
            public int Base { get; set; }
        }
        class Rectangle : Geometry
        {
            public int Width { get; set; }
            public int Height { get; set; }
        }
        class Square : Geometry
        {
            public int Width { get; set; }
        }

        public static void PatternMatching()
        {
            Geometry g = new Square() {Width = 5};
            switch (g)
            {
                case Triangle t:
                    Console.WriteLine("Triangle");
                    break;
                case Rectangle sq when sq.Width == sq.Height:
                    Console.WriteLine("Rectangle 1");
                    break;
                case Rectangle t:
                    Console.WriteLine("Rectangle 2");
                    break;
                case Square s:
                    Console.WriteLine("Square");
                    break;
                default:
                    Console.WriteLine("other");
                    break;
            }
        }
        public static void Main(String[] args)
        {
            PatternMatching();
            //Square
        }
    }
}

```


## 🧵 C++ (RTTI + dynamic_cast)
C++에는 C#처럼 switch에서 타입 패턴 매칭이 직접 지원되진 않지만, **dynamic_cast**와 **if/else if**로 비슷하게 구현할 수 있습니다.
```cpp
#include <iostream>
#include <vector>
#include <typeinfo>
using namespace std;

struct Geometry { virtual ~Geometry() = default; };
struct Triangle : Geometry { int width, height, base; };
struct Rectangle : Geometry { int width, height; };
struct Square : Geometry { int width; };

void PatternMatching(Geometry* g) {
    if (auto t = dynamic_cast<Triangle*>(g)) {
        cout << "Triangle\n";
    }
    else if (auto r = dynamic_cast<Rectangle*>(g)) {
        if (r->width == r->height)
            cout << "Rectangle 1\n";
        else
            cout << "Rectangle 2\n";
    }
    else if (auto s = dynamic_cast<Square*>(g)) {
        cout << "Square\n";
    }
    else {
        cout << "other\n";
    }
}

int main() {
    Geometry* g = new Square{5};
    PatternMatching(g); // Square
    delete g;
}
```


## 💡 dynamic_cast는 RTTI(런타임 타입 정보)를 사용해 안전하게 다운캐스팅합니다.

🧵 Java (Pattern Matching for instanceof + switch)
Java 21부터는 switch 패턴 매칭이 정식 기능이 되었고, 이전 버전에서는 instanceof + 캐스팅으로 구현합니다.
```java
sealed interface Geometry permits Triangle, Rectangle, Square {}
record Triangle(int width, int height, int base) implements Geometry {}
record Rectangle(int width, int height) implements Geometry {}
record Square(int width) implements Geometry {}

public class Main {
    static void patternMatching(Geometry g) {
        switch (g) {
            case Triangle t -> System.out.println("Triangle");
            case Rectangle r && (r.width() == r.height()) -> System.out.println("Rectangle 1");
            case Rectangle r -> System.out.println("Rectangle 2");
            case Square s -> System.out.println("Square");
            default -> System.out.println("other");
        }
    }

    public static void main(String[] args) {
        Geometry g = new Square(5);
        patternMatching(g); // Square
    }
}
```
💡 Java 21의 case Rectangle r && (조건)은 C#의 when 절과 유사합니다.

## 🧵 Python (Structural Pattern Matching, PEP 634)
Python 3.10+에서는 match/case 구문으로 구조적 패턴 매칭이 가능합니다.
```python
class Geometry: pass
class Triangle(Geometry):
    def __init__(self, width, height, base):
        self.width = width
        self.height = height
        self.base = base

class Rectangle(Geometry):
    def __init__(self, width, height):
        self.width = width
        self.height = height

class Square(Geometry):
    def __init__(self, width):
        self.width = width

def pattern_matching(g):
    match g:
        case Triangle():
            print("Triangle")
        case Rectangle(width=w, height=h) if w == h:
            print("Rectangle 1")
        case Rectangle():
            print("Rectangle 2")
        case Square():
            print("Square")
        case _:
            print("other")

g = Square(5)
pattern_matching(g)  # Square
```


💡 case Rectangle(width=w, height=h) if w == h:는 C#의 when 절과 동일한 역할을 합니다.

## 📊 비교 요약

| 언어     | 패턴 매칭 방식                          | 조건부 매칭 지원 | 최신 문법 버전 |
|----------|-----------------------------------------|------------------|----------------|
| **C#**   | `switch` + `case Type var` + `when`     | ✅               | C# 7+          |
| **C++**  | `dynamic_cast` + `if/else`              | ✅               | 전통 방식      |
| **Java** | `switch` + `case Type var && 조건`      | ✅               | Java 21        |
| **Python**| `match` + `case Type(...) if 조건`     | ✅               | Python 3.10+   |


## 📊 C#, C++, Java, Python 패턴 매칭 문법 비교

| 언어     | 문법 형태 예시                                           | 조건부 매칭 지원 | 장점                                                                 | 단점                                                                 |
|----------|----------------------------------------------------------|------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| **C#**   | `switch` + `case Type var` + `when`                       | ✅               | - 타입 검사와 조건부 분기를 `switch`에서 직관적으로 처리 가능<br>- 패턴 매칭과 변수 바인딩 동시 지원 | - `switch` 문법에 종속적<br>- 복잡한 패턴 조합 시 가독성 저하 가능   |
| **C++**  | `dynamic_cast` + `if/else`                                | ✅               | - RTTI 기반으로 안전한 다운캐스팅 가능<br>- 모든 타입에 적용 가능    | - 문법이 장황하고 `switch`와 결합 불가<br>- 패턴 매칭 전용 문법 부재 |
| **Java** | `switch` + `case Type var && 조건` *(Java 21+)*           | ✅               | - `switch`에서 타입 검사와 조건부 분기 통합<br>- `instanceof`보다 간결 | - 최신 버전에서만 지원<br>- 구버전에서는 `instanceof` + 캐스팅 필요 |
| **Python**| `match` + `case Type(...) if 조건` *(Python 3.10+)*      | ✅               | - 구조적 패턴 매칭 지원<br>- 클래스 속성 기반 매칭 가능<br>- 가독성 우수 | - Python 3.10 이상에서만 사용 가능<br>- 동적 타입 특성상 런타임 오류 가능 |



## 💡 정리 포인트
- C# → switch 패턴 매칭이 강력하고 직관적
- C++ → 전용 문법은 없지만 dynamic_cast로 유연하게 구현 가능
- Java → 최신 버전에서 switch 패턴 매칭 지원, 구버전은 instanceof 사용
- Python → match/case로 구조적 패턴 매칭 가능, 데이터 클래스와 궁합 좋음

---

# Java record
## 📦 record란?
record는 불변(immutable) 데이터 객체를 간단하게 정의할 수 있는 문법입니다.
기존의 Java 클래스에서 getter, equals, hashCode, toString 등을 직접 작성하던 번거로움을 없애줍니다.
### ✅ 특징
- 모든 필드는 final이며, 생성자 자동 생성
- equals, hashCode, toString 자동 구현
- 불변 객체로 설계되어 데이터 안정성 높음
## 🎯 예시
```java
record Person(String name, int age) {}
```

위 코드는 아래와 같은 클래스를 자동으로 생성한 것과 같아요:
```java
final class Person {
    private final String name;
    private final int age;

    // 생성자, getter, equals, hashCode, toString 자동 생성됨
}
```


## 🧩 sealed interface란?
sealed interface는 상속을 제한하는 기능입니다.
어떤 클래스나 인터페이스가 누구에게만 구현될 수 있는지 명시할 수 있어요.

### ✅ 예시
```java
sealed interface Geometry permits Triangle, Rectangle, Square {}
```

이렇게 하면 Geometry 인터페이스는 오직 Triangle, Rectangle, Square만 구현할 수 있어요.
다른 클래스가 Geometry를 구현하려고 하면 컴파일 에러가 납니다.

## 🧠 함께 쓰면 좋은 이유
sealed와 record를 함께 쓰면 패턴 매칭이나 switch 문에서 타입 분기 처리가 아주 깔끔해져요:
```java
void printArea(Geometry g) {
    switch (g) {
        case Triangle t -> System.out.println("Triangle area: " + (t.base() * t.height() / 2));
        case Rectangle r -> System.out.println("Rectangle area: " + (r.width() * r.height()));
        case Square s -> System.out.println("Square area: " + (s.width() * s.width()));
    }
}
```

## 🧪 요약
| 문법               | 역할                                | 장점                                | 예시 코드 또는 사용처                          |
|--------------------|-------------------------------------|-------------------------------------|------------------------------------------------|
| `record`           | 불변 데이터 객체 정의                | 코드 간결, 자동 메서드 생성         | `record Person(String name, int age) {}`       |
| `sealed`           | 상속/구현을 특정 클래스에만 허용     | 타입 안전성, 유지보수 용이           | `sealed interface Shape permits Circle, Square {}` |
| `switch + record`  | 타입 기반 분기 처리                  | 가독성 향상, 패턴 매칭 최적화        | `switch(shape) { case Circle c -> ... }`       |

---



