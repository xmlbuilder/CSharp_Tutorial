🧠 C#의 static 생성자란?
```csharp
class Animal<T>
{
    static Animal()
    {
        Console.WriteLine(typeof(T).FullName);
    }
}
```

- 클래스가 처음 참조될 때 한 번만 호출됨
- 정적 필드 초기화 또는 타입별 설정에 사용
- 제네릭 클래스에서는 타입별로 static 생성자가 따로 호출됨

## 🔍 실행 결과
```
System.Object
System.String
```

→ Animal<object>와 Animal<string> 각각의 static 생성자가 호출됨

## 🧩 주요 용도
| 주요 용도             | 설명                                                                 |
|----------------------|----------------------------------------------------------------------|
| 정적 필드 초기화      | 복잡한 초기값 설정이 필요한 경우, static 생성자에서 초기화 수행         |
| 제네릭 타입별 초기화  | `typeof(T)` 기반으로 타입별 설정, 캐싱, 로깅 등 구현 가능                |
| 클래스 로딩 시 로깅   | 클래스가 언제 처음 사용되는지 추적하거나 디버깅용 로그 출력               |
| 외부 리소스 연결      | 네이티브 라이브러리, 설정 파일, DB 연결 등 초기화가 필요한 외부 자원 처리 |
| 성능 최적화           | 초기화 비용이 큰 작업을 한 번만 수행하여 런타임 성능 향상                |



## 🌍 다른 언어와 비교
### 🔹 C++: 정적 초기화는 있지만 static 생성자는 없음
```cpp
class Animal {
public:
    static int count;
};

int Animal::count = []() {
    std::cout << "Static init!" << std::endl;
    return 0;
}();
```


- C++은 정적 변수 초기화 시 람다나 함수 호출 가능
- 하지만 클래스 수준의 static 생성자 개념은 없음
- 대신 정적 객체 + 생성자로 유사한 효과 가능

### 🔹 Java: static {} 블록 사용
```java
class Animal<T> {
    static {
        System.out.println("Static block called");
    }
}
```

- 클래스가 처음 로딩될 때 한 번 실행
- 제네릭 타입에 따라 static 블록이 분리되지 않음
- 즉, Animal<Object>와 Animal<String>은 동일한 static 블록

### 💡 Java는 타입별 static 초기화 불가능
C#은 제네릭 타입마다 static 생성자 분리됨


###🔹 Python: 클래스 수준 초기화는 직접 구현해야 함
```python
class Animal:
    initialized = False

    @classmethod
    def init(cls):
        if not cls.initialized:
            print("Static-like init")
            cls.initialized = True
```



- Python은 정적 생성자 없음
- 대신 @classmethod로 초기화 로직을 수동으로 구현
- 메타클래스나 싱글톤 패턴으로 유사한 효과 가능

## 📌 언어별 비교 요약
| 언어   | static 생성자 지원 | 제네릭 타입별 분리 | 초기화 방식                  |
|--------|---------------------|---------------------|------------------------------|
| C#     | ✅ 있음              | ✅ 타입별로 분리됨   | `static Animal()`            |
| C++    | ❌ 없음              | ❌                  | 정적 변수 초기화로 대체      |
| Java   | ✅ `static {}` 블록 | ❌ 하나만 존재       | 클래스 로딩 시 실행          |
| Python | ❌ 없음              | ❌                  | `@classmethod`로 수동 구현   |


## 🧠 마무리 팁
C#에서 제네릭 타입별로 static 생성자를 활용하면:
- 타입 캐시
- 타입별 설정값 로딩
- 성능 최적화

---

# TypeRegistry<T>
TypeRegistry<T>는 이름 그대로 타입별로 정보를 저장하거나 초기화하는 패턴인데, 
특히 C#의 제네릭 + static 생성자 조합을 활용하면 아주 강력한 구조를 만들 수 있어요. 
이건 라이브러리나 프레임워크에서 타입 기반 캐싱, 설정, 전략 등록 등에 자주 쓰이는 고급 패턴입니다.

## 🧠 TypeRegistry<T>란?
제네릭 타입 T에 따라 정적 데이터를 분리해서 관리하는 구조입니다.
예를 들어, TypeRegistry<string>과 TypeRegistry<int>는 서로 다른 static 필드를 갖게 되므로, 타입별로 독립적인 저장소처럼 동작합니다.
### 🔹 기본 구조 예시
```csharp
public static class TypeRegistry<T>
{
    public static string TypeName;

    static TypeRegistry()
    {
        TypeName = typeof(T).FullName;
        Console.WriteLine($"Initialized for {TypeName}");
    }
}
```

### 🔹 사용 예
```csharp
Console.WriteLine(TypeRegistry<int>.TypeName);     // System.Int32
Console.WriteLine(TypeRegistry<string>.TypeName);  // System.String
```

- 각 타입마다 TypeRegistry<T>의 static 생성자가 한 번만 호출됨
- TypeName은 타입별로 독립적으로 저장됨

## 🔧 실전 활용 예시
### 1. 타입별 전략 등록

```csharp
public static class TypeRegistry<T>
{
    public static Func<T, string> Serializer;

    static TypeRegistry()
    {
        if (typeof(T) == typeof(int))
            Serializer = x => $"int:{x}";
        else if (typeof(T) == typeof(string))
            Serializer = x => $"str:{x}";
        else
            Serializer = x => $"unknown:{x}";
    }
}


string result = TypeRegistry<int>.Serializer(42);       // int:42
string result2 = TypeRegistry<string>.Serializer("hi"); // str:hi
```

### 2. 타입별 캐시
```csharp
public static class TypeRegistry<T>
{
    public static List<T> Cache = new List<T>();
}
```

- TypeRegistry<string>.Cache와 TypeRegistry<int>.Cache는 서로 다른 리스트
- 타입별로 데이터를 분리해서 저장 가능

## 🌍 다른 언어에서는?
| 언어     | 구현 가능 여부 | 제네릭/템플릿 기반 | 타입별 static 분리 | 설명 |
|----------|----------------|---------------------|---------------------|------|
| C#       | ✅ 가능         | 제네릭 + static 생성자 | ✅ 타입별로 분리됨   | `TypeRegistry<T>` 패턴 구현에 최적화됨 |
| Java     | ❌ 제한적       | 제네릭 (타입 소거 발생) | ❌ static 필드는 공유됨 | 타입별 static 분리 불가능, 런타임에 타입 정보 소거됨 |
| C++      | ✅ 가능         | 템플릿 기반           | ✅ 타입별로 분리됨   | 템플릿 클래스의 static 멤버는 타입마다 독립적 |
| Python   | ❌ 직접 구현 필요 | 동적 타입 시스템       | ❌ 클래스 수준에서 분리 어려움 | 메타클래스나 딕셔너리 기반으로 유사 구현 가능 |



## 📌 요약: TypeRegistry<T>의 장점
| 특징               | 설명                                                  |
|--------------------|-------------------------------------------------------|
| 타입별 분리         | 제네릭 타입마다 독립적인 static 필드/생성자 존재       |
| 초기화 제어         | static 생성자로 타입별 초기화 가능                     |
| 전략/캐시 등록      | 타입별 전략 함수, 캐시, 설정값 등을 저장 가능          |
| 성능 최적화         | 한 번 초기화 후 재사용 가능, 런타임 비용 감소           |




## 🧠 핵심 개념: 템플릿 클래스의 static 멤버는 타입마다 독립적이다
C++에서는 템플릿 클래스의 static 멤버가 타입 T마다 별도로 생성되기 때문에, 
TypeRegistry<int>와 TypeRegistry<std::string>은 서로 다른 static 데이터를 갖습니다.

## 🛠️ 구현 예시: TypeRegistry<T> in C++
```cpp
#include <iostream>
#include <unordered_map>
#include <string>

template <typename T>
class TypeRegistry {
public:
    static std::unordered_map<std::string, T> registry;

    static void Register(const std::string& key, const T& value) {
        registry[key] = value;
    }

    static T Get(const std::string& key) {
        return registry.at(key);
    }

    static void PrintAll() {
        for (const auto& [key, value] : registry) {
            std::cout << key << " => " << value << std::endl;
        }
    }
};

// static 멤버 정의
template <typename T>
std::unordered_map<std::string, T> TypeRegistry<T>::registry;
```


## ✅ 사용 예시
```cpp
int main() {
    TypeRegistry<int>::Register("one", 1);
    TypeRegistry<int>::Register("two", 2);

    TypeRegistry<std::string>::Register("hello", "world");
    TypeRegistry<std::string>::Register("foo", "bar");

    std::cout << "Int Registry:" << std::endl;
    TypeRegistry<int>::PrintAll();

    std::cout << "\nString Registry:" << std::endl;
    TypeRegistry<std::string>::PrintAll();

    return 0;
}
```


## 🧪 출력 결과
```
Int Registry:
one => 1
two => 2

String Registry:
hello => world
foo => bar
```


## 💡 요점 정리
| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 타입별 static 분리       | 템플릿 클래스의 static 멤버는 타입마다 독립적으로 생성됨             |
| 템플릿 기반 구현         | `TypeRegistry<T>`는 템플릿을 사용하여 다양한 타입에 대해 확장 가능     |
| 런타임 없이 타입 분리    | 컴파일 시점에 타입별로 클래스 인스턴스가 생성되어 런타임 비용 없음     |
| 확장성                   | 새로운 타입에 대해 별도 registry를 쉽게 추가 가능                     |
| 활용 예시                | 플러그인 시스템, 타입별 설정 저장, 런타임 타입 매핑 등                 |
| C#과의 유사성            | C#의 `static` 제네릭 필드 분리와 유사한 구조                          |


## 🧱 C++에서 생성자/소멸자 등록 방식 요약 (Markdown 표)
| 항목               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| 리플렉션 부재       | C++은 런타임 리플렉션이 없어 타입 이름, 생성자 호출 등을 자동화하기 어려움 |
| 생성자 등록 방식    | 함수 포인터 또는 람다를 통해 타입별 생성 함수를 registry에 등록           |
| 소멸자 등록 방식    | 필요 시 custom deleter 또는 소멸자 래퍼를 함께 등록 가능                   |
| 타입 이름 매핑      | `typeid(T).name()` 또는 수동 문자열 키로 타입을 구분                       |
| 활용 예시           | 팩토리 패턴, 플러그인 로딩, 타입별 초기화, 커스텀 직렬화 등                 |



## 🛠️ 예시 코드: 타입별 생성자 등록
```cpp
#include <iostream>
#include <unordered_map>
#include <functional>
#include <memory>
#include <string>

class Base {
public:
    virtual void Speak() = 0;
    virtual ~Base() = default;
};

class Dog : public Base {
public:
    void Speak() override { std::cout << "Woof!" << std::endl; }
};

class Cat : public Base {
public:
    void Speak() override { std::cout << "Meow!" << std::endl; }
};

class TypeFactory {
public:
    using Creator = std::function<std::unique_ptr<Base>()>;

    static void Register(const std::string& typeName, Creator creator) {
        GetRegistry()[typeName] = creator;
    }

    static std::unique_ptr<Base> Create(const std::string& typeName) {
        auto it = GetRegistry().find(typeName);
        if (it != GetRegistry().end()) {
            return it->second();
        }
        return nullptr;
    }

private:
    static std::unordered_map<std::string, Creator>& GetRegistry() {
        static std::unordered_map<std::string, Creator> registry;
        return registry;
    }
};
```


## ✅ 등록 및 사용
```cpp
int main() {
    TypeFactory::Register("dog", []() { return std::make_unique<Dog>(); });
    TypeFactory::Register("cat", []() { return std::make_unique<Cat>(); });

    auto pet1 = TypeFactory::Create("dog");
    auto pet2 = TypeFactory::Create("cat");

    if (pet1) pet1->Speak();  // Woof!
    if (pet2) pet2->Speak();  // Meow!

    return 0;
}
```


## 💡 확장 팁
- typeid(T).name()을 키로 쓰면 자동화 가능하지만, 플랫폼마다 이름이 달라질 수 있어요
- std::any, std::variant, std::type_index 등을 활용하면 더 유연한 타입 관리 가능
- shared_ptr + custom deleter를 쓰면 소멸자도 커스터마이징 가능

---


