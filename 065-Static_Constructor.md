# ☕ C# / Java의 Static Initializer 특징

정적 생성자는 정적 데이터를 초기화하거나 한 번만 수행해야 하는 특정 작업을 수행하는 데 사용됩니다. 
첫 번째 인스턴스가 만들어지거나 정적 멤버가 참조되기 전에 자동으로 호출됩니다. 
정적 생성자는 한 번만 호출됩니다.


## C# / Java 비교

| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 명칭                     | Static Initializer Block (`static { ... }`)                          |
| 호출 시점               | 클래스가 처음 로딩될 때 자동 실행                                   |
| 호출 횟수               | 단 한 번만 실행됨                                                   |
| 명시적 호출 여부        | 직접 호출 불가                                                      |
| 위치 제한               | 클래스 내 어디든 가능 (보통 필드 선언 아래에 위치)                  |
| 주 용도                 | static 필드 초기화, 로깅, 리소스 준비 등                            |
| 접근 제한자 사용 여부   | 사용 불가 (`public`, `private` 등 붙일 수 없음)                     |
| 예외 처리               | 예외 발생 시 `ExceptionInInitializerError`로 래핑되어 던져짐         |
| 메서드와의 차이점       | 메서드가 아닌 블록이므로 이름이 없고 호출 불가                      |


## 샘플 코드
```csharp
class Animal
{
      static Animal()
      {
            Console.WriteLine("Animal initialized");
      }

      public Animal()
      {
            Console.WriteLine("Animal Created");
      }

      public static void Yawn()
      {
            Console.WriteLine($"{nameof(Yawn)}!");
      }
}

public class SessionManager
{
      public static SessionManager Instance;
      static SessionManager()
      {
            Instance = new SessionManager();
      }
}
      
public static void Main(string[] args)
{
      var tutle = new Animal();
      var giraffed = new Animal();
      Animal.Yawn();

      //Output
      //Animal initialized
      //Animal Created
      //Animal Created
      //Yawn!
}
```


## 🧱 C++에서 비슷한 기능을 흉내내는 방법들
### 1. 정적 변수 + 정적 초기화 함수
```cpp
class SessionManager {
public:
    static SessionManager& Instance() {
        static SessionManager instance; // 최초 접근 시 한 번만 생성됨
        return instance;
    }

private:
    SessionManager() {
        std::cout << "SessionManager Initialized" << std::endl;
    }
};
```

- static local variable은 C++11 이후 thread-safe하게 초기화됨
- Instance()를 호출하면 내부적으로 한 번만 생성됨 → 정적 생성자처럼 동작

### 2. 정적 객체 + 전역 초기화
```cpp
class Animal {
public:
    Animal() {
        std::cout << "Animal Initialized" << std::endl;
    }
};

static Animal staticAnimal; // 프로그램 시작 시점에 초기화됨
```


- 전역 또는 정적 객체는 프로그램 시작 시점에 초기화됨
- 단점: 초기화 순서가 불확실할 수 있음 (특히 여러 translation unit에서)

### 3. 정적 초기화 클래스 패턴
```cpp
class Initializer {
public:
    Initializer() {
        std::cout << "Static-like Initialization" << std::endl;
        // 여기에 static 변수 초기화 로직 작성
    }
};

static Initializer init; // 프로그램 시작 시점에 실행됨
```

### 샘플 코드
```cpp
#include <iostream>
using namespace std;

class Cubic {
public:
    static int s_count;
    // Constructor definition
    Cubic (int l, int w, int h)
    {
        cout <<"Cubic ctor called." << endl;
        length = l;
        width  = w;
        height = h;
        // Increase every time object is created
        s_count++;
    }
    int vol ()
    {
        return length * width * height;
    }
    static void initCount()
    {
        s_count = 0;
    }
    static int getCount()
    {
        return s_count;
    }
private:
    double length;     // Length of a box
    double width;      // Width  of a box
    double height;     // Height of a box
};

// Initialize static member of class Cubic
int Cubic::s_count = 0;  // Initializing here is non-deterministic

int main(void) {
    Cubic::initCount();  // Initializing here is deterministic

    // Print total number of objects before creating object.
    cout << "Initial Count: " << Cubic::getCount() << endl;
    Cubic cubic1(3, 1, 1);    // Declare box1
    Cubic cubic2(8, 6, 2);    // Declare box2
    // Print total number of objects after creating object.
    cout << "Final Count: " << Cubic::getCount() << endl;
    return 0;
}

//Output:
//Initial Count: 0
//Cubic ctor called.
//Cubic ctor called.
//Final Count: 2

```


- 이 방식은 C#의 static constructor처럼 자동 실행되지만
- 명확한 제어가 어렵고, 초기화 순서 문제가 생길 수 있음

## ✨ 결론
C++에는 static constructor가 없지만, 아래 방식으로 유사한 효과를 낼 수 있어요:
- static local variable in function (C++11 이후 추천)
- static object with constructor
- singleton pattern with lazy initialization
