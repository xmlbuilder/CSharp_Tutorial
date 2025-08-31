# C# BackgroundWorker

Qt QThread 예제는 백그라운드에서 작업을 수행하면서 UI 스레드에 안전하게 진행 상황(progress)과 완료 상태를 알리는 패턴을 구현.
이 개념은 C++, Java, Python에도 각각 비슷한 방식이 있습니다.

## 📊 비교표 — C#, C++, Java, Python의 BackgroundWorker 유사 개념
| 언어       | 클래스 / 패턴                 | 특징                                                                                  | UI 업데이트 방식           |
|------------|--------------------------------|---------------------------------------------------------------------------------|----------------------------|
| C#         | `BackgroundWorker`             | `DoWork`, `ProgressChanged`, `RunWorkerCompleted` 이벤트 기반 비동기 작업         | `ProgressChanged` 이벤트   |
|            | `Task` + `IProgress<T>`        | `async/await` 기반, `IProgress`로 진행률 보고                                       | `IProgress<T>.Report()`    |
| C++ (Qt)   | `QThread` + Signal/Slot         | `run()`에서 작업 수행, `emit`으로 UI 스레드에 시그널 전송                          | Qt Signal/Slot 메커니즘    |
| Java       | `SwingWorker`                   | `doInBackground()`에서 작업, `publish()`/`process()`로 중간 결과 전달               | `process()` 메서드         |
| Python     | `threading.Thread` + `queue`    | 표준 스레드에서는 `queue`나 콜백으로 UI 스레드에 전달                               | `queue`, 콜백              |
|            | `PyQt QThread`                  | Qt와 동일하게 Signal/Slot 사용                                                     | Qt Signal/Slot 메커니즘    |


## 💡 언어별 예시
### ✅ Java — SwingWorker
```java
import javax.swing.*;

public class MyWorker extends SwingWorker<Void, Integer> {
    @Override
    protected Void doInBackground() throws Exception {
        for (int i = 0; i <= 100; i++) {
            publish(i); // 중간 진행 상황 전달
            Thread.sleep(50);
        }
        return null;
    }

    @Override
    protected void process(java.util.List<Integer> chunks) {
        int latest = chunks.get(chunks.size() - 1);
        System.out.println("Progress: " + latest);
    }

    @Override
    protected void done() {
        System.out.println("Task completed");
    }
}
```


### ✅ Python — threading.Thread + queue
```python
import threading, queue, time

progress_queue = queue.Queue()

def worker():
    for i in range(101):
        time.sleep(0.05)
        progress_queue.put(i)

t = threading.Thread(target=worker)
t.start()

while True:
    try:
        progress = progress_queue.get(timeout=0.1)
        print(f"Progress: {progress}")
        if progress == 100:
            print("Task completed")
            break
    except queue.Empty:
        pass
```


### ✅ Python (PyQt) — QThread + Signal
```python
from PyQt5.QtCore import QThread, pyqtSignal
import time

class WorkerThread(QThread):
    progressChanged = pyqtSignal(int)

    def run(self):
        for i in range(101):
            time.sleep(0.05)
            self.progressChanged.emit(i)

```

## 📌 핵심 개념 정리
- UI 스레드와 작업 스레드 분리: 무거운 작업은 UI 스레드를 블로킹하지 않도록 별도 스레드에서 실행.
- 진행 상황 보고: 이벤트, 시그널, 큐 등을 통해 UI 스레드로 안전하게 전달.
- 작업 완료 처리: 완료 시 UI 업데이트나 후속 작업 실행.

---

