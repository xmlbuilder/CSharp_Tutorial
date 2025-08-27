
# 🧠 C#에서의 static 변수
## 🔹 핵심 개념
- static 변수는 클래스 전체에서 공유됨
- 인스턴스를 생성하지 않고도 접근 가능 (Foo.Counter)
- 인스턴스 변수는 각 객체마다 별도로 존재

## 예제 코드
```csharp
public class Foo
{
      public Foo()
      {
            Counter++;
            NonStaticCounter++;
      }
      public static int Counter { get; set; }
      public int NonStaticCounter { get; set; }
}

public static void Main(string[] args)
{
      var foo1 = new Foo();
      Console.WriteLine(foo1.NonStaticCounter);   //1
      Console.WriteLine(Foo.Counter);             //1
      var foo2 = new Foo();
      Console.WriteLine(foo2.NonStaticCounter);   //1
      Console.WriteLine(Foo.Counter);             //2
}
```

## 🔹 예제 동작 설명
```csharp
var foo1 = new Foo(); // Counter = 1, NonStaticCounter = 1
var foo2 = new Foo(); // Counter = 2, NonStaticCounter = 1
```

- Counter는 클래스 전체에서 공유되므로 누적됨
- NonStaticCounter는 각 인스턴스마다 1로 초기화됨

# 🧊 C++에서의 static 변수
## 🔹 핵심 개념
- static 멤버 변수는 클래스에 속하며 모든 객체가 공유
- 반드시 클래스 외부에서 정의해야 함
## 🔹 예시 코드
```cpp
class Foo {
public:
    static int Counter;
    int NonStaticCounter;

    Foo() {
        Counter++;
        NonStaticCounter++;
    }
};

int Foo::Counter = 0;

int main() {
    Foo foo1;
    std::cout << foo1.NonStaticCounter << std::endl; // 1
    std::cout << Foo::Counter << std::endl;          // 1

    Foo foo2;
    std::cout << foo2.NonStaticCounter << std::endl; // 1
    std::cout << Foo::Counter << std::endl;          // 2
}
```


# ☕ Java에서의 static 변수
## 🔹 핵심 개념
- static 변수는 클래스에 속하며 모든 인스턴스가 공유
- 인스턴스 변수는 객체마다 별도로 존재
## 🔹 예시 코드

```java
public class Foo {
    public static int counter = 0;
    public int nonStaticCounter = 0;

    public Foo() {
        counter++;
        nonStaticCounter++;
    }

    public static void main(String[] args) {
        Foo foo1 = new Foo();
        System.out.println(foo1.nonStaticCounter); // 1
        System.out.println(Foo.counter);           // 1

        Foo foo2 = new Foo();
        System.out.println(foo2.nonStaticCounter); // 1
        System.out.println(Foo.counter);           // 2
    }
}
```


# 🐍 Python에서의 static 변수
## 🔹 핵심 개념
- Python은 @staticmethod와 클래스 변수로 static 개념을 구현
- 클래스 변수는 모든 인스턴스가 공유
## 🔹 예시 코드
class Foo:
```python
    counter = 0  # class variable
    def __init__(self):
        Foo.counter += 1
        self.non_static_counter = 1

foo1 = Foo()
print(foo1.non_static_counter)  # 1
print(Foo.counter)              # 1

foo2 = Foo()
print(foo2.non_static_counter)  # 1
print(Foo.counter)              # 2
```


## 📊 언어별 static 변수 비교 (Markdown 표)
| 언어     | static 변수 선언 위치       | 접근 방식           | 인스턴스 없이 접근 가능 | 클래스 전체 공유 여부 |
|----------|-----------------------------|----------------------|--------------------------|------------------------|
| C#       | 클래스 내부 (`static`)       | `ClassName.Variable` | ✅                       | ✅                     |
| C++      | 클래스 내부 + 외부 정의 필요 | `ClassName::Variable`| ✅                       | ✅                     |
| Java     | 클래스 내부 (`static`)       | `ClassName.variable` | ✅                       | ✅                     |
| Python   | 클래스 내부 (`class var`)    | `ClassName.variable` | ✅                       | ✅                     |

---



