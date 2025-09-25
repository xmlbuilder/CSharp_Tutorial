# Reflection 실전 예제
- Attribute를 통해 메서드에 데이터를 전달하고, Reflection으로 실행 흐름을 제어하는 방식은 실전에서도 테스트 자동화, 샘플 실행기, 플러그인 시스템 등에서 자주 쓰입니다. 


## C# 실전 예제
```csharp
//Attribute 로 데이터 전달 (method)

using System.Reflection;

namespace SampleRunner
{
    [AttributeUsage(AttributeTargets.Method)]
    public class DataAttribute : Attribute
    {
        private List<Object> _objects = new List<object>();
        
        public DataAttribute(params Object[] args)
        {
            foreach (var obj in args)
            {
                _objects.Add(obj);
            }
        }
        public List<Object> GetList()
        {
            return _objects;
        }
        
    }

    [AttributeUsage(AttributeTargets.Class)]
    public class SampleCodeAttribute : Attribute
    {
        public string Chapter { get; private set; }
        public SampleCodeAttribute(string chapter)
        {
            Chapter = chapter;
        }
            
    }
    
    // Attribute 사용중
    
    [SampleCode("Chapter1")]
    public class SampleCode1
    {
        public void TestCode1()
        {
            Console.WriteLine("Chapter1 - TestCode1");
        }   
        
        public void TestCode2()
        {
            Console.WriteLine("Chapter1 - TestCode2");
        }

        [Data(1, 2, 3, 4, 5, 6)]
        public void TestWidthData(List<Object> args)
        {
            Console.WriteLine("Started - TestWidthData");
            var lists = args.Select(s => (int) s);
            Console.WriteLine(lists.Count());
            //6
        }
    }
    
    class Program
    {
        //Reflection을 통하여 클래스 정보를 분석하여 구동하는 모듈
        public static void Run()
        {
            //기본 애플리케이션 도메인에 있는 프로세스 실행 파일을 가져옵니다. 이 실행 파일은 다른 애플리케이션 도메인에서
            var asm = Assembly.GetEntryAssembly();
            
            var typeSampleCodes = asm.GetTypes().OrderBy(x => x.Name);
            
            foreach (var type in typeSampleCodes)
            {
                var attr = type.GetCustomAttribute(typeof(SampleCodeAttribute)) as SampleCodeAttribute;
                if (attr == null) continue;
                Console.WriteLine($"{attr.Chapter}");
                //Chapter1
                
                var obj = Activator.CreateInstance(type);
                CallMethods(obj);
            }
        }
        public static void CallMethods(Object obj)
        {
            var type = obj.GetType();
            var methods = type.GetMethods(BindingFlags.Instance | BindingFlags.Public);
            foreach (var method in methods)
            {
                if (method.Name.StartsWith("Test"))
                {
                    var dataAttribute = method.GetCustomAttribute(typeof(DataAttribute)) as DataAttribute;
                    if (dataAttribute == null)
                    {
                        method.Invoke(obj, null);
                    }
                    else
                    {
                        List<Object> datas = dataAttribute.GetList();
                        method.Invoke(obj, new object[] {datas});
                    }
                }
            }
        }
        
        static void Main(string[] args)
        {
            Run();
        }
    }
}


```

## ☕ Java 버전: Annotation + Reflection
Java에서는 C#의 Attribute에 해당하는 것이 @Annotation입니다. 아래는 유사한 구조의 예제입니다:
### ✅ Java 코드 예제
```java
import java.lang.annotation.*;
import java.lang.reflect.*;
import java.util.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Data {
    int[] value();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface SampleCode {
    String chapter();
}

@SampleCode(chapter = "Chapter1")
class SampleCode1 {
    public void testCode1() {
        System.out.println("Chapter1 - testCode1");
    }

    public void testCode2() {
        System.out.println("Chapter1 - testCode2");
    }

    @Data({1, 2, 3, 4, 5, 6})
    public void testWithData(List<Integer> args) {
        System.out.println("Started - testWithData");
        System.out.println(args.size()); // 6
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        for (Class<?> clazz : List.of(SampleCode1.class)) {
            SampleCode sampleCode = clazz.getAnnotation(SampleCode.class);
            if (sampleCode == null) continue;

            System.out.println(sampleCode.chapter());
            Object obj = clazz.getDeclaredConstructor().newInstance();

            for (Method method : clazz.getDeclaredMethods()) {
                if (method.getName().startsWith("test")) {
                    Data data = method.getAnnotation(Data.class);
                    if (data == null) {
                        method.invoke(obj);
                    } else {
                        List<Integer> list = Arrays.stream(data.value()).boxed().toList();
                        method.invoke(obj, list);
                    }
                }
            }
        }
    }
}

```

Java에서는 Annotation을 RetentionPolicy.RUNTIME으로 설정해야 Reflection으로 접근할 수 있어요.


## 🐍 Python 버전: Decorator + Reflection
Python은 @decorator를 통해 메타데이터를 붙이고, inspect 모듈로 Reflection을 수행할 수 있어요.
### ✅ Python 코드 예제
```python
import inspect

def data(*args):
    def wrapper(func):
        func._data = list(args)
        return func
    return wrapper

def sample_code(chapter):
    def wrapper(cls):
        cls._chapter = chapter
        return cls
    return wrapper

@sample_code("Chapter1")
class SampleCode1:
    def test_code1(self):
        print("Chapter1 - test_code1")

    def test_code2(self):
        print("Chapter1 - test_code2")

    @data(1, 2, 3, 4, 5, 6)
    def test_with_data(self, args):
        print("Started - test_with_data")
        print(len(args))  # 6

def run():
    for cls in [SampleCode1]:
        chapter = getattr(cls, "_chapter", None)
        if chapter:
            print(chapter)
            obj = cls()
            for name, method in inspect.getmembers(obj, predicate=inspect.ismethod):
                if name.startswith("test"):
                    data_args = getattr(method, "_data", None)
                    if data_args is None:
                        method()
                    else:
                        method(data_args)

run()

```
Python은 클래스나 함수에 속성을 자유롭게 추가할 수 있어서, 데코레이터로 메타데이터를 붙이는 방식이 매우 자연스럽습니다.



## 🧭 Reflection + 메타데이터 실행 방식 비교

| 언어     | 메타데이터 표현 방식     | 실행 방식                  | 특징 요약                          |
|----------|--------------------------|-----------------------------|------------------------------------|
| **C#**   | `Attribute`              | `MethodInfo.Invoke()`       | 강력한 타입 시스템, 정적 구조       |
| **Java** | `@Annotation`            | `Method.invoke()`           | 런타임 접근 제한, 타입 안정성       |
| **Python** | `@decorator` + 속성       | `getattr()`, `inspect`       | 유연하고 동적, 속성·함수 자유롭게 조작 |



---

