# SequenceEqual
🧠 기능 요약
두 배열이 길이와 순서까지 동일한지 비교하는 코드입니다.

## ✅ C# 버전
```csharp
namespace GrammarTest
{
    class Program
    {
        public static void Main(string[] args)
        {
            int[] a = new[] {1, 2, 3};
            int[] b = new[] {1, 2, 3};
            int[] c = new[] {1, 3, 2};

            bool returnTrue = a.SequenceEqual(b);
            bool returnFalse = a.SequenceEqual(c);
            
            Console.WriteLine(returnTrue);
            //True

            Console.WriteLine(returnFalse);
            //False
        }
    }
}

```

## ✅ C++ 버전
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> a = {1, 2, 3};
    std::vector<int> b = {1, 2, 3};
    std::vector<int> c = {1, 3, 2};

    bool returnTrue = (a == b);
    bool returnFalse = (a == c);

    std::cout << std::boolalpha << returnTrue << std::endl;  // true
    std::cout << std::boolalpha << returnFalse << std::endl; // false

    return 0;
}
```

C++에서는 std::vector끼리 == 연산자로 순서 포함 비교가 가능합니다.


## ✅ Java 버전
```java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        int[] a = {1, 2, 3};
        int[] b = {1, 2, 3};
        int[] c = {1, 3, 2};

        boolean returnTrue = Arrays.equals(a, b);
        boolean returnFalse = Arrays.equals(a, c);

        System.out.println(returnTrue);  // true
        System.out.println(returnFalse); // false
    }
}
```

Java에서는 Arrays.equals()가 배열의 순서까지 비교해줍니다.


## ✅ Python 버전
```python
a = [1, 2, 3]
b = [1, 2, 3]
c = [1, 3, 2]

return_true = a == b
return_false = a == c

print(return_true)   # True
print(return_false)  # False

```


Python에서는 리스트끼리 == 연산으로 순서 포함 비교가 가능합니다.

---


# 🔍 객체 비교 연산 정리

| 언어       | 연산자 (`==`)       | 메서드 (`Equals`, `equals`, `__eq__`) | 연산자 오버로드 (`operator==`) | 예시 코드                         |
|------------|---------------------|----------------------------------------|-------------------------------|-----------------------------------|
| C#         | `==`                | `.Equals()`                            | `operator==`                  | `obj1 == obj2`, `obj1.Equals(obj2)` |
| C++        | `==`                | 없음                                   | `operator==`                  | `obj1 == obj2`                    |
| Java       | `==`                | `.equals()`                            | 없음                          | `obj1 == obj2`, `obj1.equals(obj2)` |
| Python     | `==`                | `__eq__()`                             | `__eq__()`                    | `obj1 == obj2`                    |



## 🧠 핵심 개념 요약
### ✅ C#
- ==는 참조 비교 (기본적으로 같은 인스턴스인지 확인)
- .Equals()도 참조 비교지만, 오버라이딩하면 값 비교 가능
- IEquatable<T> 인터페이스 구현 시 .Equals() 커스터마이징 권장
```csharp
public class Person {
    public string Name;
    public override bool Equals(object obj) {
        return obj is Person p && p.Name == this.Name;
    }
}
```


### ✅ C++
- == 연산자는 클래스에서 직접 오버로딩해야 동작
- 기본적으로는 사용자 정의 객체끼리 비교 불가
```cpp
class Person {
public:
    std::string name;
    bool operator==(const Person& other) const {
        return name == other.name;
    }
};
```


### ✅ Java
- ==는 참조 비교 (주소 비교)
- .equals()는 오버라이딩 전에는 ==와 동일
- Object.equals()를 오버라이딩하여 값 비교 구현
```java
public class Person {
    String name;
    @Override
    public boolean equals(Object obj) {
        if (obj instanceof Person) {
            return name.equals(((Person)obj).name);
        }
        return false;
    }
}
```


### ✅ Python
- ==는 내부적으로 __eq__() 호출
- __eq__()를 오버라이딩하면 값 비교 가능
```python
class Person:
    def __init__(self, name):
        self.name = name
    def __eq__(self, other):
        return isinstance(other, Person) and self.name == other.name
```



