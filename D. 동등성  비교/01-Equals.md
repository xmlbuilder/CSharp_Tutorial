# 객체 동등성 비교 (C# Equals vs C++ operator==)

## 1. C#의 Equals / GetHashCode 개념

-   **Equals(object)**\
    두 객체가 "값 기준으로 같은지" 비교할 때 오버라이드.
    (기본 Object.Equals는 참조 비교 → 동일 인스턴스 여부)

-   **GetHashCode()**\
    Equals가 true이면 반드시 같은 해시코드를 반환해야 함.
    → `Dictionary`, `HashSet`, `Distinct()` 등 해시 기반 컬렉션에서
    필수.

-   **연산자 오버로드 (`==`, `!=`)**
    Equals와 일관성 있게 구현해야 함.

-   **IEqualityComparer`<T>`{=html}**
    외부에서 비교 기준을 따로 정의하고 싶을 때 사용.
    (`Distinct(new PersonComparator())`)

------------------------------------------------------------------------

### C# 예제

``` csharp
public class Person {
    public string Name { get; set; }
    public int Age { get; set; }
    public string Clothes { get; set; }

    public override bool Equals(object obj) {
        Person other = obj as Person;
        if (other == null) return false;
        return (Name == other.Name && Age == other.Age);
    }

    public override int GetHashCode() {
        return Name.GetHashCode() * Age.GetHashCode();
    }

    public static bool operator ==(Person lhs, Person rhs) {
        if (lhs is null) return rhs is null;
        return lhs.Equals(rhs);
    }
    public static bool operator !=(Person lhs, Person rhs) => !(lhs == rhs);

    public override string ToString() => $"Name:{Name}, Age:{Age}, Clothes:{Clothes}";
}
```

사용 예:

``` csharp
var persons = new List<Person>{
    new Person{Name="Jon",Age=20,Clothes="shirt"},
    new Person{Name="Jon",Age=20,Clothes="other"}
};

Console.WriteLine(persons[0] == persons[1]); // True (Name,Age 기준 동등성)
var distinctPersons = persons.Distinct().ToList(); // 중복 제거됨
```

------------------------------------------------------------------------

## 2. C++에서의 동등성 비교

-   C++은 `Object.Equals` 같은 표준 메서드는 없음.
-   대신 `operator==` / `operator!=` 연산자를 **직접 재정의**하여 동등성
    기준 정의.
-   해시 기반 컬렉션(`std::unordered_set`, `std::unordered_map`)을 쓸
    때는
    `std::hash<T>`를 특수화하거나 커스텀 해시 functor 제공.

------------------------------------------------------------------------

### C++ 예제

``` cpp
#include <iostream>
#include <unordered_set>

class TVector2d {
private:
    double x{0}, y{0};
public:
    TVector2d(double x, double y) : x{x}, y{y}{}

    friend bool operator==(const TVector2d& lhs, const TVector2d& rhs) {
        return (lhs.x == rhs.x && lhs.y == rhs.y);
    }
    friend bool operator!=(const TVector2d& lhs, const TVector2d& rhs) {
        return !(lhs == rhs);
    }
};

// 해시 특수화
namespace std {
    template<>
    struct hash<TVector2d> {
        size_t operator()(const TVector2d& v) const noexcept {
            return std::hash<double>()(v.x) ^ (std::hash<double>()(v.y) << 1);
        }
    };
}

int main() {
    TVector2d vec1(1.0, 2.0);
    TVector2d vec2(1, 2);

    std::cout << (vec1 == vec2) << std::endl; // true

    std::unordered_set<TVector2d> set;
    set.insert(vec1);
    set.insert(vec2); // 같은 값이므로 중복 삽입되지 않음

    std::cout << set.size() << std::endl; // 1
    return 0;
}
```

------------------------------------------------------------------------

## 3. C# vs C++ 비교표

| 개념            | C#                           | C++                        |
|-----------------|------------------------------|----------------------------|
| 값 비교         | `Equals` 오버라이드          | `operator==` 오버로드      |
| 참조 비교       | `ReferenceEquals` (기본 Object) | 기본적으로 포인터 비교    |
| 해시 코드       | `GetHashCode` 오버라이드 필요 | `std::hash<T>` 특수화 필요 |
| 컬렉션 동작     | `Distinct`, `HashSet`, `Dict` | `std::unordered_set/map`   |
| 연산자 오버로드 | `==`, `!=` 연산자 구현 권장  | `==`, `!=` 연산자 직접 구현 |


## 4. 요약

-   C#은 **Equals + GetHashCode + (==, !=)** 세트를 반드시 일관되게
    구현해야 한다.
-   C++은 **operator==, operator!=, std::hash 특수화**를 조합해 동일한
    동작을 얻는다.
-   두 언어 모두 "값 기반 동등성"을 정의할 수 있고, 해시 기반
    컬렉션에서도 올바르게 동작하도록 준비해야 한다.
