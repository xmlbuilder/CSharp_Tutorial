## 📘 Overloading equality operators

### 📌 핵심 비교 기준

| 언어       | 기본 동등성 비교                          | 사용자 정의 비교 방식         | 연산자 오버로드 지원         | 박싱 회피 / 성능 최적화 |
|------------|-------------------------------------------|-------------------------------|-------------------------------|--------------------------|
| **C#**     | `object.Equals`, `GetHashCode`            | `IEquatable<T>.Equals`        | `==`, `!=` 오버로드 가능      | `IEquatable<T>`로 박싱 회피 |
| **C++**    | `operator==`, `std::hash`                 | 직접 연산자 및 해시 오버로드 | `==`, `!=` 오버로드 가능      | 해당 없음               |
| **Java**   | `Object.equals`, `hashCode`               | `equals()` 오버라이드         | `==`은 참조 비교만 가능       | 해당 없음               |
| **Python** | `__eq__`, `__hash__`                      | `__eq__`, `__hash__` 오버라이드 | `==`, `!=`은 `__eq__`에 연결 | 해당 없음               |

### 📌 각 언어별 설명

#### 🔹 C#
```csharp
/*
1. object.Equals and object.GetHashCode
2. IEquatable<T>.Equals (optional, allows avoiding boxing)
3. operator == and operator != (optional, allows using operators)
*/

class Student : IEquatable<Student>
{
    public string Name { get; set; } = "";
    
    public bool Equals(Student other)
    {
        if (ReferenceEquals(other, null)) return false;
        if (ReferenceEquals(other, this)) return true;
        return string.Equals(Name, other.Name);
    }
    public override bool Equals(object obj)
    {
        if (ReferenceEquals(null, obj)) return false;
        if (ReferenceEquals(this, obj)) return true;
        return Equals(obj as Student);
    }
    
    public override int GetHashCode()
    {
        return Name?.GetHashCode() ?? 0;
    }
    
    public static bool operator ==(Student left, Student right)
    {
        return Equals(left, right);
    }
    
    public static bool operator !=(Student left, Student right)
    {
        return !Equals(left, right);
    }
}

class Program
{
    static int Main(string[] args)
    {
        Student student1 = new Student();
        Student student2 = new Student();
        Student student3 = new Student();
        student1.Name = "jhjeong";
        student2.Name = "jhjeong";
        student3.Name = "jhjeong2";
        Console.WriteLine($"student1 == student2 {student1 == student2}"); //True
        Console.WriteLine($"student1 == student3 {student1 == student3}"); //False
        Console.WriteLine($"student1.Equals(stduent2)  {student1.Equals(student2)}"); //True
        
        return 0;
    }
}
```

- object.Equals(object obj)와 object.GetHashCode()는 모든 객체가 기본적으로 갖는 메서드입니다.
- IEquatable<T>.Equals(T other)는 제네릭 타입 비교를 위한 인터페이스로, 박싱 없이 타입 안전한 비교가 가능합니다.
- ==, != 연산자 오버로드를 통해 직관적인 비교 문법을 제공합니다.
- Dictionary, HashSet 같은 컬렉션에서 성능 향상에 유리합니다.

#### 🔹 C++
- operator==와 operator!=를 직접 오버로드하여 동등성 비교를 정의합니다.
- std::hash를 오버로드하면 unordered_map, unordered_set에서 사용할 수 있습니다.
- 인터페이스 기반 비교는 없지만, 템플릿과 연산자 오버로드로 유연하게 구현 가능합니다.
```cpp
struct Student {
    std::string name;
    bool operator==(const Student& other) const {
        return name == other.name;
    }
};
namespace std {
    template<>
    struct hash<Student> {
        size_t operator()(const Student& s) const {
            return hash<std::string>()(s.name);
        }
    };
}
```

#### 🔹 Java
- Object.equals(Object obj)와 hashCode()를 오버라이드하여 동등성 비교를 정의합니다.
- `==`는 참조 비교이므로, 값 비교에는 `equals()`를 반드시 사용해야 합니다.
- Comparable<T>는 정렬을 위한 인터페이스이며, IEquatable<T>에 해당하는 인터페이스는 없습니다.
```java
public class Student {
    private String name;
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Student)) return false;
        Student other = (Student) obj;
        return name.equals(other.name);
    }
    @Override
    public int hashCode() {
        return name.hashCode();
    }
}
```

#### 🔹 Python
- __eq__ 메서드를 오버라이드하여 동등성 비교를 정의합니다.
- __hash__를 함께 정의하면 set, dict에서 사용할 수 있습니다.
- ==, != 연산자는 __eq__, __ne__에 연결되어 자동 호출됩니다.
```python
class Student:
    def __init__(self, name):
        self.name = name
    def __eq__(self, other):
        if not isinstance(other, Student):
            return False
        return self.name == other.name
    def __hash__(self):
        return hash(self.name)
```


### 📌 요약
- C#은 IEquatable<T>와 연산자 오버로드를 통해 타입 안전성과 성능을 모두 챙길 수 있는 구조입니다.
- C++은 연산자 오버로드와 템플릿을 통해 유연하게 구현하지만 명시적입니다.
- Java는 equals()와 hashCode()를 오버라이드해야 하며, 연산자 오버로드는 불가능합니다.
- Python은 매직 메서드(__eq__, __hash__)를 통해 간결하게 동등성 비교를 구현합니다.

---
