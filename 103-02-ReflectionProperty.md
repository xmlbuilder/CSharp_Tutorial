# C# PropertyInfo
 C#의 Reflection 기능을 활용해 런타임에 객체의 속성을 탐색하고 값을 가져오는 예시. 특히 PropertyInfo를 통해 "Length"라는 이름을 가진 속성을 찾아 출력. 

## 🧠 Reflection이란?
Reflection은 프로그램이 자기 자신을 런타임에 조사하거나 수정할 수 있는 기능입니다. 클래스의 메서드, 속성, 필드 등을 동적으로 탐색하거나 호출할 수 있어요. 주로 다음과 같은 상황에서 유용합니다:
- 직렬화/역직렬화
- ORM(Object-Relational Mapping)
- 테스트 프레임워크
- 플러그인 시스템

## ✅ C# 코드 요약
```csharp
using System.Reflection;
namespace  GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            string sample = "Sample Model";
            Type type = sample.GetType();
            
            PropertyInfo[] propertyInfos = type.GetProperties();
            foreach (var propertyInfo in propertyInfos)
            {
                if (propertyInfo.Name.Contains("Length"))
                {
                    Console.WriteLine(propertyInfo.Name);
                    //Length

                    // public int Length
                    // {
                    //   [Intrinsic] get => this._stringLength;
                    // }

                    Console.WriteLine(propertyInfo.GetValue(sample, null));
                    //12
                }
            }
        }
    }
}


```

```csharp
string sample = "Sample Model";
Type type = sample.GetType();
PropertyInfo[] propertyInfos = type.GetProperties();

foreach (var propertyInfo in propertyInfos)
{
    if (propertyInfo.Name.Contains("Length"))
    {
        Console.WriteLine(propertyInfo.Name); // Length
        Console.WriteLine(propertyInfo.GetValue(sample, null)); // 12
    }
}
```


## 🟦 Java 대응 코드
Java에서는 java.lang.reflect 패키지를 사용합니다.
import java.lang.reflect.*;

```java

public class ReflectionExample {
    public static void main(String[] args) throws Exception {
        String sample = "Sample Model";
        Class<?> clazz = sample.getClass();

        Method[] methods = clazz.getMethods();
        for (Method method : methods) {
            if (method.getName().equals("length") && method.getParameterCount() == 0) {
                Object result = method.invoke(sample);
                System.out.println("Method: " + method.getName()); // length
                System.out.println("Value: " + result); // 12
            }
        }
    }
}
```

Java에서는 length가 속성이 아니라 메서드이므로 getMethods()로 접근해야 합니다.


## 🐍 Python 대응 코드
Python은 매우 직관적인 Reflection 기능을 제공합니다.
```python
sample = "Sample Model"

# dir()로 속성 목록 확인
for attr in dir(sample):
    if "len" in attr.lower():
        value = getattr(sample, attr) if not callable(getattr(sample, attr)) else getattr(sample, attr)()
        print(f"Attribute: {attr}")  # __len__
        print(f"Value: {value}")     # 12

```

Python에서는 __len__() 메서드가 len() 함수로 노출되므로 직접 호출하거나 len(sample)을 사용해도 됩니다.


## 🧩 C++에서의 대안
C++은 정적 타입 언어라서 기본적으로 Reflection을 지원하지 않지만, 몇 가지 대안이 있어요:
### 1. RTTI (Run-Time Type Information)
```cpp
#include <iostream>
#include <typeinfo>

int main() {
    std::string sample = "Sample Model";
    std::cout << "Type: " << typeid(sample).name() << std::endl;
    std::cout << "Length: " << sample.length() << std::endl;
}
```

typeid는 타입 이름만 제공하고 속성 탐색은 불가능합니다.

## 🧭 Reflection 기능 비교

| 언어     | Reflection 지원 수준 | 속성/메서드 접근 방식     | 예시 객체 속성 접근 | 특징 요약                          |
|----------|----------------------|----------------------------|---------------------|-----------------------------------|
| **C#**   | ✅ 매우 강력          | `PropertyInfo`, `MethodInfo` | `propertyInfo.GetValue(obj)` | 런타임에 타입, 속성, 메서드 모두 탐색 가능 |
| **Java** | ✅ 중간 수준          | `Method`, `Field`           | `method.invoke(obj)` | 메서드 중심, 속성은 `Field`로 접근 |
| **Python** | ✅ 매우 유연         | `getattr`, `dir`, `__dict__` | `getattr(obj, "attr")` | 동적 타입, 매우 직관적인 Reflection |
| **C++**  | ❌ 기본 미지원        | `typeid`, 매크로, 라이브러리 | `sample.length()`   | RTTI만 기본 제공, 고급 기능은 외부 도구 필요 |

---


# 🌸 Boost.Hana란?
- 헤더 전용 라이브러리: 별도의 링크 과정 없이 #include만으로 사용 가능
- C++14 이상 필요: 최신 C++ 기능을 적극 활용
- 타입과 값 모두에 대한 컴파일 타임 연산 지원
- 표준 라이브러리 및 Boost의 다른 라이브러리와 호환 가능
Hana는 타입 리스트, 정적 조건문, 정적 반복문, 정적 맵 등 다양한 기능을 제공합니다.


## 🛠 설치 방법
Boost 1.61 이상에 포함되어 있으며, 별도로 설치할 수도 있습니다:
git clone https://github.com/boostorg/hana.git
cd hana
mkdir build && cd build
cmake ..
cmake --build . --target install
CMake를 사용하는 프로젝트에서는 FindHana.cmake 모듈을 통해 쉽게 통합할 수 있어요.

🧪 샘플 코드: 구조체의 필드 이름과 값 출력
```cpp
#include <boost/hana.hpp>
#include <iostream>
#include <string>

namespace hana = boost::hana;
using namespace std::literals;

struct Person {
    BOOST_HANA_DEFINE_STRUCT(Person,
        (std::string, name),
        (int, age)
    );
};

int main() {
    Person p{"JungHwan", 30};

    hana::for_each(hana::members(p), [](const auto& value) {
        std::cout << value << std::endl;
    });

    hana::for_each(hana::accessors<Person>(), [&](const auto& pair) {
        auto name = hana::to<const char*>(hana::first(pair));
        auto accessor = hana::second(pair);
        std::cout << name << ": " << accessor(p) << std::endl;
    });

    return 0;
}
```

## 🔍 코드 설명
- BOOST_HANA_DEFINE_STRUCT: 구조체를 Hana가 인식할 수 있도록 정의
- hana::members(p): 구조체의 멤버 값들을 순회
- hana::accessors<Person>(): 멤버 이름과 접근자 쌍을 반환
- hana::first(pair): 멤버 이름
- hana::second(pair)(p): 멤버 값


## 📌 Boost.Hana의 주요 기능

| 기능명                  | 설명                                                                 |
|------------------------|----------------------------------------------------------------------|
| `BOOST_HANA_DEFINE_STRUCT` | 구조체를 Hana가 인식할 수 있도록 정의. 멤버 이름과 값을 컴파일 타임에 접근 가능. |
| `hana::tuple`          | 컴파일 타임에 타입 안전한 튜플을 생성. `std::tuple`보다 더 강력한 메타프로그래밍 지원. |
| `hana::map`            | 키-값 쌍을 컴파일 타임에 저장하고 조회할 수 있는 정적 맵.                                    |
| `hana::for_each`       | 컴파일 타임 반복문. 튜플이나 맵의 각 요소에 대해 함수 적용 가능.                             |
| `hana::if_`            | 컴파일 타임 조건문. `constexpr` 조건에 따라 분기 처리 가능.                                  |




## 🧩 대안이 있을까?
1. 일반 구조체 + 수동 매핑
```cpp
struct Person {
    std::string name;
    int age;
};

auto person_members = hana::make_tuple(
    hana::make_pair("name"_s, [](const Person& p) { return p.name; }),
    hana::make_pair("age"_s, [](const Person& p) { return p.age; })
);
```

이렇게 하면 BOOST_HANA_DEFINE_STRUCT 없이도 Hana 스타일로 접근 가능하지만, 멤버마다 수동으로 매핑해야 하므로 유지보수가 어렵습니다.

---








