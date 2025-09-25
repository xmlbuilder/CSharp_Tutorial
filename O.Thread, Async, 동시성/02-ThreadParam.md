# 🧵 C# Thread Param
```csharp
namespace ThreadTest
{
    class Program
    {
        static void Main(string[] args)
        {
            Thread t1 = new Thread(new ThreadStart(Run));           
            t1.Start();
            
            Thread t2 = new Thread(new ParameterizedThreadStart(Calc));
            t2.Start(1.0);
            
            Thread t3 = new Thread(() => Sum(10, 20, 30));
            t3.Start();
            
            Thread t4 = new Thread(new ThreadStart(Run));
            t4.IsBackground = true;
            t4.Start();
            Thread t5 = new Thread(new  ParameterizedThreadStart(ParamMethod));
            t5.Start(new int[]{1,2,3,4});
            
            /*
             Calc Started
             r=1, area=3.14
             Calc Finished
             ParamMethod Started
             Sum Started
             60
             Sum Finished
             Total :  10
             ParamMethod Finished
             Run Started
             Run Started
             Total : 3
             Run Finished
             */
        }

        static void Run()
        {
            Console.WriteLine("Run Started");
            int total = 0;
            for (int i = 0; i < 3; i++)
            {
                Thread.Sleep(100);
                total += i;
            }
            Console.WriteLine("Total : {0}", total);
            Console.WriteLine("Run Finished");
        }

        static void Calc(object radius)
        {
            Console.WriteLine("Calc Started");
            double r = (double) radius;
            double area = r * r * 3.14;
            Console.WriteLine("r={0}, area={1}", r,  area);
            Console.WriteLine("Calc Finished");
        }

        static void Sum(int d1, int d2, int d3)
        {
            Console.WriteLine("Sum Started");
            int sum = d1 + d2 + d3;
            Console.WriteLine(sum);
            Console.WriteLine("Sum Finished");
        }

        static void ParamMethod(object obj)
        {
            Console.WriteLine("ParamMethod Started");
            int total = 0;
            int[] number = (int[]) obj;
            for (int i = 0; i < number.Length; i++)
            {
                total += number[i];
            }
            
            Console.WriteLine("Total :  {0}", total);
            
            Console.WriteLine("ParamMethod Finished");
        }
    }
}


```
- ThreadStart: 인자 없이 실행
- ParameterizedThreadStart: object 타입으로 인자 전달
- 람다식 사용: 여러 인자 직접 전달 가능
- IsBackground: 백그라운드 쓰레드 설정

## 🧵 C++ (std::thread 사용)
```cpp
#include <iostream>
#include <thread>
#include <vector>

void run() {
    std::cout << "Run Started\n";
    int total = 0;
    for (int i = 0; i < 3; ++i) {
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        total += i;
    }
    std::cout << "Total : " << total << "\nRun Finished\n";
}

void calc(double r) {
    std::cout << "Calc Started\n";
    double area = r * r * 3.14;
    std::cout << "r=" << r << ", area=" << area << "\nCalc Finished\n";
}

void sum(int a, int b, int c) {
    std::cout << "Sum Started\n" << (a + b + c) << "\nSum Finished\n";
}

void paramMethod(std::vector<int> nums) {
    std::cout << "ParamMethod Started\n";
    int total = 0;
    for (int n : nums) total += n;
    std::cout << "Total : " << total << "\nParamMethod Finished\n";
}

int main() {
    std::thread t1(run);
    std::thread t2(calc, 1.0);
    std::thread t3(sum, 10, 20, 30);
    std::thread t4(run);
    std::thread t5(paramMethod, std::vector<int>{1, 2, 3, 4});

    t1.join(); t2.join(); t3.join(); t4.join(); t5.join();
}
```


## ☕ Java (Runnable & Lambda)
```java
public class ThreadTest {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> run());
        t1.start();

        Thread t2 = new Thread(() -> calc(1.0));
        t2.start();

        Thread t3 = new Thread(() -> sum(10, 20, 30));
        t3.start();

        Thread t4 = new Thread(() -> run());
        t4.setDaemon(true);
        t4.start();

        Thread t5 = new Thread(() -> paramMethod(new int[]{1, 2, 3, 4}));
        t5.start();
    }

    static void run() {
        System.out.println("Run Started");
        int total = 0;
        for (int i = 0; i < 3; i++) {
            try { Thread.sleep(100); } catch (InterruptedException e) {}
            total += i;
        }
        System.out.println("Total : " + total);
        System.out.println("Run Finished");
    }

    static void calc(double r) {
        System.out.println("Calc Started");
        double area = r * r * 3.14;
        System.out.println("r=" + r + ", area=" + area);
        System.out.println("Calc Finished");
    }

    static void sum(int a, int b, int c) {
        System.out.println("Sum Started");
        System.out.println(a + b + c);
        System.out.println("Sum Finished");
    }

    static void paramMethod(int[] nums) {
        System.out.println("ParamMethod Started");
        int total = 0;
        for (int n : nums) total += n;
        System.out.println("Total : " + total);
        System.out.println("ParamMethod Finished");
    }
}
```


## 🐍 Python (threading.Thread 사용)
```python
import threading
import time

def run():
    print("Run Started")
    total = 0
    for i in range(3):
        time.sleep(0.1)
        total += i
    print(f"Total : {total}")
    print("Run Finished")

def calc(r):
    print("Calc Started")
    area = r * r * 3.14
    print(f"r={r}, area={area}")
    print("Calc Finished")

def sum_func(a, b, c):
    print("Sum Started")
    print(a + b + c)
    print("Sum Finished")

def param_method(nums):
    print("ParamMethod Started")
    total = sum(nums)
    print(f"Total : {total}")
    print("ParamMethod Finished")

t1 = threading.Thread(target=run)
t2 = threading.Thread(target=calc, args=(1.0,))
t3 = threading.Thread(target=sum_func, args=(10, 20, 30))
t4 = threading.Thread(target=run, daemon=True)
t5 = threading.Thread(target=param_method, args=([1, 2, 3, 4],))

t1.start()
t2.start()
t3.start()
t4.start()
t5.start()

t1.join()
t2.join()
t3.join()
t5.join()
```





