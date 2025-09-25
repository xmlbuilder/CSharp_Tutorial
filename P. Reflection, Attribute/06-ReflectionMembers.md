# Reflection GetMembers
C#의 Reflection 기능 중 GetMembers()를 활용해서 System.Object 타입의 모든 멤버(필드, 속성, 메서드 등)를 탐색하는 예제. 

## 🧠 MemberInfo와 GetMembers()란?
### 🔹 개념
- MemberInfo는 C#에서 클래스의 **모든 구성 요소(멤버)**를 표현하는 추상 클래스예요.
- GetMembers()는 해당 타입의 모든 멤버 정보를 배열로 반환합니다.
- 멤버에는 다음이 포함됩니다:
- MethodInfo: 메서드
- PropertyInfo: 속성
- FieldInfo: 필드
- EventInfo: 이벤트
- ConstructorInfo: 생성자


## 🔹 언제 쓰이나요?

| 사용 사례             | 설명                                                                 |
|----------------------|----------------------------------------------------------------------|
| 직렬화/역직렬화       | 객체의 속성 목록을 자동으로 탐색해 JSON, XML 등으로 변환하거나 복원할 때 사용됨 |
| ORM 매핑             | 클래스의 필드를 데이터베이스 컬럼과 자동으로 매핑할 때 활용됨               |
| 테스트 프레임워크     | 테스트 대상 클래스의 메서드를 자동으로 탐색하고 실행할 때 사용됨             |
| 플러그인 시스템       | 런타임에 로드된 타입의 기능을 동적으로 탐색하고 실행할 때 사용됨             |
| 디버깅/로깅 도구      | 객체의 상태를 자동으로 출력하거나 기록할 때 사용됨                          |
| 코드 자동 생성        | 클래스 구조를 기반으로 UI, API, 문서 등을 자동 생성할 때 활용됨              |


## C#
```csharp
using System.Reflection;

namespace GrammarTest
{
    class Program
    {
        public static void Main(String[] args)
        {
            var members = typeof(Object).GetMembers(BindingFlags.Public |
                                                    BindingFlags.Static | BindingFlags.Instance);
            foreach (var member in members)
            {
                try
                {
                    Console.WriteLine($"Name = {member.Name}, MemberType = {member.MemberType}, DeclaringType = {member.DeclaringType}");
                    
                }catch(Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }
        }
    }
}

```

## ☕ Java에서의 대응 방식
Java에서는 java.lang.reflect 패키지를 통해 유사한 기능을 제공합니다.
### ✅ 예제
```java
import java.lang.reflect.*;

public class MemberReflectionJava {
    public static void main(String[] args) {
        Class<?> clazz = Object.class;

        Member[] members = clazz.getDeclaredFields(); // 필드만
        Method[] methods = clazz.getDeclaredMethods(); // 메서드만
        Constructor<?>[] constructors = clazz.getDeclaredConstructors(); // 생성자만

        for (Method method : methods) {
            System.out.println("Name = " + method.getName() +
                               ", ReturnType = " + method.getReturnType() +
                               ", DeclaringClass = " + method.getDeclaringClass());
        }
    }
}
```

Java에서는 Member라는 공통 인터페이스가 있지만, 실제로는 Method, Field, Constructor 등으로 분리되어 있어요.


## 🐍 Python에서의 대응 방식
Python은 inspect 모듈이나 dir() 함수를 통해 객체의 멤버를 탐색할 수 있어요.
### ✅ 예제
```python
import inspect

members = inspect.getmembers(object)

for name, member in members:
    print(f"Name = {name}, Type = {type(member)}")

```

inspect.getmembers()는 모든 속성과 메서드를 튜플 형태로 반환합니다.
Python은 동적 언어라서 getattr(), hasattr() 등을 통해 멤버를 직접 조작하기도 쉬워요.



## 🧭 Reflection 멤버 탐색 방식 비교

| 언어     | 멤버 탐색 방식                     | 반환 타입                      | 특징 요약                         |
|----------|------------------------------------|--------------------------------|-----------------------------------|
| **C#**   | `Type.GetMembers()`                | `MemberInfo[]`                 | 모든 멤버를 통합적으로 탐색 가능     |
| **Java** | `Class.getDeclaredMethods()` 등    | `Method[]`, `Field[]` 등       | 멤버 타입별로 분리되어 있음         |
| **Python** | `inspect.getmembers()`, `dir()`   | `List[Tuple[str, Any]]`        | 매우 유연하고 동적, 속성·메서드 혼합 |

---


