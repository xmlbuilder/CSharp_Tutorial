# 🔒 Thread Lock 비교: C#, C++, Java, Python
모든 언어에서 락은 공유 자원에 대한 동시 접근을 제어하기 위한 핵심 도구입니다.

## 언어 비교
| 언어     | 동기화 방식            | 설명                                                                 |
|----------|------------------------|----------------------------------------------------------------------|
| C#       | `lock(obj)`            | `Monitor.Enter/Exit`의 축약형. 블록 내에서 단일 스레드만 접근 가능     |
| C++      | `std::mutex` + `lock_guard` | C++11부터 지원. RAII 방식으로 안전하게 락을 관리                     |
| Java     | `synchronized`         | 메서드 또는 블록 단위로 락을 걸어 하나의 스레드만 접근 가능           |
| Python   | `threading.Lock()`     | `with` 문과 함께 사용하여 락 획득/해제를 자동 처리                    |


## 💡 각 언어별 예시 코드

### ✅ C# (lock)
private static object _lockObj = new object();

private static void TaskWork()
{
    lock (_lockObj)
    {
        Console.WriteLine("Entered");
        Task.Delay(1000);
        Console.WriteLine("Done Delaying");
        Console.WriteLine("Leaving");
    }
}

### 전체 소스
```csharp
namespace GrammarTest
{
    class Program
    {
        private static object _lockObj = new object();
        
        public static void Main(String[] args)
        {
            Console.WriteLine("Message");
            Task task1 = Task.Run(() => TaskWork());
            Task task2 =Task.Run(() => TaskWork());
            Task task3 = Task.Run(() => TaskWork());
            Task.WaitAll(task1, task2, task3);
        }
        
        private static void TaskWork()
        {
            lock (_lockObj)
            {
                Console.WriteLine("Entered");
                Task.Delay(1000);
                Console.WriteLine("Done Delaying");
                Console.WriteLine("Leaving");
            }
        }
    }
   
}

```
### 출력 결과
```
Message
Entered
Done Delaying
Leaving
Entered
Done Delaying
Leaving
Entered
Done Delaying
Leaving

```


### ✅ C++ (std::mutex)
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::mutex mtx;

void TaskWork() {
    std::lock_guard<std::mutex> lock(mtx);
    std::cout << "Entered\n";
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::cout << "Done Delaying\n";
    std::cout << "Leaving\n";
}
```

### ✅ Java (synchronized)
```java
public class Example {
    private static final Object lockObj = new Object();

    public static void taskWork() {
        synchronized (lockObj) {
            System.out.println("Entered");
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
            System.out.println("Done Delaying");
            System.out.println("Leaving");
        }
    }
}
```

### ✅ Python (threading.Lock)
```python
import threading
import time

lock = threading.Lock()

def task_work():
    with lock:
        print("Entered")
        time.sleep(1)
        print("Done Delaying")
        print("Leaving")

```

## 🧠 참고 포인트
- C++은 std::lock_guard를 통해 예외 안전성을 확보하고, Python은 with 문으로 자동 해제.
- Java의 synchronized는 JVM 레벨에서 최적화된 락을 제공하며, C#의 lock은 Monitor 기반입니다.

---
