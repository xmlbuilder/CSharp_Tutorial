# CompareTo 
C#의 CompareTo는 객체 간의 정렬 기준을 정의할 때 사용하는 대표적인 메서드입니다.
이 기능은 C++, Java, Python에서도 각각의 방식으로 존재하며, 정렬이나 비교 연산에서 핵심 역할을 합니다.

## 🔍 C#의 CompareTo 개념
- IComparable<T> 인터페이스를 구현하면 CompareTo 메서드를 통해 객체 간 비교 로직을 정의할 수 있습니다.
- 반환값 의미:
- 음수: 현재 객체가 비교 대상보다 작음
- 0: 같음
- 양수: 현재 객체가 비교 대상보다 큼
```csharp
public int CompareTo(Item? other)
{
    return this.idNumber - other!.idNumber;
}
```

## 소스
```csharp
namespace GrammarTest
{
    public class Item : IComparable<Item>
    {
        public string name;
        public int idNumber;
        public decimal price;
        public int CompareTo(Item? other)
        {
            return (this.idNumber - other!.idNumber);
        }
    }
    
    class Program
    {
        public static void Main(String[] args)
        {
            Item apple = new Item();
            apple.idNumber = 15;
            Item banana = new Item();
            banana.idNumber = 4;
            Item cow = new Item();
            cow.idNumber = 15;
            Item diamond = new Item();
            diamond.idNumber = 18;
            
            //IComparable
            Console.WriteLine(apple.CompareTo(banana));
            //11
            
            
        }
    }
}

```


- 위 예제에서는 idNumber를 기준으로 비교하며, List<Item>을 Sort()하면 자동으로 이 기준으로 정렬됩니다.

## 🧊 C++에서의 비교 연산자 오버로딩
- C++에서는 operator<, operator== 등을 오버로딩하여 비교 기준을 정의합니다.
- C++20부터는 operator<=> (three-way comparison)도 지원합니다.
```cpp
class Item {
public:
    int idNumber;
    bool operator<(const Item& other) const {
        return idNumber < other.idNumber;
    }
};
```

- std::sort() 같은 알고리즘에서 자동으로 사용됨.

## ☕ Java의 Comparable 인터페이스
- Java에서는 Comparable<T> 인터페이스를 구현하고 compareTo() 메서드를 정의합니다.
```java
public class Item implements Comparable<Item> {
    public int idNumber;

    @Override
    public int compareTo(Item other) {
        return this.idNumber - other.idNumber;
    }
}
```

- Collections.sort() 또는 List.sort()에서 자동으로 사용됨.

## 🐍 Python의 비교 메서드 (__lt__, __eq__, 등)
- Python에서는 __lt__, __eq__, __gt__ 등의 특수 메서드를 오버라이드하여 비교 기준을 정의합니다.
- functools.total_ordering 데코레이터를 사용하면 나머지 비교 메서드를 자동 생성할 수 있습니다.

```python
from functools import total_ordering

@total_ordering
class Item:
    def __init__(self, idNumber):
        self.idNumber = idNumber

    def __lt__(self, other):
        return self.idNumber < other.idNumber

    def __eq__(self, other):
        return self.idNumber == other.idNumber
```

- sorted() 함수에서 자동으로 사용됨.

## 📊 요약 비교 (Markdown 표)
| 언어   | 비교 방식                         | 메서드/연산자         | 자동 정렬 사용 가능 | 특징                          |
|--------|----------------------------------|------------------------|---------------------|-------------------------------|
| C#     | `IComparable<T>` 구현             | `CompareTo()`          | ✅                  | 정렬 기준 명시, LINQ와 호환   |
| C++    | 연산자 오버로딩 (`<`, `<=>`)      | `operator<`, `<=>`     | ✅                  | STL과 통합, 고성능             |
| Java   | `Comparable<T>` 구현              | `compareTo()`          | ✅                  | 컬렉션 정렬에 최적화           |
| Python | 특수 메서드 오버라이드            | `__lt__`, `__eq__`     | ✅                  | 유연한 문법, 데코레이터 지원   |


이런 비교 기능은 정렬뿐 아니라 검색, 필터링, 우선순위 큐, 트리 구조 등 다양한 곳에서 활용됩니다.

---


