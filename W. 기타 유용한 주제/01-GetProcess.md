## 📘 C# Process

- C#에서 `System.Diagnostics.Process`를 사용해 현재 시스템의 모든 프로세스 목록을 가져오는 예제

```csharp
Process[] processes = Process.GetProcesses(); // 모든 프로세스 가져오기
var proc = Process.GetCurrentProcess();       // 현재 프로세스 정보
var mem = proc.WorkingSet64;                  // 메모리 사용량
var cpu = proc.TotalProcessorTime;            // CPU 사용 시간
```

- Process.GetProcesses()로 전체 프로세스 목록 조회
- WorkingSet64, TotalProcessorTime으로 리소스 사용량 확인

### 📌 전체 소스
```csharp
/*
현재 로컬 컴퓨텅에 있는 모든 프로세스 목록을 가져옵니다.
*/

using System.Diagnostics;
class Program
{
    static int Main(string[] args)
    {
        Process[] processes = Process.GetProcesses();
        var proc = Process.GetCurrentProcess();
        var mem = proc.WorkingSet64;
        var cpu = proc.TotalProcessorTime;
        
        /*
        입력 값: 123456.789
        "N" -123,456.80
        "N3" -123,456.800
        "N10" -123,456.8000000000
         */
        
        Console.WriteLine("My process used working set {0:n3} K of working set and CPU {1:n} msec", 
                 mem / 1024.0, cpu.TotalMilliseconds);
        //My process used working set 26,404.000 K of working set and CPU 15.62 msec
        
        foreach (var aProc in Process.GetProcesses())
            Console.WriteLine("Proc {0,30}  CPU {1,-20:n} msec", aProc.ProcessName, cpu.TotalMilliseconds);
        /*
         Proc                        svchost  CPU 31.25                msec
         Proc            ShellExperienceHost  CPU 31.25                msec
         Proc                  RuntimeBroker  CPU 31.25                msec
         Proc                        svchost  CPU 31.25                msec
         Proc                        svchost  CPU 31.25                msec
         Proc             backgroundTaskHost  CPU 31.25                msec
         Proc                         armsvc  CPU 31.25                msec
         Proc                  RuntimeBroker  CPU 31.25                msec
         Proc                         chrome  CPU 31.25                msec
         Proc                         chrome  CPU 31.25                msec
         Proc                         chrome  CPU 31.25                msec
         */
        
        return 0;
    }
}
```

### 📌 Java에서 모든 프로세스 가져오기
- Java는 기본적으로 OS의 프로세스 목록을 직접 가져오는 API는 없지만, OS 명령어를 실행해서 파싱하는 방식으로 구현.
```java
import java.io.*;

public class ProcessList {
    public static void main(String[] args) {
        try {
            Process process = Runtime.getRuntime().exec("tasklist"); // Windows 기준
            BufferedReader reader = new BufferedReader(new InputStreamReader(process.getInputStream()));
            String line;
            while ((line = reader.readLine()) != null)
                System.out.println(line);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

- Windows: tasklist
- Linux/macOS: ps -e 또는 top -b -n 1

### 📌 Python에서 모든 프로세스 가져오기
- Python은 psutil이라는 강력한 라이브러리를 통해 매우 쉽게 프로세스 정보를 가져올 수 있음.

```python
import psutil

for proc in psutil.process_iter(['pid', 'name', 'cpu_percent', 'memory_info']):
    try:
        print(f"PID: {proc.info['pid']}, Name: {proc.info['name']}, CPU: {proc.info['cpu_percent']}%,
            Mem: {proc.info['memory_info'].rss / 1024:.2f} KB")
    except (psutil.NoSuchProcess, psutil.AccessDenied):
        pass
```

- psutil.process_iter()로 전체 프로세스 순회
- cpu_percent, memory_info 등 다양한 리소스 정보 제공
- 설치 필요: pip install psutil


### 📌 C++에서 모든 프로세스 가져오기 (Windows 기준)
- Windows에서는 ToolHelp32Snapshot API를 사용해 프로세스 목록을 가져올 수 있음.

```cpp
#include <windows.h>
#include <tlhelp32.h>
#include <iostream>

int main() {
    HANDLE hSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    PROCESSENTRY32 pe;
    pe.dwSize = sizeof(PROCESSENTRY32);

    if (Process32First(hSnapshot, &pe)) {
        do {
            std::wcout << L"Process: " << pe.szExeFile << L"  PID: " << pe.th32ProcessID << std::endl;
        } while (Process32Next(hSnapshot, &pe));
    }

    CloseHandle(hSnapshot);
    return 0;
}
```

- CreateToolhelp32Snapshot()으로 프로세스 스냅샷 생성
- Process32First() / Process32Next()로 반복 조회

### 📌 프로세스 목록 조회 비교 요약

| 언어       | 프로세스 조회 방식                          | 리소스 정보 조회 가능 | 플랫폼 의존성 | 특징 요약                                 |
|------------|---------------------------------------------|------------------------|----------------|--------------------------------------------|
| **C#**     | `Process.GetProcesses()`                    | ✅ `WorkingSet64`, `TotalProcessorTime` | Windows 전용 | 가장 직관적이며 풍부한 API 제공             |
| **Java**   | `Runtime.exec("tasklist")` 또는 `ps` 명령어 | ❌ 직접 파싱 필요       | OS 명령어에 따라 다름 | 기본 API 없음, 명령어 결과 파싱 필요         |
| **Python** | `psutil.process_iter()`                     | ✅ `cpu_percent`, `memory_info` 등 | 크로스 플랫폼 | 설치만 하면 강력하고 간결한 기능 제공        |
| **C++**    | `CreateToolhelp32Snapshot()` (WinAPI)       | ❌ 추가 API 필요        | Windows 전용     | 복잡하지만 세부 제어 가능, WinAPI 기반       |

---
