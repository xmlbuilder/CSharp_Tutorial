# 📦 Boxing & Unboxing in C#
## 🔹 개념 설명
| 개념            | 설명                                                                 |
|-----------------|----------------------------------------------------------------------|
| Boxing          | 값 형식(예: int, struct)을 object 타입으로 변환하여 힙에 저장하는 과정 |
| Unboxing        | object로 박싱된 값을 다시 원래의 값 형식으로 캐스팅하는 과정           |
| 대상 타입       | int, float, bool, struct 등 모든 값 형식                              |
| 저장 위치       | Boxing 시 힙에 저장됨 (참조 형식처럼 동작)                            |
| 성능 영향       | Boxing/Unboxing은 오버헤드가 발생하므로 반복 사용 시 성능 저하 가능     |
| 캐스팅 필요 여부| Boxing은 암시적, Unboxing은 명시적 캐스팅 필요                        |
| 예시 코드       | object obj = 3; int num = (int)obj;                                   |

## 🔹 코드 예시
```csharp
int num = 3;
object obj = num;         // Boxing
int result = (int)obj;    // Unboxing

DATA Test = new DATA("test", 3);
object obj1 = Test;       // Boxing (struct → object)
DATA test1 = (DATA)obj1;  // Unboxing
```

- int와 DATA는 값 형식이므로 object로 변환 시 Boxing 발생
- 다시 원래 타입으로 캐스팅할 때 Unboxing 발생

## ☕ Java와의 비교
Java에서도 유사한 개념이 존재하지만, **자동 박싱(Auto-boxing)**과 래퍼 클래스를 통해 처리됩니다.
### 🔸 Java의 방식
| 개념              | 설명                                                                 |
|-------------------|----------------------------------------------------------------------|
| Primitive Type     | `int`, `double`, `boolean` 등 값 형식으로 스택에 저장됨               |
| Wrapper Class      | `Integer`, `Double`, `Boolean` 등 참조 형식으로 힙에 저장됨           |
| Auto-boxing        | 기본형 → 래퍼 클래스로 자동 변환 (`int → Integer`)                   |
| Unboxing           | 래퍼 클래스 → 기본형으로 자동 변환 (`Integer → int`)                 |
| 구조체 지원 여부   | 구조체(`struct`)는 없음. 모든 사용자 정의 타입은 클래스 기반 참조 형식 |
| 캐스팅 필요 여부   | Boxing/Unboxing 모두 자동으로 처리됨                                 |
| 성능 영향          | 자주 사용 시 오토박싱으로 인한 힙 할당 및 GC 부담 발생 가능            |
| 예시 코드          | `Integer boxed = 3; int num = boxed;`                                |


### 🔸 Java 예시
```java
int num = 3;
Integer boxed = num;        // Auto-boxing
int result = boxed;         // Unboxing

// 구조체는 없지만, 클래스 사용 가능
class Data {
    String name;
    int number;
    Data(String name, int number) {
        this.name = name;
        this.number = number;
    }
}
```

- Java는 struct가 없고 모든 사용자 정의 타입은 클래스 기반 참조 형식
- Boxing/Unboxing은 기본형 ↔ 래퍼 클래스 간의 변환에만 해당

## 🧠 C# vs Java 비교 요약
| 항목             | C#                                      | Java                                      |
|------------------|------------------------------------------|-------------------------------------------|
| 값 형식          | `int`, `struct`, `bool` 등               | `int`, `double`, `boolean` 등             |
| 참조 형식        | `object`, `class`                        | `Integer`, `Double`, `Boolean`, `class`   |
| Boxing 방식      | 명시적 (`object obj = value`)            | 자동 (`Integer boxed = value`)            |
| Unboxing 방식    | 명시적 캐스팅 (`(int)obj`)               | 자동 (`int value = boxed`)                |
| 구조체 지원      | `struct` 존재                            | 없음 (모든 사용자 정의 타입은 클래스)     |


## 전체 코드

```csharp
namespace TestCode
{
    struct DATA
    {
        public string name;
        public int number;
        public DATA(string name, int number)
        {
            this.name = name;
            this.number = number;
        }
    }
 
    class Program
    {
        static void Main(string[] args)
        {
            int num = 3;
            object obj = num;
            int result = (int)obj;
            DATA Test = new DATA("test", 3);

            object obj1 = Test; 
            DATA test1 = (DATA)obj1;

            Console.WriteLine("{0} {1}", test1.name, test1.number);
        }
    }
}
```


## 🧪 출력 결과
test 3


- DATA 구조체가 object로 Boxing된 후 다시 Unboxing되어 출력됨
- 구조체의 값이 유지된 채로 복원됨
