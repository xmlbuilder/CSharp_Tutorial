# 🧠 C#의 Collection Initializer
## ✅ 조건
- IEnumerable 구현
- Add() 메서드 존재
## ✅ 특징
- 객체 생성과 동시에 요소 추가 가능
- 오버로드된 Add()도 지원
- 확장 메서드 Add()도 사용 가능
## ✅ 예시
```csharp

var col = new MyCollection()
{
    "item1",
    {"item2", 3},
    123.45d
};
```

## 소스
```csharp
namespace GrammarTest
{
    public class Tag1
    {
        public IList<string> Synonyms { get; set; }
    }

    public class Tag2
    {
        public IList<string> Synonyms { get; private set; }

        public Tag2()
        {
            Synonyms = new List<string>();
        }
    }
    
    class Program
    {
        public static void Main(string[] args)
        {
            Tag1 t1 = new Tag1()
            {
                Synonyms = new List<string>() {"C#", "VB"}
            };
            
            /*
            Tag2 t2 = new Tag2()
            {
                Synonyms = new List<string>() {"C#", "VB"}
            };
            */

            Tag2 t2 = new Tag2()
            {
                Synonyms = {"C#", "VB"}
            };
        }
    }
}


```



## 🌍 다른 언어에서의 유사 기능
### 🔹 C++: 생성자 + initializer_list
C++은 std::initializer_list를 통해 유사한 초기화 문법을 제공합니다.
```cpp
#include <iostream>
#include <vector>
#include <initializer_list>

class MyCollection {
    std::vector<std::string> list;
public:
    MyCollection(std::initializer_list<std::string> init) {
        for (auto& item : init)
            list.push_back(item);
    }

    void print() {
        for (auto& item : list)
            std::cout << item << "\n";
    }
};

MyCollection col = {"item1", "item2", "baz"};
col.print();
```

- 제약: 오버로드된 Add()처럼 다양한 타입은 직접 처리해야 함

### 🔹 Java: 정적 팩토리 메서드 또는 생성자
Java는 컬렉션 초기화에 Arrays.asList() 또는 List.of()를 자주 사용하며, 커스텀 클래스에서는 생성자나 빌더 패턴을 활용합니다.
```java
class MyCollection {
    List<String> list = new ArrayList<>();

    public MyCollection(String... items) {
        list.addAll(Arrays.asList(items));
    }

    public void print() {
        list.forEach(System.out::println);
    }
}

MyCollection col = new MyCollection("item1", "item2", "baz");
col.print();
```

- 대안: Builder 패턴으로 유연한 초기화 가능

###  🔹 Python: __init__ + *args 또는 **kwargs
Python은 매우 유연해서 생성자에서 가변 인자를 받아 초기화하는 방식이 일반적입니다.
```python
class MyCollection:
    def __init__(self, *items):
        self.list = []
        for item in items:
            if isinstance(item, tuple):
                self.list.extend([item[0]] * item[1])
            else:
                self.list.append(str(item))

    def __iter__(self):
        return iter(self.list)

col = MyCollection("item1", ("item2", 3), "baz", 123.45)
for val in col:
    print(val)

```

- 장점: 타입 혼합, 튜플 처리 등 자유롭게 구현 가능

## 📊 요약 비교 (Markdown 표)
| 언어   | 초기화 방식                        | 유연성 | 특징 및 메모                                 |
|--------|-----------------------------------|--------|----------------------------------------------|
| C#     | Collection Initializer + Add()    | ✅     | `IEnumerable` + `Add()`으로 자동 초기화 가능 |
| C++    | `initializer_list` + 생성자       | ⚠️     | 타입 제한 있음, 오버로드는 직접 처리 필요    |
| Java   | 생성자 + `varargs` 또는 `List.of()`| ✅     | 빌더 패턴으로 확장 가능                      |
| Python | `__init__` + `*args`              | ✅✅   | 가장 유연, 튜플/타입 혼합도 자유롭게 처리    |



이런 초기화 방식은 특히 DSL 스타일 API, 테스트용 데이터 생성, 구성 객체 조립 등에 매우 유용해요.

---


## 🎯 Using collection initializer inside object initializer
기존 방식은 이렇게 두 단계로 나눠야 했죠:
```csharp
var tag = new Tag1();
tag.Synonyms = new List<string>();
tag.Synonyms.Add("C#");
tag.Synonyms.Add("VB");
```

하지만 이걸 한 번에 표현하면:
```csharp
var tag = new Tag1()
{
    Synonyms = { "C#", "VB" }
};
```

이처럼 객체 생성 → 컬렉션 초기화 → 요소 추가를 한 줄로 표현할 수 있어요.
특히 UI 바인딩, 테스트 데이터, 설정 객체 등을 만들 때 매우 유용하죠.

## ⚠️ 왜 Tag2는 오류가 날까?
```csharp
Tag2 t2 = new Tag2()
{
    Synonyms = new List<string>() {"C#", "VB"} // ❌ 오류
};
```

이건 컬렉션 초기화 문법인데, Synonyms 속성이 private set이라서 초기화 시점에 새 컬렉션을 할당할 수 없기 때문이에요.
하지만 Synonyms 속성 자체가 이미 new List<string>()으로 초기화되어 있으므로, 컬렉션 초기화 문법을 사용할 수 있어요:
```csharp
Tag2 t2 = new Tag2()
{
    Synonyms = { "C#", "VB" } // ✅ 내부 컬렉션에 Add() 호출됨
};
```

## 소스

```csharp
namespace GrammarTest
{
    public class Tag1
    {
        public IList<string> Synonyms { get; set; }
    }

    public class Tag2
    {
        public IList<string> Synonyms { get; private set; }

        public Tag2()
        {
            Synonyms = new List<string>();
        }
    }
    
    class Program
    {
        public static void Main(string[] args)
        {
            Tag1 t1 = new Tag1()
            {
                Synonyms = new List<string>() {"C#", "VB"}
            };
            
            /*
            Tag2 t2 = new Tag2()
            {
                Synonyms = new List<string>() {"C#", "VB"}
            };
            */

            Tag2 t2 = new Tag2()
            {
                Synonyms = {"C#", "VB"}
            };
        }
    }
}


```


## 🌍 다른 언어에서의 유사 기능
### 🔹 C++: 생성자 + initializer_list
```cpp
class Tag {
public:
    std::vector<std::string> synonyms;
    Tag(std::initializer_list<std::string> init) : synonyms(init) {}
};

Tag t = {"C#", "VB"};
```

- 제약: 컬렉션 속성은 직접 초기화해야 함
- 장점: initializer_list로 간결한 초기화 가능

### 🔹 Java: 생성자 + Arrays.asList() 또는 List.of()
```java
class Tag {
    List<String> synonyms;

    public Tag(List<String> synonyms) {
        this.synonyms = synonyms;
    }
}

Tag t = new Tag(Arrays.asList("C#", "VB"));
```


- 제약: 컬렉션 초기화는 생성자 또는 팩토리 메서드로 처리
- 장점: List.of() (Java 9+)로 불변 리스트도 가능

### 🔹 Python: __init__ + 리스트 인자
```python
class Tag:
    def __init__(self, synonyms=None):
        self.synonyms = synonyms or []

t = Tag(["C#", "VB"])
```

- 장점: 매우 유연하고 직관적
- 추가: dataclass를 쓰면 더 간결하게 표현 가능
```python
from dataclasses import dataclass, field

@dataclass
class Tag:
    synonyms: list = field(default_factory=list)

t = Tag(synonyms=["C#", "VB"])
```



## 📌 요약
| 언어   | 객체 + 컬렉션 초기화 방식                   | 지원 여부 | 특징 및 메모                                                  |
|--------|-----------------------------------------------|-----------|------------------------------------------------------|
| C#     | `{ Property = { item1, item2 } }`             | ✅        | `IEnumerable` + `Add()` 필요, 매우 직관적이고 강력한 문법     |
| C++    | 생성자 + `initializer_list`                   | ⚠️        | 컬렉션 속성 직접 초기화 필요, 오버로드된 초기화는 수동 처리   |
| Java   | 생성자 + `Arrays.asList()` 또는 `List.of()`   | ⚠️        | 객체 초기화와 컬렉션 초기화는 분리, 빌더 패턴으로 확장 가능    |
| Python | `__init__` + 리스트 인자 또는 `dataclass`     | ✅        | 가장 유연함, 튜플/혼합 타입도 자유롭게 처리 가능              |


---



