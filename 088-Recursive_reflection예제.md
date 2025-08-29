# 🔍 C# 버전
 재귀적으로 타입을 탐색하는 방식이 객체 트리 구조를 시각적으로 이해하는 데 아주 유용

```csharp
namespace GrammarTest
{
    class Program
    {
        public class ChildTwo
        {
            public string ChildTwoName { get; set; }
        }

        public class ChildOne
        {
            public string ChildOneName { get; set; }
            public ChildTwo Child { get; set; }
        }

        public class Root
        {
            public string Name { get; set; }
            public ChildOne Child { get; set; }
        }
       
        public static void Main(String[] args)
        {
           DescribeTypeOfObject(typeof(Root));
           
           /*
            *
            Describing type Root
            Property Name (Name), Property Type Name (String)
            Property Name (Child), Property Type Name (ChildOne)
            Describing type ChildOne
            Property Name (ChildOneName), Property Type Name (String)
            Property Name (Child), Property Type Name (ChildTwo)
            Describing type ChildTwo
            Property Name (ChildTwoName), Property Type Name (String)
            */
 
        }

        static void DescribeTypeOfObject(Type type)
        {
            Console.WriteLine($"Describing type {type.Name}");
            PropertyInfo[] propertyInfos = type.GetProperties();
            foreach (var pi in propertyInfos)
            {
                Console.WriteLine($"Property Name ({pi.Name}), Property Type Name ({pi.PropertyType.Name})");
                if (pi.PropertyType.IsClass && !pi.PropertyType.FullName.StartsWith("System."))
                {
                    DescribeTypeOfObject(pi.PropertyType);
                }
                
            }
        }
    }
    
}
```


## ☕ Java 버전: 재귀적 리플렉션 탐색 예제
```java
import java.lang.reflect.*;
import java.util.*;

public class RecursiveReflection {

    public static class ChildTwo {
        public String childTwoName;
    }

    public static class ChildOne {
        public String childOneName;
        public ChildTwo child;
    }

    public static class Root {
        public String name;
        public ChildOne child;
    }

    public static void main(String[] args) {
        describeType(Root.class);
    }

    public static void describeType(Class<?> clazz) {
        System.out.println("Describing type " + clazz.getSimpleName());
        for (Field field : clazz.getDeclaredFields()) {
            System.out.println("Property Name (" + field.getName() + "), Property Type Name (" + field.getType().getSimpleName() + ")");
            if (isCustomClass(field.getType())) {
                describeType(field.getType());
            }
        }
    }

    private static boolean isCustomClass(Class<?> type) {
        return !type.isPrimitive() &&
               !type.getName().startsWith("java.") &&
               !type.isEnum();
    }
}
```


## 🔍 주요 차이점 요약
| 항목                     | C# (.NET)                                | Java                                      |
|--------------------------|-------------------------------------------|-------------------------------------------|
| 타입 객체                | `Type`                                    | `Class<?>`                                |
| 속성/필드 탐색           | `GetProperties()`                         | `getDeclaredFields()`                     |
| 시스템 타입 필터링       | `FullName.StartsWith("System.")`          | `type.getName().startsWith("java.")`      |
| 사용자 정의 클래스 판별 | `IsClass`                                 | `!isPrimitive() && !isEnum()`             |


## ✨ 활용 아이디어
이 구조는 단순한 타입 탐색을 넘어서 다음과 같은 용도로 확장할 수 있어요:
- ✅ 객체 직렬화/역직렬화: JSON/XML 변환 시 필드 자동 탐색
- ✅ UI 자동 생성: 객체 구조 기반으로 폼이나 입력창 자동 생성
- ✅ 디버깅 도구: 런타임에 객체 구조를 시각화
- ✅ 문서화 자동화: 클래스 구조를 Markdown이나 HTML로 출력

---

