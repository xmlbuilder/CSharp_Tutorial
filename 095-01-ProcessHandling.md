# 🚀 C#에서의 프로세스 핸들링

```csharp
using System.Diagnostics;

Process myProcess = new Process();
myProcess.StartInfo.FileName = "notepad.exe";
myProcess.StartInfo.UseShellExecute = false;
myProcess.StartInfo.CreateNoWindow = true;
myProcess.Start();
```

rocess.Start()로 외부 프로그램 실행
- UseShellExecute = false → 명령줄 기반 실행
- CreateNoWindow = true → GUI 없이 백그라운드 실행
- WaitForExit()로 종료 대기, Kill()로 강제 종료 가능

## 전체 소스
```csharp

/*
로컬 및 원격 프로세스에 대한 액세스를 제공하고 로컬 시스템 프로세스를 시작하고 중지할 수 있습니다.
*/

using System.Diagnostics;

class Program
{
    static int Main(string[] args)
    {
        
        using (Process myProcess = new Process())
        {
            myProcess.StartInfo.UseShellExecute = false;
            // You can start any process, HelloWorld is a do-nothing example.
            myProcess.StartInfo.FileName = "notepad.exe";
            myProcess.StartInfo.CreateNoWindow = true;
            myProcess.Start();
            // This code assumes the process you are starting will terminate itself.
            // Given that it is started without a window so you cannot terminate it
            // on the desktop, it must terminate itself or you can do it programmatically
            // from this application using the Kill method.
        }

        ProcessStartInfo psi = new ProcessStartInfo();
        psi.UseShellExecute = false;
        psi.FileName = "bash";
        psi.Arguments = "-c \"" + "rider" + "\"";
        
        Console.WriteLine(psi.Arguments);
        //-c "rider"
        try
        {
            Process child = Process.Start(psi);
            child.WaitForExit();
        }
        catch (Exception e)
        {
            Console.WriteLine(e.ToString());
        }
        
        return 0;
    }
}
```


## ☕ Java에서의 프로세스 핸들링
```java
import java.io.*;

public class Main {
    public static void main(String[] args) {
        try {
            ProcessBuilder pb = new ProcessBuilder("notepad.exe");
            pb.inheritIO(); // 콘솔 출력 공유
            Process process = pb.start();
            process.waitFor(); // 종료 대기
        } catch (IOException | InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

- ProcessBuilder로 명령어 구성
- start()로 실행, waitFor()로 종료 대기
- destroy()로 강제 종료 가능

## 🐍 Python에서의 프로세스 핸들링

```python
import subprocess

# 실행
process = subprocess.Popen(["notepad.exe"], stdout=subprocess.PIPE, stderr=subprocess.PIPE)

# 종료 대기
process.wait()

# 강제 종료
# process.kill()

```

- subprocess.Popen()으로 비동기 실행
- run()은 동기 실행 (Python 3.5+)
- stdout, stderr 캡처 가능
- kill()로 강제 종료

## 💻 C++에서의 프로세스 핸들링 (Windows 기준)
```cpp
#include <windows.h>

int main() {
    STARTUPINFO si = { sizeof(si) };
    PROCESS_INFORMATION pi;

    // 실행할 명령어
    LPCSTR command = "notepad.exe";

    // 프로세스 생성
    if (CreateProcess(NULL, (LPSTR)command, NULL, NULL, FALSE, 0, NULL, NULL, &si, &pi)) {
        WaitForSingleObject(pi.hProcess, INFINITE); // 종료 대기
        CloseHandle(pi.hProcess);
        CloseHandle(pi.hThread);
    } else {
        MessageBox(NULL, "실행 실패", "Error", MB_OK);
    }

    return 0;
}
```

- CreateProcess()로 외부 프로그램 실행
- WaitForSingleObject()로 종료 대기
- TerminateProcess()로 강제 종료 가능


## 📌 프로세스 핸들링 비교 요약

| 언어     | 실행 방식               | 종료 대기         | 강제 종료       | 특징 요약                       |
|----------|-------------------------|-------------------|------------------|----------------------------------|
| **C#**   | `Process.Start()`       | `WaitForExit()`   | `Kill()`         | GUI/CLI 모두 제어 가능           |
| **Java** | `ProcessBuilder.start()`| `waitFor()`       | `destroy()`      | 명령어 인자 세분화 가능          |
| **Python** | `subprocess.Popen()`  | `wait()`          | `kill()`         | 출력 캡처 및 파이프 연결 용이    |
| **C++**  | `CreateProcess()`       | `WaitForSingleObject()` | `TerminateProcess()` | WinAPI 기반, 세부 제어 가능      |


---

