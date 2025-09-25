# 🧠 C#에서의 클래스 캐스팅
## 🔹 핵심 개념
- C#은 명시적 인터페이스 구현을 통해 동일한 메서드 이름을 가진 인터페이스를 구분할 수 있음
- 명시적으로 구현된 메서드는 인터페이스 타입으로 캐스팅해야 호출 가능
- MyClass는 IMyInterface1, IMyInterface2를 모두 구현하지만, obj.GetName()은 직접 호출 불가
## 🔹 캐스팅 예시
```csharp
IMyInterface1 myClass1 = (IMyInterface1)obj;
Console.WriteLine(myClass1.GetName()); // "IMyInterface1"
```


# 🧠 C++에서의 클래스 캐스팅
## 🔹 핵심 개념
- C++은 다중 상속을 통해 여러 인터페이스(추상 클래스)를 구현 가능
- 동일한 메서드 이름을 가진 인터페이스는 스코프 지정 연산자(::) 로 구분
- 캐스팅은 포인터 타입으로 명시적으로 수행
## 🔹 캐스팅 예시
```cpp
IMyInterface1* myInterface1 = (IMyInterface1*)myClass;
std::cout << myInterface1->GetName(); // "IMyInterface1"
```


# ☕ Java에서의 클래스 캐스팅
## 🔹 핵심 개념
- Java는 다중 인터페이스 구현을 지원하지만, 명시적 인터페이스 구현은 없음
- 동일한 메서드 이름을 가진 인터페이스를 구현하면 하나의 메서드로 통합됨
- 캐스팅은 인터페이스 타입으로 가능하지만, 메서드 충돌은 컴파일 에러
## 🔹 예시 코드
```java
interface IMyInterface1 {
    String getName();
}
interface IMyInterface2 {
    String getName();
}
class MyClass implements IMyInterface1, IMyInterface2 {
    public String getName() {
        return "Unified";
    }
}
public class Main {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        IMyInterface1 i1 = obj;
        IMyInterface2 i2 = obj;
        System.out.println(i1.getName()); // "Unified"
        System.out.println(i2.getName()); // "Unified"
    }
}

```

Java에서는 인터페이스 간 메서드 이름이 같으면 하나의 구현으로 공유됨


# 🐍 Python에서의 클래스 캐스팅
## 🔹 핵심 개념
- Python은 동적 타입 언어로 캐스팅 개념이 거의 없음
- 인터페이스는 ABC (Abstract Base Class)로 흉내낼 수 있음
- 동일한 이름의 메서드는 오버라이딩되며, 타입 캐스팅 없이 호출 가능
## 🔹 예시 코드
```python
class IMyInterface1:
    def get_name(self): pass

class IMyInterface2:
    def get_name(self): pass

class MyClass(IMyInterface1, IMyInterface2):
    def get_name_from_1(self):
        return "IMyInterface1"
    def get_name_from_2(self):
        return "IMyInterface2"

obj = MyClass()
print(obj.get_name_from_1())  # "IMyInterface1"
print(obj.get_name_from_2())  # "IMyInterface2"
```

Python은 명시적 인터페이스 구현이 없고, 메서드 이름 충돌은 개발자가 직접 관리해야 함


## 📊 언어별 클래스 캐스팅 비교 (Markdown 표)
| 언어     | 다중 인터페이스 구현 | 명시적 인터페이스 구현 | 캐스팅 방식           | 메서드 이름 충돌 처리 |
|----------|----------------------|-------------------------|------------------------|------------------------|
| C#       | 지원                 | 지원 (`InterfaceName.Method`) | 명시적 캐스팅 필요     | 명시적 구현으로 분리    |
| C++      | 지원 (다중 상속)     | 지원 (`Interface::Method`)   | 포인터 캐스팅          | 스코프 지정으로 구분    |
| Java     | 지원                 | 미지원                  | 인터페이스 타입 캐스팅 | 하나의 구현으로 통합    |
| Python   | 지원 (`ABC`)         | 미지원                  | 캐스팅 불필요          | 개발자가 직접 분리 구현 |

---




