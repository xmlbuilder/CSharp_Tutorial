# 제네릭 타입의 정의와 인스턴스화
C# 코드는 제네릭 타입의 정의와 인스턴스화 여부를 Reflection으로 판별하고, Dictionary<string, string>의 제네릭 인자 타입을 추출하는 예제.

## C#
```csharp
namespace GrammarTest
{
    public class Program
    {
        public class MyClass
        {
            public MyClass(int i, int j, string str)
            {
                Console.WriteLine($"{i}, {j} : {str}");
            }
        }
        
        public static void Main(String[] args)
        {
            Type type = typeof(List<>);
            Console.WriteLine(type.IsGenericType);
            //True
            Console.WriteLine(type.IsGenericTypeDefinition);
            //True
            Console.WriteLine(type.IsConstructedGenericType);
            //False

            Type type1 = typeof(List<int>);
            Console.WriteLine(type1.IsGenericType);
            //False
            Console.WriteLine(type1.IsGenericTypeDefinition);
            //True
            Console.WriteLine(type1.IsConstructedGenericType);
            //True

            Type type2 = typeof(Dictionary<string, string>);
            foreach (var typeArgument in type2.GetGenericArguments())
            {
                Console.WriteLine(typeArgument.Name);
            }
            /*
            String
            String
             */
        }
    }
}
```


## ☕ Java: Reflection으로 제네릭 타입 정보 추출
Java는 타입 소거(Type Erasure) 때문에 런타임에 제네릭 타입 정보를 완전히 유지하지는 않지만, 클래스 선언부나 필드, 메서드의 선언에서는 일부 정보를 추출할 수 있음.
### ✅ 예제: 제네릭 타입 인자 추출
```java

import java.lang.reflect.*;
import java.util.*;

public class ReflectionGenericJava {
    public static void main(String[] args) throws Exception {
        // Dictionary<String, String>에 해당하는 Map<String, String>
        Field field = Sample.class.getDeclaredField("map");
        Type genericType = field.getGenericType();

        if (genericType instanceof ParameterizedType) {
            ParameterizedType pt = (ParameterizedType) genericType;
            for (Type arg : pt.getActualTypeArguments()) {
                System.out.println(arg.getTypeName()); // java.lang.String
            }
        }
    }

    static class Sample {
        Map<String, String> map = new HashMap<>();
    }
}
```

## 🔍 주요 포인트
- getGenericType() → 제네릭 타입 정보 추출
- ParameterizedType → 실제 타입 인자 확인 가능
- getActualTypeArguments() → String, Integer 등 타입 인자 확인
객체 자체에서는 제네릭 정보를 알 수 없고, 필드나 메서드 선언부에서만 확인 가능.


## 🐍 Python: 제네릭 타입 정보 추출
Python은 동적 타입 언어라서 제네릭 타입을 런타임에 자유롭게 다룰 수 있어요. 특히 typing 모듈과 __orig_class__, __annotations__ 등을 활용하면 유사한 기능을 구현할 수 있습니다.
###  ✅ 예제: 제네릭 타입 인자 확인
```python
from typing import Generic, TypeVar, Dict

T = TypeVar('T')
U = TypeVar('U')

class MyDict(Generic[T, U]):
    def __init__(self):
        self.data: Dict[T, U] = {}


# 인스턴스 생성
instance = MyDict[str, int]()

# 타입 힌트 확인
print(instance.__class__.__orig_bases__)  # (Generic[str, int],)
```

### 🔍 주요 포인트
- Generic[T, U] → 제네릭 타입 정의
- __orig_bases__ → 실제 타입 인자 확인
- __annotations__ → 필드의 타입 힌트 확인 가능
Python은 런타임에 타입 정보를 유지하지 않지만, 정적 분석이나 타입 힌트 기반으로 추론할 수 있어요.



## 🧭 Reflection + Generic 타입 인자 추출 비교

| 언어     | 제네릭 타입 정의 예시         | 타입 인자 추출 방식                          | 런타임 정보 유지 수준 |
|----------|-------------------------------|----------------------------------------------|------------------------|
| **C#**   | `List<>`, `Dictionary<,>`     | `GetGenericArguments()`                      | ✅ 완전 유지            |
| **Java** | `List<T>`, `Map<K,V>`         | `ParameterizedType.getActualTypeArguments()` | ⚠️ 제한적 (타입 소거)  |
| **Python** | `Generic[T]`, `Dict[T,U]`     | `__orig_bases__`, `__annotations__`           | ✅ 힌트 기반 유지       |

---
