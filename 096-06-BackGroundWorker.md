# C# BackgroundWorker

Qt QThread 예제는 백그라운드에서 작업을 수행하면서 UI 스레드에 안전하게 진행 상황(progress)과 완료 상태를 알리는 패턴을 구현.  이 개념은 C++, Java, Python에도 각각 비슷한 방식이 있습니다.

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
### ✅ C#
```csharp
using System.ComponentModel;
namespace backgroundworkertest
{
    class MainClass
    {
        public void do_work(object sender, DoWorkEventArgs e) {
            
            Console.WriteLine("Start");
            BackgroundWorker b = (BackgroundWorker) sender;
           
            for (int i = 0; i < 100; i++)
            {
                long total = 0;
                for (int j = 0; j < 1000000; j++)
                {
                    total += j;
                }
                b.ReportProgress(i);
            }
            Thread.Sleep(50);
            b.ReportProgress(100);
            Console.WriteLine("Done");
        }

        public static void Main (string[] args)
        {
            for(int i = 0; i < 6; i++) {
                MainClass mainClass = new MainClass();
                mainClass.Run();
            }
            Thread.Sleep(2000);
            Console.WriteLine("Really done");
        }
        
        private void Bg_ProgressChanged(object sender, ProgressChangedEventArgs e)
        {
            Console.WriteLine("Progress : {0}", e.ProgressPercentage);
        }
        
        private void Bg_RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
        {
            Console.WriteLine("Task completed");
        }

        private void Run()
        {
            BackgroundWorker b = new BackgroundWorker();
            b.WorkerReportsProgress = true;
            b.DoWork += new DoWorkEventHandler(do_work);
            b.ProgressChanged += Bg_ProgressChanged;
            b.RunWorkerCompleted += Bg_RunWorkerCompleted;
            b.RunWorkerAsync();
        }
    }
}
```

### ✅ C++
```cpp
class WorkerThread : public QThread
{
    Q_OBJECT
public:
    explicit WorkerThread(QObject *parent = nullptr);
    void run() override{
        int iVal = 0;
        while(true){
            if(m_bForceStop) break;
            msleep(200);
            emit progressChanged(iVal);
            iVal++;
            if(iVal > 100) iVal = 0;
        }
        emit progressChanged(0);
    }
    void setForceStop() { m_bForceStop = true; }
signals:
     void progressChanged(int iVal);
private:
     bool m_bForceStop {false};
};

#pragma once
#include <QMainWindow>

class WorkerThread;
    QT_BEGIN_NAMESPACE
    namespace Ui { class MainWindow; }
QT_END_NAMESPACE

class MainWindow : public QMainWindow
{
    Q_OBJECT
public:
    MainWindow(QWidget *parent = nullptr);
    ~MainWindow();
public slots:
    void onProgressChanged(int iVal);
private slots:
    void on_actionStop_triggered();
    void on_actionOpen_triggered();
private:
    Ui::MainWindow *ui;
    WorkerThread* m_pWorkThread {nullptr};
};

#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QDebug>
#include "workerthread.h"

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);
}

MainWindow::~MainWindow()
{
    delete ui;
}

void MainWindow::onProgressChanged(int iVal)
{
    ui->progressBar->setValue(iVal);
}

void MainWindow::on_actionStop_triggered()
{
    if(m_pWorkThread->isRunning()){
        m_pWorkThread->setForceStop();
        m_pWorkThread = nullptr;
    }
}

void MainWindow::on_actionOpen_triggered()
{
    if(m_pWorkThread != nullptr && m_pWorkThread->isRunning()) return;
    
    m_pWorkThread = new WorkerThread;
    // Connect our signal and slot
    connect(m_pWorkThread, SIGNAL(progressChanged(int)),
            SLOT(onProgressChanged(int)));
    connect(m_pWorkThread, SIGNAL(finished()),
            m_pWorkThread, SLOT(deleteLater()));
    // Run, Forest, run!
    m_pWorkThread->start();
}

```

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

