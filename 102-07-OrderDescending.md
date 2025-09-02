# OrderByDescending
OrderByDescending은 C# LINQ에서 컬렉션을 내림차순으로 정렬할 때 사용하는 메서드입니다


## 🧠 핵심 요약: OrderByDescending
| 언어   | 내림차순 정렬 방식                                            |
|--------|---------------------------------------------------------------|
| C#     | OrderByDescending(p => p.Age)                                 |
| C++    | std::sort(..., [](a, b) { return a.Age > b.Age; })            |
| Java   | Comparator.comparing(Person::getAge).reversed()              |
| Python | sorted(people, key=lambda p: p.age, reverse=True)            |


## C#  예제
```csharp
namespace  GrammarTest
{
    public class Program
    {
        class Person
        {
            public string Name { get; set; }
            public int Age { get; set; }
            public override string ToString()
            {
                return $"[Name : {Name}, Age : {Age}]";
            }
        }
        public static void Main(String[] args)
        {
            var people = new[]
            {
                new Person() {Name = "Alice", Age = 25},
                new Person() {Name = "Bob", Age = 21},
                new Person() {Name = "Carol", Age = 43}
            };

            var oldestPerson = people.OrderByDescending(p => p.Age).First();
            Console.WriteLine(oldestPerson);
            //[Name : Carol, Age : 43]
        }
    }
}

```


## 🧩 C++ 예제
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

struct Person {
    std::string Name;
    int Age;
};

int main() {
    std::vector<Person> people = {
        {"Alice", 25},
        {"Bob", 21},
        {"Carol", 43}
    };

    std::sort(people.begin(), people.end(), [](const Person& a, const Person& b) {
        return a.Age > b.Age; // 내림차순
    });

    const Person& oldest = people.front();
    std::cout << "[Name : " << oldest.Name << ", Age : " << oldest.Age << "]" << std::endl;
}

```



☕ Java 예제
```java
import java.util.*;

class Person {
    String name;
    int age;
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String toString() {
        return "[Name : " + name + ", Age : " + age + "]";
    }
}

public class Main {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 25),
            new Person("Bob", 21),
            new Person("Carol", 43)
        );

        people.sort(Comparator.comparingInt(p -> p.age).reversed());

        Person oldest = people.get(0);
        System.out.println(oldest);
    }
}

```

## 🐍 Python 예제
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def __str__(self):
        return f"[Name : {self.name}, Age : {self.age}]"

people = [
    Person("Alice", 25),
    Person("Bob", 21),
    Person("Carol", 43)
]

sorted_people = sorted(people, key=lambda p: p.age, reverse=True)
oldest = sorted_people[0]
print(oldest)
```


✅ 출력 결과 (공통)
```
[Name : Carol, Age : 43]
```
----





