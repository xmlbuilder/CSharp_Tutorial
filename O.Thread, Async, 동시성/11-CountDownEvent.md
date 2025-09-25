# CountdownEvent 예제

CountdownEvent를 사용해 메인 쓰레드가 여러 작업 쓰레드 중 일부의 완료 신호만 기다리는 구조를 보여주고 있어요.
이 패턴은 병렬 작업에서 부분 완료 조건을 기다릴 때 매우 유용.

## C# 소스
```csharp
//  CountdownEvent는 한 쓰레드에서 복수 쓰레드들로부터의 신호들을 기다리는데 사용된다. 
//  아래는 10개의 쓰레드를 시작한 후, 
//  이 쓰레드들로부터 처음 5개의 신호가 (CountdownEvent.Signal() 메서드) 먼저 도착하는 대로 
//  메인쓰레드는 Wait 대기 상태를 해제하고 다음 문장을 실행하게 된다.

namespace GrammarTest
{
    class Program
    {
        // CountdownEvent 객체 필드
        static CountdownEvent countEvent = new CountdownEvent(5);
        static void Main()
        {
            // 10개의 쓰레드 시작
            // 10개중 5개만 Vote만 끝내면 중지            
            for (int i = 0; i < 10; i++)
            {
                new Thread(Vote).Start(i);
            }
            // 메인쓰레드 첫 5개 신호를 기다림
            countEvent.Wait();
            Console.WriteLine("Vote is done!");
            /*
            1: Vote
            0: Vote
            4: Vote
            3: Vote
            2: Vote
            5: No vote
            6: No vote
            7: No vote
            8: No vote
            9: No vote
            Vote is done!
             */
        }
        
        static void Vote(object id)
        {                        
            if (countEvent.CurrentCount > 0)
            {
                // CountdownEvent 신호. -1씩 카운트다운.
                countEvent.Signal();
                Console.WriteLine("{0}: Vote", id);
            }
            else
            {
                Console.WriteLine("{0}: No vote", id);
            }
        }
    }
}


```

## 🧵 C++ 예제 (std::condition_variable + atomic counter)
C++에는 CountdownEvent 같은 고수준 클래스는 없지만, std::condition_variable과 std::atomic을 조합해서 비슷한 기능을 구현할 수 있어요.
```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <atomic>
#include <condition_variable>
#include <mutex>

std::atomic<int> count(5);
std::mutex mtx;
std::condition_variable cv;

void Vote(int id) {
    if (count > 0) {
        count--;
        std::cout << id << ": Vote\n";
        if (count == 0) {
            cv.notify_one();
        }
    } else {
        std::cout << id << ": No vote\n";
    }
}

int main() {
    std::vector<std::thread> threads;
    for (int i = 0; i < 10; ++i) {
        threads.emplace_back(Vote, i);
    }

    std::unique_lock<std::mutex> lock(mtx);
    cv.wait(lock, [] { return count == 0; });

    std::cout << "Vote is done!\n";

    for (auto& t : threads) t.join();
}
```



## ☕ Java 예제 (CountDownLatch)
Java에는 CountDownLatch가 있어서 C#의 CountdownEvent와 거의 동일한 기능을 제공합니다.
```java
import java.util.concurrent.CountDownLatch;

public class CountdownExample {
    static CountDownLatch latch = new CountDownLatch(5);

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            final int id = i;
            new Thread(() -> vote(id)).start();
        }

        try {
            latch.await(); // 5개의 신호를 기다림
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Vote is done!");
    }

    static void vote(int id) {
        if (latch.getCount() > 0) {
            latch.countDown();
            System.out.println(id + ": Vote");
        } else {
            System.out.println(id + ": No vote");
        }
    }
}
```


## 🐍 Python 예제 (threading.Condition + counter)
Python에는 CountdownEvent가 없지만, threading.Condition과 공유 변수로 유사하게 구현할 수 있어요.
```python
import threading

count = 5
lock = threading.Lock()
condition = threading.Condition(lock)

def vote(id):
    global count
    with lock:
        if count > 0:
            count -= 1
            print(f"{id}: Vote")
            if count == 0:
                condition.notify()
        else:
            print(f"{id}: No vote")

threads = []
for i in range(10):
    t = threading.Thread(target=vote, args=(i,))
    threads.append(t)
    t.start()

with condition:
    condition.wait()  # count가 0이 될 때까지 기다림

print("Vote is done!")

for t in threads:
    t.join()

```

## 📊 요약 비교
| 언어     | 동기화 도구                     | 대기 메서드            | 비고             |
|----------|----------------------------------|------------------------|------------------|
| C#       | `CountdownEvent`                | `Wait()`               | 고수준 API        |
| C++      | `condition_variable` + `atomic` | `cv.wait()`            | 수동 구현 필요     |
| Java     | `CountDownLatch`                | `await()`              | 직관적이고 간단함  |
| Python   | `Condition` + 변수              | `condition.wait()`     | 유연하지만 수동적  |

---


