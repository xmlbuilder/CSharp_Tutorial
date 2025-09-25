# async/await를 활용해 현재 프로세스의 CPU 사용률 측정 샘플
C# 코드에서는 async/await를 활용해 현재 프로세스의 CPU 사용률을 주기적으로 비동기적으로 측정

## 🧠 C# 코드 요약
- ConsumeCPU: CPU를 일정 비율로 소모하는 함수
- GetCpuUsageForProcess: 현재 프로세스의 CPU 사용률을 500ms 간격으로 측정
- Main: 2초마다 CPU 사용률을 출력

### 소스 코드
```csharp
using System.Diagnostics;
namespace GrammarTest
{
    public class Program
    {
        public static async Task Main(string[] args)
        {
            var task = Task.Run(() => ConsumeCPU(50));
            while (true)
            {
                await Task.Delay(2000);
                var cpuUsage = await GetCpuUsageForProcess();
                Console.WriteLine(cpuUsage);
            }
            /*
            1.2807907002578272
            2.3421400129550944
            2.0262896328473414
            2.0821061399744325
            2.2783641389227127
            2.7886093448972153
            */
        }

        public static void ConsumeCPU(int percentage)
        {
            Stopwatch watch = new Stopwatch();
            watch.Start();
            while (true)
            {
                if (watch.ElapsedMilliseconds > percentage)
                {
                    Thread.Sleep(100 - percentage);
                    watch.Reset();
                    watch.Start();
                }
            }
        }

        private static async Task<double> GetCpuUsageForProcess()
        {
            var startTime = DateTime.UtcNow;
            var startCpuUsage = Process.GetCurrentProcess().TotalProcessorTime;
            await Task.Delay(500);
            var endTime = DateTime.UtcNow;
            var endCpuUsage = Process.GetCurrentProcess().TotalProcessorTime;
            var cpuUsedMs = (endCpuUsage - startCpuUsage).TotalMilliseconds;
            var totalMsPassed = (endTime - startTime).TotalMilliseconds;
            var cpuUsageTotal = cpuUsedMs / (Environment.ProcessorCount * totalMsPassed);
            return cpuUsageTotal * 100;
        }
        
    }
}

```


## 🧵 C++ (with <thread> and <chrono>)
C++에는 async/await는 없지만, std::async나 std::thread로 비동기 작업을 수행할 수 있어요.
```cpp
#include <iostream>
#include <thread>
#include <chrono>
#include <windows.h>

double GetCpuUsageForProcess() {
    FILETIME ftStart, ftEnd, ftDummy1, ftDummy2;
    ULARGE_INTEGER startTime, endTime;

    GetProcessTimes(GetCurrentProcess(), &ftDummy1, &ftDummy2, &ftStart, &ftEnd);
    startTime.LowPart = ftStart.dwLowDateTime;
    startTime.HighPart = ftStart.dwHighDateTime;

    std::this_thread::sleep_for(std::chrono::milliseconds(500));

    GetProcessTimes(GetCurrentProcess(), &ftDummy1, &ftDummy2, &ftStart, &ftEnd);
    endTime.LowPart = ftStart.dwLowDateTime;
    endTime.HighPart = ftStart.dwHighDateTime;

    double cpuUsedMs = (endTime.QuadPart - startTime.QuadPart) / 10000.0;
    return cpuUsedMs / (0.5 * std::thread::hardware_concurrency()) * 100.0;
}

int main() {
    std::thread([] {
        while (true) {
            std::this_thread::sleep_for(std::chrono::milliseconds(50));
        }
    }).detach();

    while (true) {
        std::this_thread::sleep_for(std::chrono::seconds(2));
        std::cout << GetCpuUsageForProcess() << std::endl;
    }
}

```


## ☕ Java (with CompletableFuture)
Java에서는 CompletableFuture를 사용해 비동기 작업을 수행할 수 있어요.
```java
import java.lang.management.ManagementFactory;
import java.lang.management.OperatingSystemMXBean;
import java.util.concurrent.*;

public class CpuUsageMonitor {
    public static void main(String[] args) {
        Executors.newSingleThreadExecutor().submit(() -> consumeCPU(50));

        ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
        scheduler.scheduleAtFixedRate(() -> {
            CompletableFuture<Double> future = CompletableFuture.supplyAsync(() -> getCpuUsage());
            future.thenAccept(cpu -> System.out.println(cpu));
        }, 0, 2, TimeUnit.SECONDS);
    }

    public static void consumeCPU(int percentage) {
        long busyTime = percentage;
        long idleTime = 100 - percentage;
        while (true) {
            long start = System.currentTimeMillis();
            while (System.currentTimeMillis() - start < busyTime);
            try { Thread.sleep(idleTime); } catch (InterruptedException ignored) {}
        }
    }

    public static double getCpuUsage() {
        OperatingSystemMXBean osBean = ManagementFactory.getOperatingSystemMXBean();
        if (osBean instanceof com.sun.management.OperatingSystemMXBean) {
            return ((com.sun.management.OperatingSystemMXBean) osBean).getProcessCpuLoad() * 100;
        }
        return -1;
    }
}

```

## 🐍 Python (with asyncio and psutil)
Python은 asyncio와 psutil을 활용하면 간단하게 비동기 CPU 측정이 가능해요.
```python
import asyncio
import psutil
import time
import threading

def consume_cpu(percentage):
    busy_time = percentage / 100
    idle_time = 1 - busy_time
    while True:
        start = time.time()
        while (time.time() - start) < busy_time:
            pass
        time.sleep(idle_time)

async def get_cpu_usage():
    process = psutil.Process()
    start = process.cpu_times()
    await asyncio.sleep(0.5)
    end = process.cpu_times()
    cpu_used = (end.user + end.system) - (start.user + start.system)
    return cpu_used / (0.5 * psutil.cpu_count()) * 100

async def main():
    threading.Thread(target=consume_cpu, args=(50,), daemon=True).start()
    while True:
        await asyncio.sleep(2)
        usage = await get_cpu_usage()
        print(usage)

asyncio.run(main())
```


## 🎯 요약 비교
| 언어     | 비동기 방식           | CPU 사용률 측정 방식                  | 병렬 처리 방식         |
|----------|------------------------|--------------------------------------|------------------------|
| C#       | `async/await`          | `Process.TotalProcessorTime`         | `Task.Run`             |
| C++      | `std::thread`          | `GetProcessTimes` (WinAPI)           | `detach()`             |
| Java     | `CompletableFuture`    | `OperatingSystemMXBean`              | `ExecutorService`      |
| Python   | `asyncio`              | `psutil.Process().cpu_times()`       | `threading.Thread`     |

---



