# 🧩 C#의 열거형 (Enum)
## 기본 특징
- enum 키워드 사용
- 기본 타입은 int, 다른 정수형으로도 지정 가능 (byte, ushort, 등)
- [Flags] 특성을 사용하면 비트 필드로 조합 가능
## 예시
```csharp
enum Season { Spring, Summer, Autumn, Winter }

enum ErrorCode : ushort {
    None = 0,
    Unknown = 1,
    ConnectionLost = 100,
    OutlierReading = 200
}
```

## 비트 필드 조합
```cpp
[Flags]
enum MyEnum { FlagA = 1, FlagB = 2, FlagC = 4, FlagD = 8 }

var flags = MyEnum.FlagA | MyEnum.FlagB;
Console.WriteLine(flags); // 출력: FlagA, FlagB
```

## 사용 예
```csharp
Namespace GrammarTest
{
    class Program
    {
        [Flags]
        enum MyEnum
        {
            FlagA = 1,
            FlagB = 2,
            FlagC = 4,
            FlagD = 8
        }

        public static void Main(string[] args)
        {
            
            var twoFlags = MyEnum.FlagA | MyEnum.FlagB;
            Console.WriteLine(twoFlags); 
            //FlagA, FlagB value = 3

            
            Console.WriteLine((twoFlags & MyEnum.FlagA) == MyEnum.FlagA); 
            //True

            if(twoFlags.HasFlag(MyEnum.FlagA))
            {
                Console.WriteLine("Enum Has FlagA"); 
            }
            //Enum Has FlagA

            
            var type = typeof(MyEnum);
            Console.WriteLine(type.Name);
            //MyEnum

            var names = Enum.GetNames(type); //names: string[4]
            Console.WriteLine(names.Length);
            //4

            foreach(var name in names) //name : string
            {
                var item = (MyEnum)Enum.Parse(type,name);
                if(twoFlags.HasFlag(item))
                {
                    Console.WriteLine("Enum has " + name);
                }
                /*
                 Enum has FlagA
                 Enum has FlagB
                 */
            }
        }
    }
}

```



## ☕ Java의 열거형 (Enum)
### 기본 특징
- enum은 클래스처럼 동작하며, 메서드, 필드, 생성자를 가질 수 있음
- 각 열거형 값은 객체로 취급됨
- 비트 필드 조합은 직접 구현해야 하며, C#처럼 [Flags]는 없음
### 예시
```java
enum Season {
    SPRING, SUMMER, AUTUMN, WINTER
}
```

### 메서드 포함 가능
```java
enum ErrorCode {
    NONE(0), UNKNOWN(1), CONNECTION_LOST(100), OUTLIER_READING(200);

    private final int code;
    ErrorCode(int code) { this.code = code; }
    public int getCode() { return code; }
}
```

### 조합 방식 (비트 필드처럼 사용하려면)
```java
enum Permission {
    READ(1), WRITE(2), EXECUTE(4);

    private final int value;
    Permission(int value) { this.value = value; }
    public int getValue() { return value; }
}
```

비트 연산은 직접 구현해야 하며, EnumSet을 사용하는 것이 일반적입니다.


## 🧠 C++의 열거형 (Enum)
### 기본 특징
- enum 또는 enum class 사용
- 기본 타입은 int, 명시적으로 다른 타입 지정 가능
- enum class는 타입 안전을 제공
### 예시
```cpp
enum Season { Spring, Summer, Autumn, Winter };

enum ErrorCode : unsigned short {
    None = 0,
    Unknown = 1,
    ConnectionLost = 100,
    OutlierReading = 200
};
```

### enum class (C++11 이후)
```cpp
enum class Color { Red, Green, Blue };

Color c = Color::Red;
```

### 비트 필드 조합
```cpp
enum Permission {
    Read = 1,
    Write = 2,
    Execute = 4
};

int perms = Read | Write;
if (perms & Read) {
    std::cout << "Has Read permission\n";
}
```


## 📊 비교 요약
| 항목 | C# | Java | C++ |
|------|----|------|------| 
| 기본 타입 | int (변경 가능) | 객체 기반 | int (변경 가능) | 
| 비트 필드 지원 | [Flags] 특성으로 지원 | 직접 구현 필요 | 비트 연산으로 직접 구현 | 
| 타입 안전성 | 보통 열거형은 타입 안전 아님 | 열거형은 클래스처럼 안전함 | enum class로 타입 안전 | 
| 메서드 포함 여부 | 불가능 | 가능 | enum class는 불가능 | 
| 리플렉션 지원 | Enum.GetNames() 등 가능 | values() 메서드 제공 | 리플렉션은 직접 구현 필요 | 

---




