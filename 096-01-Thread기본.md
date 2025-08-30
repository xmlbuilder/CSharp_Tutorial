# 🧵 C#, C++, Java, Python의 기본 Thread 생성 방식 비교

C#에서 쓰레드를 만드는 기본적인 클래스로 System.Threading.Thread라는 클래스가 있다.\\    
이 클래스의 생성자(Constructor)에 실행하고자 하는 메서드를 델리게이트로 지정한 후, 
Thread클래스 객체에서 Start() 메서드를 호출하면 새로운 쓰레드가 생성되어 실행되게 된다.

| 언어     | 쓰레드 클래스 / 방식         | 실행 메서드 |
|----------|------------------------------|-------------|
| C#       | `System.Threading.Thread`    | `Start()`   |
| C++      | `std::thread`                | 생성 시 실행 |
| Java     | `Thread` / `Runnable`        | `start()`   |
| Python   | `threading.Thread`           | `start()`   |



🔧 각 언어별 코드 예시

✅ C# 코드
```csharp

namespace GrammarTest
{
    public class Program
    {
        void DoTest()
        {
            Thread t1 = new Thread(new ThreadStart(Run));
            t1.Start();
            Run();
        }
        
        void Run()
        {
            Console.WriteLine("Thread#{0} : Begin", Thread.CurrentThread.ManagedThreadId);
            Thread.Sleep(3000);
            Console.WriteLine("Thread#{0} : End", Thread.CurrentThread.ManagedThreadId);
        }

        public static void Main(String[] args)
        {
            new Program().DoTest();
            /*
            Thread#1 : Begin
            Thread#4 : Begin
            Thread#4 : End
            Thread#1 : End
            */
        }
    }
}


```


## ✅ C++ (C++11 이상)
```cpp
#include <iostream>
#include <thread>
#include <chrono>

void run() {
    std::cout << "Thread#" << std::this_thread::get_id() << " : Begin\n";
    std::this_thread::sleep_for(std::chrono::seconds(3));
    std::cout << "Thread#" << std::this_thread::get_id() << " : End\n";
}

int main() {
    std::thread t1(run);
    run(); // 메인 쓰레드에서 실행
    t1.join(); // t1 쓰레드가 끝날 때까지 대기
    return 0;
}
```


## ✅ Java
```java
public class Program {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> run());
        t1.start();
        run(); // 메인 쓰레드에서 실행
    }

    static void run() {
        System.out.println("Thread#" + Thread.currentThread().getId() + " : Begin");
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Thread#" + Thread.currentThread().getId() + " : End");
    }
}

```


## ✅ Python
```python
import threading
import time

def run():
    print(f"Thread#{threading.get_ident()} : Begin")
    time.sleep(3)
    print(f"Thread#{threading.get_ident()} : End")

def main():
    t1 = threading.Thread(target=run)
    t1.start()
    run()  # 메인 쓰레드에서 실행
    t1.join()

if __name__ == "__main__":
    main()
```    


## 🧠 요점 정리
- 모든 언어에서 함수를 쓰레드에 전달하고 start() 또는 join()으로 제어하는 방식은 유사함.
- C++과 Python은 함수 포인터 또는 람다를 직접 전달.
- Java는 익명 클래스 또는 람다식을 활용.
- C#은 델리게이트를 통해 메서드를 지정.
---



