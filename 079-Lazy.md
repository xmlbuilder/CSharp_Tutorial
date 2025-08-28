# Lazy Initialization

Lazy Initialization은 단순한 최적화 기법을 넘어서, 멀티스레드 환경에서 안전한 Singleton 패턴 구현의 핵심이죠.
C#, C++, Java, Python 각각의 언어에서 이 패턴을 어떻게 다루는지 비교해보면, 언어의 철학과 런타임 특성이 그대로 드러납니다.

## 🧠 Lazy Initialization & Thread-Safe Singleton 비교
| 언어     | Lazy 초기화 방식      | Thread-Safe Singleton 구현 방식                    | 특징 요약                                      |
|----------|--------------------------------------------|--------------------------------------------------|------------------------------------------------|
| **C#**   | `Lazy<T>` 클래스 사용                      | `Lazy<T>`는 기본적으로 thread-safe                | .NET에서 가장 깔끔하고 안전한 방식             |
| **Java** | `synchronized`, `volatile`, 또는 `Holder` 패턴 | `synchronized` 또는 정적 내부 클래스 방식으로 구현      | JVM의 메모리 모델을 고려한 설계 필요      |
| **C++**  | C++11 이후 `static` 지역 변수 초기화 보장   | `static` 지역 변수는 thread-safe (C++11 이상)    | 복잡한 경우 `std::call_once`도 사용 가능       |
| **Python** | `__new__` 오버라이딩 또는 모듈 단위 싱글톤 | GIL 덕분에 대부분의 경우 thread-safe         | GIL이 있지만 멀티프로세싱 환경에선 주의 필요   |



## 🔧 각 언어별 예시 코드
### ✅ C#
```csharp
private static readonly Lazy<LazySingleton> _instance = new Lazy<LazySingleton>(() => new LazySingleton());
```

- Lazy<T>는 기본적으로 LazyThreadSafetyMode.ExecutionAndPublication을 사용
- 생성자 접근을 막고, Instance 속성으로 접근

## 소스
```csharp

namespace GrammarTest
{
    public class LazySingleton
    {
        private static readonly Lazy<LazySingleton> _instance = new Lazy<LazySingleton>(() => new LazySingleton());

        public static LazySingleton Instance
        {
            get { return _instance.Value; }
        }

        private LazySingleton(){}
    }
    
    class Program
    {
        public static void Main(String[] args)
        {
            var instance = LazySingleton.Instance;
        }
    }
}

```

### ☕ Java
```java
public class LazySingleton {
    private LazySingleton() {}

    private static class Holder {
        private static final LazySingleton INSTANCE = new LazySingleton();
    }

    public static LazySingleton getInstance() {
        return Holder.INSTANCE;
    }
}
```


- 정적 내부 클래스 방식은 JVM이 클래스 로딩 시점에 thread-safe하게 초기화해줌
- synchronized 없이도 안전

### 🧊 C++ (C++11 이상)
```cpp
class LazySingleton {
public:
    static LazySingleton& getInstance() {
        static LazySingleton instance; // thread-safe in C++11+
        return instance;
    }

private:
    LazySingleton() {}
    LazySingleton(const LazySingleton&) = delete;
    LazySingleton& operator=(const LazySingleton&) = delete;
};
```

### 🔐 Singleton에서 초기화 vs. 상태 변경의 핵심 구분 (C++ 중심)

| 구분           | 설명                                                          | Thread-Safe 여부         | 예시 코드 또는 처리 방식                       |
|----------------|------------------------------------------------------------|---------------------------|-------------------------------------------|
| **초기화**     | Singleton 객체를 최초로 생성하는 과정 (`static` 지역 변수 등)    | ✅ C++11 이상에서 보장됨   | `static Singleton instance;`                |
| **상태 변경** | Singleton 객체 내부의 멤버 값을 변경하는 행위 (`setValue()` 등)  | ❌ 직접 동기화 필요        | `std::mutex`, `std::atomic`, `lock_guard` 등 사용 |



#### ✅ 예시: 초기화는 안전하지만…
```cpp
class Singleton {
public:
    static Singleton& getInstance() {
        static Singleton instance; // 초기화는 thread-safe
        return instance;
    }

    void setValue(int v) { value = v; } // 상태 변경은 thread-unsafe
    int getValue() const { return value; }

private:
    Singleton() {}
    int value = 0;
};
```

이 코드에서 getInstance()는 여러 스레드가 동시에 호출해도 안전하게 instance를 초기화해줍니다.
하지만 setValue()나 getValue()는 동기화 없이 호출하면 race condition이 발생할 수 있어요.

#### 🔒 해결 방법: std::mutex 사용
```cpp
class Singleton {
public:
    static Singleton& getInstance() {
        static Singleton instance;
        return instance;
    }

    void setValue(int v) {
        std::lock_guard<std::mutex> lock(mtx);
        value = v;
    }

    int getValue() const {
        std::lock_guard<std::mutex> lock(mtx);
        return value;
    }

private:
    Singleton() {}
    mutable std::mutex mtx;
    int value = 0;
};
```

이렇게 하면 상태 변경도 thread-safe하게 보호됩니다.

#### 🧠 요약
- C++11 이후 static 지역 변수 초기화는 thread-safe
- 하지만 Singleton 객체의 내부 상태 변경은 개발자가 직접 보호해야 함
- std::mutex, std::atomic, std::shared_mutex 등으로 동기화 필요



- static 지역 변수는 C++11부터 thread-safe 보장
- 더 복잡한 초기화가 필요하면 std::call_once 사용 가능

### 🐍 Python
```python
class LazySingleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super(LazySingleton, cls).__new__(cls)
        return cls._instance

```

- GIL(Global Interpreter Lock) 덕분에 대부분의 경우 thread-safe
- 하지만 멀티프로세싱 환경에서는 별도 보호 필요

### 🧭 요약
- C#은 Lazy<T> 덕분에 가장 직관적이고 안전한 방식 제공
- Java는 JVM의 메모리 모델을 고려한 설계가 필요
- C은 언어 자체의 진화(C11 이후)로 thread-safe 초기화가 가능
- Python은 GIL에 의존하지만, 병렬 환경에서는 주의 필요


### 🔐 Singleton에서 초기화 vs. 상태 변경의 핵심 구분 (C++ 중심)

| 구분           | 설명                                                             | Thread-Safe 여부         | 예시 코드 또는 처리 방식                       |
|----------------|--------------------------------------------------------------|---------------------------|------------------------------------------------|
| **초기화**     | Singleton 객체를 최초로 생성하는 과정 (`static` 지역 변수 등)       | ✅ C++11 이상에서 보장됨   | `static Singleton instance;                  |
| **상태 변경** | Singleton 객체 내부의 멤버 값을 변경하는 행위 (`setValue()` 등)     | ❌ 직접 동기화 필요      | `std::mutex`, `std::atomic`, `lock_guard` 등 사용 |

---


## 🔍 각 언어의 철학적 차이
### ☕ Java
- 동시성 철학의 기초를 닦은 언어
- Thread와 synchronized로 시작해, ExecutorService, ForkJoinPool로 발전
- AtomicInteger, ConcurrentHashMap 등 병렬 자료구조도 풍부
- JVM의 메모리 모델이 잘 정의되어 있어, 동기화의 의미가 명확함

### 🧊 C#
- 비동기 프로그래밍의 대중화 선도
- async/await로 병렬 처리의 진입장벽을 낮춤
- Task 기반으로 스레드 풀을 자동 관리
- ConcurrentDictionary, BlockingCollection 등 고수준 병렬 도구 제공

### 🔧 C++
- 강력하지만 위험한 자유
- C++11 이후 std::thread, std::mutex 등 표준화
- std::atomic으로 lock-free 프로그래밍 가능
- 하지만 스레드 안전성은 개발자 책임이며, 실수 여지가 많음

### 🦀 Rust
- 동시성과 안전성을 동시에 추구하는 언어
- Send, Sync trait으로 컴파일 타임에 스레드 안전성 검증
- Arc<Mutex<T>> 같은 구조로 공유 + 가변성 제어
- tokio, async/.await로 고성능 비동기 처리 가능
- 철학적으로 “안전하지 않으면 컴파일되지 않는다”는 접근

### 💡 비교
- Java는 동시성 개념을 정립한 언어
- C#은 그걸 현대적으로 추상화한 언어
- C++은 제어권을 주되 책임도 요구하는 언어
- Rust는 그 모든 걸 철학적으로 재정의한 언어
