
# 🧵 Thread Join의 의미
정의
join()은 **호출한 스레드(보통 메인 스레드)**가 대상 스레드의 실행이 끝날 때까지 대기하도록 만드는 메서드입니다.
즉, "너 끝날 때까지 나는 기다릴게"라는 동기화(synchronization) 동작입니다.

특징
- 대상 스레드가 종료될 때까지 블로킹
- 여러 스레드의 실행 순서를 제어할 수 있음
- 리소스 정리나 후속 작업 전에 안전하게 스레드 종료를 보장

## 소스
```csharp
/*
제공된 모든 Task 개체의 실행이 완료되기를 기다립니다.
Task클래스 사용을 위해 흔히 사용되는 방법은 Task.Factory.StartNew()를 사용하여 
실행하고자 하는 메서드에 대한 델리케이트를 지정하는 것이다. 
이 StartNew()는 쓰레드를 생성과 동시에 실행하는 방식이다
*/

namespace  GrammarTest
{
    public class Program
    {
        static void func1()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine("Thread 1");
            }
        }
        static void func2()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine("Thread 2");
            }
        }
        public static async Task Main(String[] args)
        {
            Task thread1 = Task.Factory.StartNew(() => func1());
            Task thread2 = Task.Factory.StartNew(() => func2());
            
            Task.WaitAll(thread1, thread2);
            Console.WriteLine("The End");
            
            /*
             Thread 2
             Thread 2
             Thread 2
             Thread 2
             Thread 2
             Thread 1
             Thread 1
             Thread 1
             Thread 1
             Thread 1
             The End
             */
        }
       
    }
}


namespace GrammarTest
{
    public class Program
    {
        static void func1()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine("Thread 1");
            }
        }
        static void func2()
        {
            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine("Thread 2");
            }
        }
        public static async Task Main(String[] args)
        {
            Thread thread1 = new Thread(new ThreadStart(func1));
            Thread thread2 = new Thread(new ThreadStart(func2));
            thread1.Start();
            thread2.Start();
            thread2.Join();
            Console.WriteLine("The End");
            /*
            Thread 1
            Thread 1
            Thread 1
            Thread 1
            Thread 1
            Thread 2
            Thread 2
            Thread 2
            Thread 2
            Thread 2
            The End
             */
        }
    }
}



```

## 1️⃣ C++에서의 join()
```cpp
#include <iostream>
#include <thread>

void func1() {
    for (int i = 0; i < 5; i++)
        std::cout << "Thread 1\n";
}

void func2() {
    for (int i = 0; i < 5; i++)
        std::cout << "Thread 2\n";
}

int main() {
    std::thread t1(func1);
    std::thread t2(func2);

    t1.join(); // t1이 끝날 때까지 대기
    t2.join(); // t2가 끝날 때까지 대기

    std::cout << "The End\n";
}
```

- std::thread::join()은 해당 스레드가 종료될 때까지 현재 스레드를 블로킹
- join()을 호출하지 않고 스레드 객체가 소멸되면 std::terminate()가 호출되어 프로그램이 비정상 종료됨

## 2️⃣ Java에서의 join()
```java
class MyThread extends Thread {
    private String name;
    MyThread(String name) { this.name = name; }
    public void run() {
        for (int i = 0; i < 5; i++)
            System.out.println(name);
    }
}

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new MyThread("Thread 1");
        Thread t2 = new MyThread("Thread 2");

        t1.start();
        t2.start();

        t1.join(); // t1 종료 대기
        t2.join(); // t2 종료 대기

        System.out.println("The End");
    }
}

```
- Thread.join()은 호출한 스레드가 대상 스레드의 종료를 기다림
- join(long millis)로 최대 대기 시간을 지정할 수도 있음

## 3️⃣ Python에서의 join()
```python
import threading

def func1():
    for _ in range(5):
        print("Thread 1")

def func2():
    for _ in range(5):
        print("Thread 2")

t1 = threading.Thread(target=func1)
t2 = threading.Thread(target=func2)

t1.start()
t2.start()

t1.join()  # t1 종료 대기
t2.join()  # t2 종료 대기

print("The End")
```

- Thread.join()은 해당 스레드가 끝날 때까지 현재 스레드를 블로킹
- join(timeout)으로 최대 대기 시간을 지정 가능

📌 C#, C++, Java, Python 비교
| 언어     | 동기화 방식                   | 설명                                                         |
|----------|------------------------------|--------------------------------------------------------------|
| C#       | `Thread.Join()`              | 특정 스레드가 종료될 때까지 현재 스레드를 블로킹             |
|          | `Task.WaitAll()`             | 여러 Task가 모두 완료될 때까지 대기                          |
| C++      | `std::thread::join()`        | C++11부터 지원. 스레드가 종료될 때까지 현재 스레드를 블로킹 |
| Java     | `Thread.join()`              | 지정한 스레드가 종료될 때까지 현재 스레드를 블로킹           |
| Python   | `Thread.join()`              | 해당 스레드가 끝날 때까지 현재 스레드를 블로킹               |


## 💡 정리
- join()은 스레드 동기화의 기본 도구
- 후속 작업이 스레드의 결과나 종료를 전제로 할 때 필수
- C#, C++, Java, Python 모두 개념은 동일하지만, 언어별로 예외 처리나 시간 제한 옵션이 조금씩 다름

---

