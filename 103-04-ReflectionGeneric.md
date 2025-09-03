# Refelction Generic Type
C#의 MakeGenericType과 Activator.CreateInstance처럼 런타임에 제네릭 타입을 생성하고 인스턴스를 만드는 방식.

## C#
```csharp
namespace GrammarTest
{
 
    public class Program
    {
       public static void Main(String[] args)
        {
            Type openType = typeof(List<>);
            Type[] tArgs = {typeof(string)};

            Type target = openType.MakeGenericType(tArgs);

            List<string> result = (List<string>) Activator.CreateInstance(target);

            result.Add("Model");
            result.Add("Model1");
            foreach (var str in result)
            {
                Console.WriteLine(str);
            }
            //Model
            //Model1
            
        }
    }
}
```


## ☕ Java: Reflection + Generics
Java는 타입 소거(Type Erasure) 때문에 런타임에 제네릭 타입 정보를 완전히 유지하지는 않지만, 
클래스나 메서드 선언부에서는 일부 정보를 추출하거나 활용할 수 있어요.
✅ Java 대응 예제
```java
import java.lang.reflect.*;
import java.util.*;

public class ReflectionGenericJava {
    public static void main(String[] args) throws Exception {
        Class<?> rawType = Class.forName("java.util.ArrayList");
        Constructor<?> constructor = rawType.getConstructor();
        Object instance = constructor.newInstance();

        Method addMethod = rawType.getMethod("add", Object.class);
        addMethod.invoke(instance, "Model");
        addMethod.invoke(instance, "Model1");

        Method getMethod = rawType.getMethod("get", int.class);
        System.out.println(getMethod.invoke(instance, 0)); // Model
        System.out.println(getMethod.invoke(instance, 1)); // Model1
    }
}
```

## 🔍 설명
- Class<?> rawType = Class.forName("java.util.ArrayList"): 제네릭 없이 raw 타입으로 접근
- addMethod.invoke(instance, "Model"): 런타임에 값 추가
- 제네릭 타입은 런타임에 유지되지 않지만, Object로 처리 가능
Java에서는 List<String>처럼 구체적인 제네릭 타입을 런타임에 생성하는 건 불가능하지만, raw 타입으로 동작은 동일하게 구현할 수 있어요.


## 🐍 Python: Reflection + 동적 타입
Python은 동적 타입 언어이기 때문에 제네릭 타입을 생성하거나 조작하는 데 훨씬 자유롭습니다. 
특히 typing 모듈과 type() 함수, __class__ 등을 활용하면 유사한 기능을 쉽게 구현할 수 있어요.
### ✅ Python 대응 예제

```python
from typing import List

# 런타임에 List[str] 타입처럼 동작하는 객체 생성
result = []

# 값 추가
result.append("Model")
result.append("Model1")

# 출력
for item in result:
    print(item)
```


### 🔍 설명
- Python에서는 List[str]은 타입 힌트일 뿐, 런타임에는 그냥 list 타입
- append()와 for 루프는 모두 런타임에 동작
- 필요하다면 type()이나 __class__로 타입 정보 확인 가능

### 🧪 고급: 런타임에 타입 생성
```python
DynamicList = type("DynamicList", (list,), {})
instance = DynamicList()
instance.append("Model")
instance.append("Model1")

for item in instance:
    print(item)
```

type()을 사용하면 C#의 Activator.CreateInstance처럼 런타임에 클래스 생성이 가능합니다.



## 🧭 Reflection + Generic 타입 생성 비교

| 언어     | 제네릭 타입 생성 방식       | 인스턴스 생성 방식           | 제네릭 정보 유지 여부 |
|----------|-----------------------------|-------------------------------|------------------------|
| **C#**   | `MakeGenericType`           | `Activator.CreateInstance`    | ✅ 유지됨              |
| **Java** | ❌ (타입 소거로 불가)        | `newInstance()` 또는 `Constructor.newInstance()` | ❌ 대부분 소거됨       |
| **Python** | `type()` 또는 `typing.List[str]` | 직접 생성 (`list()`, `[]`)     | ✅ 힌트로 유지 가능     |

---
# *타입 소거(Type Erasure)

Java의 **제네릭(Generic)**이 런타임에 Object로 퇴화되는 이유는 바로 하위 호환성(Backward Compatibility) 때문입니다. 
이 현상을 **타입 소거(Type Erasure)**라고 부르며, Java의 제네릭 설계 철학을 이해하는 핵심 키워드예요.

## 🔍 왜 Java는 제네릭을 Object로 퇴화시키는가?
### 🔹 이유: 기존 코드와의 호환성 유지
Java는 2004년 JDK 5에서 제네릭을 도입했는데, 그 이전에 작성된 수많은 라이브러리와 코드들이 List, Map 등을 **비제네릭(raw type)**으로 사용하고 있었어요. 
제네릭을 도입하면서도 기존 코드를 깨지 않기 위해 다음과 같은 전략을 취했죠:
- 컴파일 타임에 타입 체크 → 안전성 확보
- 런타임에는 타입 정보 제거 → 기존 JVM과 호환
이게 바로 **타입 소거(Type Erasure)**입니다.
```java
List<String> list = new ArrayList<>();
list.add("Hi");
String s = list.get(0);
```

이 코드는 컴파일 후 다음처럼 변환됩니다:
```java
List list = new ArrayList();
list.add("Hi");
String s = (String) list.get(0);
```


런타임에는 List<String>이 그냥 List로 처리되고, 내부적으로는 Object로 저장됩니다.


### 🚫 제네릭 안에서 배열이 안 되는 이유
Java에서는 다음과 같은 코드가 컴파일 오류를 일으킵니다:
```java
T[] array = new T[10]; // ❌ 오류
```

#### 🔹 이유:
- T는 컴파일 타임에만 존재하고, 런타임에는 실제 타입 정보가 없기 때문
- 배열은 런타임에 타입을 유지해야 하는 구조인데, 제네릭 타입은 런타임에 사라지므로 충돌이 발생합니다
✅ 우회 방법:
```java
@SuppressWarnings("unchecked")
T[] array = (T[]) new Object[10]; // 경고는 뜨지만 동작은 함
```

이 방식은 unchecked cast 경고를 발생시키며, 타입 안정성이 약해집니다.


## 🧠 요약: Java 제네릭의 퇴화는 의도된 설계

| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 타입 소거 (Type Erasure) | 컴파일 타임에만 타입 체크하고, 런타임에는 제네릭 정보가 제거되어 Object로 처리됨 |
| 하위 호환성 유지         | 기존 JVM과 비제네릭 코드와의 호환을 위해 제네릭 정보를 런타임에 유지하지 않음     |
| 배열 생성 불가           | 제네릭 타입은 런타임에 존재하지 않기 때문에 `new T[]` 같은 배열 생성이 불가능     |
| 타입 안전성              | 컴파일 타임에는 안전하지만, 런타임에는 캐스팅 오류가 발생할 수 있음               |
| 유연성 제한              | 런타임에 타입 정보를 활용한 동적 제네릭 처리나 리플렉션이 어려움                   |

---



