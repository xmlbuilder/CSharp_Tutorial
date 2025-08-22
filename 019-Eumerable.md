# 🧭 C#의 IEnumerable & IEnumerator
## 핵심 개념
- IEnumerable: 반복 가능한 컬렉션을 정의하는 인터페이스
- IEnumerator: 반복자의 동작을 정의 (MoveNext(), Current, Reset())
## 사용자 정의 예제 요약
```csharp
public class Persons : IEnumerable {
    public IEnumerator GetEnumerator() => new PersonsEnum(_person);
}

public class PersonsEnum : IEnumerator {
    public bool MoveNext() { ... }
    public object Current { get; }
    public void Reset() { ... }
}
```
## 구현 예
```csharp
using System.Collections;

public class PersonInfo
{
    public string Name { get; set; }
    public int Age { get; set; }
    
    public PersonInfo(string _name, int _age)
    {
        this.Name = _name;
        this.Age = _age;
    }
}

/*
순서 2. 열거자를 반환하는 클래스를 구현합니다.
Person 객체를 반환하는 IEnumerable 인터페이스의 GetEnumerator() 메서드를 구현합니다.
*/

public class Persons : IEnumerable
{
    private PersonInfo[] _person;
    
    public Persons(PersonInfo[] uArray)
    {
        _person = new PersonInfo[uArray.Length];
        for (int i = 0; i < uArray.Length; i++)
        {
            _person[i] = uArray[i];
        }
    }
    
    IEnumerator IEnumerable.GetEnumerator()
    {
        return (IEnumerator)GetEnumerator();
    }
    
    public PersonsEnum GetEnumerator()
    {
        return new PersonsEnum(_person);
    }
}

/*
순서 3. 컬렉션 요소를 반복적으로 접근하기 위해 IEnumerator 인터페이스를 가지는 클래스를 구현합니다.
*/
public class PersonsEnum : IEnumerator
{
    public PersonInfo[] _person;
    int currentIndex = -1;
    
    public PersonsEnum(PersonInfo[] list)
    {
        _person = list;
    }
    
    public bool MoveNext()
    {
        currentIndex++;
        return (currentIndex < _person.Length);
    }
    
    object IEnumerator.Current
    {
        get { return Current; }
    }
    
    public PersonInfo Current
    {
        get
        {
            try
            {
                return _person[currentIndex];
            }
            catch (IndexOutOfRangeException)
            {
                throw new InvalidOperationException();
            }
        }
    }
    
    public void Reset()
    {
        currentIndex = -1;
    }
}

class Program
{
    static void Main(string[] args)
    {
        PersonInfo[] pInfo = new PersonInfo[3] {
            new PersonInfo("홍길동", 20),
            new PersonInfo("또치", 100),
            new PersonInfo("마이콜", 22)
        };
        
        Persons persons = new Persons(pInfo);
        foreach (var person in persons)
        {
            Console.WriteLine("person.Name: " + person.Name + ", person.Age: " + person.Age);
        }
        /*
         person.Name: 홍길동, person.Age: 20
         person.Name: 또치, person.Age: 100
         person.Name: 마이콜, person.Age: 22
         */
        
        
        //Array도 Ienumrable interface을 상속하고 있으므로 IEnumerable을 사용 가능하다.
        int[] arr1 = {3, 5, 7};

        IEnumerable<int> enumerableIntegers = arr1;

        List<int> listOfIntegers = new List<int>();
        listOfIntegers.AddRange(arr1);

        Console.WriteLine(listOfIntegers.Count);
        //3
    }
}


```


## 📌 foreach 문에서 자동으로 GetEnumerator()를 호출하여 반복

### 🧭 C++의 iterator & STL 스타일
#### 핵심 개념
- C++의 반복자는 포인터처럼 동작하는 객체
- begin()과 end()를 통해 범위 기반 for문에서 사용
- 사용자 정의 반복자는 operator++, operator*, operator!= 등을 구현
#### 사용자 정의 예제 (C++ 스타일)
```cpp
#include <iostream>
#include <vector>

class PersonInfo {
public:
    std::string name;
    int age;
    PersonInfo(std::string n, int a) : name(n), age(a) {}
};

class Persons {
    std::vector<PersonInfo> people;
public:
    void add(PersonInfo p) { people.push_back(p); }

    auto begin() { return people.begin(); }
    auto end() { return people.end(); }
};

int main() {
    Persons persons;
    persons.add({"홍길동", 20});
    persons.add({"또치", 100});
    persons.add({"마이콜", 22});

    for (const auto& person : persons) {
        std::cout << "person.Name: " << person.name << ", person.Age: " << person.age << "\n";
    }
}
```

#### 📌 std::vector의 반복자를 그대로 활용하거나, 커스텀 반복자 클래스를 만들 수도 있음

## 🧭 Java의 Iterable & Iterator
### 핵심 개념
- Iterable: iterator() 메서드를 제공하는 인터페이스
- Iterator: hasNext(), next() 메서드를 구현
#### 사용자 정의 예제
```java
class PersonInfo {
    String name;
    int age;
    PersonInfo(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

class Persons implements Iterable<PersonInfo> {
    private PersonInfo[] people;

    public Persons(PersonInfo[] people) {
        this.people = people;
    }

    public Iterator<PersonInfo> iterator() {
        return new Iterator<PersonInfo>() {
            int index = 0;
            public boolean hasNext() {
                return index < people.length;
            }
            public PersonInfo next() {
                return people[index++];
            }
        };
    }
}

public class Main {
    public static void main(String[] args) {
        PersonInfo[] pInfo = {
            new PersonInfo("홍길동", 20),
            new PersonInfo("또치", 100),
            new PersonInfo("마이콜", 22)
        };

        Persons persons = new Persons(pInfo);
        for (PersonInfo person : persons) {
            System.out.println("person.Name: " + person.name + ", person.Age: " + person.age);
        }
    }
}
```

#### 📌 for-each 문은 Iterable을 구현한 클래스에서 자동으로 iterator()를 호출


## 📊 비교 요약
| 항목 | C# (IEnumerable) | C++ (iterator) | Java (Iterable) | 
|------|------------------|----------------|-----------------|
| 반복자 인터페이스 | IEnumerator | iterator 또는 포인터 스타일 | Iterator | 
| 반복자 메서드 | MoveNext(), Current | operator++, operator* | hasNext(), next() | 
| foreach 지원 | foreach | range-based for (C++11~) | for-each | 
| 사용자 정의 가능 | IEnumerable 구현 필요 | begin(), end() 구현 필요 | Iterable 구현 필요 | 
| 제네릭 지원 | IEnumerable<T> | template 기반 | Iterable<T> | 

---

## 🧠 마무리 팁
- C#은 yield return을 사용하면 반복자 구현을 훨씬 간단하게 만들 수 있어요.
- C++은 std::iterator를 상속하거나 std::ranges를 활용하면 더 현대적인 반복이 가능해요.
- Java는 Stream API로 더 선언적인 반복이 가능하죠 (forEach, map, filter 등).


