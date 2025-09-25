# SelectMany
C#의 SelectMany는 중첩된 컬렉션을 평탄화(flatten)하는 데 아주 유용.
- 문자열 배열을 분할하고 평탄화 ("a, b, c" → ["a", "b", "c"])
- 객체 배열에서 내부 컬렉션 추출 (School[] → Student[])


## C# 예제
```csharp
namespace GrammarTest
{
    public class Program
    {
        class School
        {
            public Student[] Students { get; set; }
        }
        class Student
        {
            public string Name { get; set; }
        }
        public static void Main(String[] args)
        {
            {
                var words = new[] {"a, b, c", "d, e", "f"};
                var splitAndCombine = words.Select(x => x.Split(','));
                foreach (var split in splitAndCombine)
                {
                    foreach (var str in split)
                    {
                        Console.WriteLine(str.Trim());
                    }
                }
                /*
                a
                b
                c
                d
                e
                f
                 */

            }
            {
                var words = new[] {"a, b, c", "d, e", "f"};
                var splitAndCombine = words.SelectMany(x => x.Split(','));
                foreach (var split in splitAndCombine)
                {
                    Console.WriteLine(split.Trim());
                }
            }
            
            var schools = new[]
            {
                new School()
                {
                    Students = new[]
                    {
                        new Student() {Name = "Bob"},
                        new Student() {Name = "Jack"},
                    },
                   
                },
                new School()
                {
                    Students = new[]
                    {
                        new Student() {Name = "Jim"},
                        new Student() {Name = "John"},
                    },
                }
            };
            var allStudents = schools.SelectMany(s => s.Students);
            foreach (var student in allStudents)
            {
                Console.WriteLine(student.Name);
            }

        }
    }
}

```


## 🧩 C++ 예제
### 1. 문자열 분할 후 평탄화
```cpp
#include <iostream>
#include <vector>
#include <sstream>
#include <string>
#include <algorithm>

std::vector<std::string> split(const std::string& str, char delimiter) {
    std::vector<std::string> result;
    std::stringstream ss(str);
    std::string item;
    while (std::getline(ss, item, delimiter)) {
        item.erase(0, item.find_first_not_of(" \t")); // trim left
        item.erase(item.find_last_not_of(" \t") + 1); // trim right
        result.push_back(item);
    }
    return result;
}

int main() {
    std::vector<std::string> words = {"a, b, c", "d, e", "f"};
    std::vector<std::string> flattened;

    for (const auto& word : words) {
        auto parts = split(word, ',');
        flattened.insert(flattened.end(), parts.begin(), parts.end());
    }

    for (const auto& s : flattened) {
        std::cout << s << std::endl;
    }
}
```

### 2. 객체 배열 평탄화
```cpp
#include <iostream>
#include <vector>
#include <string>

struct Student {
    std::string Name;
};

struct School {
    std::vector<Student> Students;
};

int main() {
    std::vector<School> schools = {
        { { {"Bob"}, {"Jack"} } },
        { { {"Jim"}, {"John"} } }
    };

    std::vector<Student> allStudents;
    for (const auto& school : schools) {
        allStudents.insert(allStudents.end(), school.Students.begin(), school.Students.end());
    }

    for (const auto& student : allStudents) {
        std::cout << student.Name << std::endl;
    }
}
```


## ☕ Java 예제
### 1. 문자열 분할 후 평탄화
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class FlattenExample {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("a, b, c", "d, e", "f");

        List<String> flattened = words.stream()
            .flatMap(s -> Arrays.stream(s.split(",")))
            .map(String::trim)
            .collect(Collectors.toList());

        flattened.forEach(System.out::println);
    }
}
```

### 2. 객체 배열 평탄화
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

class Student {
    String name;
    Student(String name) { this.name = name; }
}

class School {
    List<Student> students;
    School(Student... students) {
        this.students = Arrays.asList(students);
    }
}

public class SchoolExample {
    public static void main(String[] args) {
        List<School> schools = Arrays.asList(
            new School(new Student("Bob"), new Student("Jack")),
            new School(new Student("Jim"), new Student("John"))
        );

        List<Student> allStudents = schools.stream()
            .flatMap(school -> school.students.stream())
            .collect(Collectors.toList());

        allStudents.forEach(s -> System.out.println(s.name));
    }
}
```


## 🐍 Python 예제

### 1. 문자열 분할 후 평탄화
```python
words = ["a, b, c", "d, e", "f"]

flattened = [part.strip() for word in words for part in word.split(",")]
for s in flattened:
    print(s)
```

### 2. 객체 배열 평탄화
```python
class Student:
    def __init__(self, name):
        self.name = name

class School:
    def __init__(self, students):
        self.students = students

schools = [
    School([Student("Bob"), Student("Jack")]),
    School([Student("Jim"), Student("John")])
]

all_students = [student for school in schools for student in school.students]
for student in all_students:
    print(student.name)

```
## ✅ 출력 결과 (공통)
```
a
b
c
d
e
f
Bob
Jack
Jim
John
```
---



