# Thread 비동기 처리 개요
C#의 async/await는 비동기 작업을 “작업 단위(Task)”로 다루고, await 지점에서 스레드를 점유하지 않으면서 결과가 준비되면 이어서 실행합니다. C++, Java, Python도 각자 방식으로 비동기를 제공합니다. 핵심 차이는 이벤트 루프 기반 비동기(IO에 강함)인지, 스레드/스레드풀 기반 비동기(일반 작업 분산)에 무게를 두는지입니다.

비동기 모델 비교표
| 언어       | 비동기 모델 / 키워드         | 대표 API / 클래스                              | 특징                                                                 | 장점                                               | 단점                                                    |
|------------|-----------------------------|------------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------|
| C#         | `async` / `await` + Task    | `Task`, `Task<T>`, `HttpClient`, `IProgress<T>`| 스레드풀 + 논블로킹 IO 추상화, `CancellationToken` 지원               | 문법 간결, 예외 처리 용이, .NET 라이브러리와 통합   | UI 스레드 컨텍스트 주의 필요                             |
| C++ (Qt)   | QThread + Signal/Slot       | `QThread`, `QtConcurrent`, `QNetworkAccessManager` | 스레드 기반, Signal/Slot으로 UI 안전 업데이트                        | UI와 자연스러운 연동, 고성능 네이티브               | 표준 C++만으로는 고수준 비동기 IO 부족, Qt 의존성        |
| Java       | CompletableFuture           | `CompletableFuture`, `ExecutorService`, `HttpClient.sendAsync` | 논블로킹 체이닝, Executor로 스레드풀 제어 가능                        | 체이닝/예외 처리 강력, Virtual Thread 지원(21+)     | 문법이 다소 장황, 블로킹 호출 혼용 시 주의               |
| Python     | asyncio + await             | `asyncio`, `aiohttp`, `asyncio.Task`           | 이벤트 루프 기반, IO 바운드에 최적                                   | 문법 간결, 다양한 async 라이브러리                  | GIL로 CPU 바운드에 비효율, 멀티프로세싱 필요              |
| Python     | Thread/Process Pool         | `threading`, `concurrent.futures`              | 스레드풀/프로세스풀로 동시성 구현                                    | CPU 바운드 작업 분산 가능                           | 스레드는 GIL 영향, 프로세스풀은 IPC 오버헤드              |

필요 시 UI에서는 C++(Qt), Java(Swing/JavaFX), Python(PyQt/Tkinter)의 “메인(UI) 스레드로 되돌아오는” 메커니즘을 이용해 업데이트합니다.

## C#
```csharp
// 비동기 메서드로 만들려면 메서드에 async 키워드를 지정하고 반환값을 Task형으로 지정해야한다. 
// 반환값이 없는 메서드일 경우에는 Task형으로 지정하고 반환값이 있다면 Task<Tresult>형으로 지정합니다.

 public static async Task Main(String[] args)
{
    Task<int> downloading = DownloadDocsMainPageAsync();
    Console.WriteLine($"{nameof(Main)}: Launched downloading.");

    int bytesLoaded = await downloading;
    Console.WriteLine($"{nameof(Main)}: Downloaded {bytesLoaded} bytes.");

}

private static async Task<int> DownloadDocsMainPageAsync()
{
    Console.WriteLine($"{nameof(DownloadDocsMainPageAsync)}: About to start downloading.");

    var client = new HttpClient();
    byte[] content = await client.GetByteArrayAsync("https://docs.microsoft.com/en-us/");

    Console.WriteLine($"{nameof(DownloadDocsMainPageAsync)}: Finished downloading.");
    return content.Length;
}
```

## C++ 비동기 처리

짧은 정리: 표준은 스레드와 미래를 제공하고, 네트워크/HTTP는 외부 라이브러리를 씁니다. UI에서는 Qt 신호/슬롯이 자연스러운 패턴입니다.
- std::async + std::future
```cpp
#include <future>
#include <chrono>
#include <thread>
#include <iostream>

int fake_download() {
    std::this_thread::sleep_for(std::chrono::milliseconds(300));
    return 12345; // 다운로드된 바이트 수라고 가정
}

int main() {
    auto fut = std::async(std::launch::async, fake_download);
    std::cout << "Launched\n";
    int bytes = fut.get();
    std::cout << "Downloaded " << bytes << " bytes\n";
}
```
- 스레드풀 패턴(간단 예)
```cpp
#include <vector>
#include <future>
#include <numeric>

int work(int n) { return n * n; }

int main() {
    std::vector<std::future<int>> futs;
    for (int i = 0; i < 8; ++i)
        futs.emplace_back(std::async(std::launch::async, work, i));

    int sum = 0;
    for (auto& f : futs) sum += f.get();
}
```
- 코루틴(C++20) 개념
- 핵심: co_await로 “중단 가능한 함수”를 작성. 표준만으로 IO await는 어려워서 보통 Boost.Asio/Beast, cppcoro 등의 런타임과 결합합니다.
- Qt: HTTP는 QNetworkAccessManager를 쓰고, 진행률/완료는 시그널로 UI 스레드에 전달합니다.

## Java 비동기 처리
짧은 정리: CompletableFuture로 비동기 체이닝을 구성하고, Java 11+의 HttpClient.sendAsync로 네트워크도 자연스럽게 처리합니다.
- HttpClient + CompletableFuture
```java
import java.net.URI;
import java.net.http.*;
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
        HttpClient client = HttpClient.newHttpClient();
        HttpRequest req = HttpRequest.newBuilder(URI.create("https://docs.microsoft.com/en-us/")).build();

        CompletableFuture<Integer> downloading =
            client.sendAsync(req, HttpResponse.BodyHandlers.ofByteArray())
                  .thenApply(resp -> resp.body().length);

        System.out.println("Main: Launched downloading.");
        int bytesLoaded = downloading.join(); // 또는 .get()
        System.out.println("Main: Downloaded " + bytesLoaded + " bytes.");
    }
}
```
- 체이닝/예외/스레드풀
- 체이닝: thenApply, thenCompose로 비동기 단계 연결.
- 예외 처리: exceptionally, handle로 복구 로직 추가.
- 커스텀 풀: CompletableFuture.supplyAsync(supplier, executor)로 전용 ExecutorService 사용.
- Virtual Threads(자바 21+): 대규모 동시성을 “스레드처럼” 코딩하면서 경량으로 실행.

## Python 비동기 처리
짧은 정리: 네트워크/IO는 asyncio + aiohttp가 표준에 가깝고, CPU 바운드는 multiprocessing이나 네이티브 확장(NumPy 등)을 고려합니다.
- asyncio + aiohttp
```python
import asyncio
import aiohttp

async def download_docs_main_page():
    print("download: About to start downloading.")
    async with aiohttp.ClientSession() as session:
        async with session.get("https://docs.microsoft.com/en-us/") as resp:
            content = await resp.read()
    print("download: Finished downloading.")
    return len(content)

async def main():
    task = asyncio.create_task(download_docs_main_page())
    print("Main: Launched downloading.")
    bytes_loaded = await task
    print(f"Main: Downloaded {bytes_loaded} bytes.")

if __name__ == "__main__":
    asyncio.run(main())
```

- 스레드/프로세스 풀과의 조합
```python

import concurrent.futures, time

def cpu_work(n):
    total = 0
    for i in range(10_000_000):
        total += (i % (n + 1))
    return total

if __name__ == "__main__":
    with concurrent.futures.ProcessPoolExecutor() as pool:
        futs = [pool.submit(cpu_work, n) for n in range(4)]
        results = [f.result() for f in futs]
```

- IO 바운드: ThreadPoolExecutor 또는 asyncio.to_thread로 기존 동기 함수를 비동기 루프에 통합.
- CPU 바운드: GIL 때문에 ProcessPoolExecutor나 C 확장(NumPy, Numba)을 권장.

## 실전 팁
- 모델 선택: IO 중심이면 이벤트 루프(자바 sendAsync, 파이썬 asyncio)가 단순하고 효율적입니다. CPU 중심이면 스레드풀(C#/Java) 또는 멀티프로세싱(Python)을 고려하세요.
- 차단 코드 주의: 이벤트 루프 내부에서 블로킹 호출(예: 파일/네트워크 동기 API)을 직접 쓰지 말고, 비동기 API나 전용 풀로 넘기세요.
- 취소와 타임아웃: C# CancellationToken, Java orTimeout/completeOnTimeout, Python asyncio.wait_for로 제어하세요.
- 진행률/컨텍스트: UI는 “메인 스레드로 복귀”가 필요합니다. C# IProgress<T>, Qt 시그널/슬롯, Java Swing publish/process, Python(PyQt) 시그널을 사용하세요.
- 예외 처리: C# try/await/catch, Java exceptionally/handle, Python try/await/except로 비동기 경로의 오류를 명시적으로 다루세요.




--- 

# 📦 시나리오 설명 ("동시 N건 다운로드 + 취소 + 진행률")
- 목표: 여러 개의 URL을 동시에 다운로드
- 진행률 표시: 각 다운로드 완료 시 전체 진행률 업데이트
- 취소 기능: 중간에 취소 요청 시 모든 다운로드 중단
## 
1️⃣ C# (async/await + HttpClient + CancellationToken)
```csharp
using System;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        string[] urls = {
            "https://example.com",
            "https://example.org",
            "https://example.net"
        };

        using var cts = new CancellationTokenSource();
        Console.CancelKeyPress += (s, e) => { e.Cancel = true; cts.Cancel(); };

        int completed = 0;
        var client = new HttpClient();

        var tasks = new Task[urls.Length];
        for (int i = 0; i < urls.Length; i++)
        {
            string url = urls[i];
            tasks[i] = Task.Run(async () =>
            {
                var data = await client.GetByteArrayAsync(url, cts.Token);
                Interlocked.Increment(ref completed);
                Console.WriteLine($"[{completed}/{urls.Length}] {url} - {data.Length} bytes");
            }, cts.Token);
        }

        try
        {
            await Task.WhenAll(tasks);
        }
        catch (OperationCanceledException)
        {
            Console.WriteLine("Download cancelled.");
        }
    }
}

```

## 2️⃣ C++ (Qt QNetworkAccessManager + QThread + Signal/Slot)

```cpp
#include <QCoreApplication>
#include <QNetworkAccessManager>
#include <QNetworkReply>
#include <QTimer>
#include <iostream>

class Downloader : public QObject {
    Q_OBJECT
public:
    Downloader(QStringList urls) : urls(urls) {
        connect(&manager, &QNetworkAccessManager::finished, this, &Downloader::onFinished);
    }

    void start() {
        for (const auto &url : urls) {
            QNetworkRequest req(url);
            QNetworkReply *reply = manager.get(req);
            connect(reply, &QNetworkReply::downloadProgress, this, &Downloader::onProgress);
        }
    }

public slots:
    void onFinished(QNetworkReply *reply) {
        completed++;
        std::cout << "[" << completed << "/" << urls.size() << "] "
                  << reply->url().toString().toStdString() << " done\n";
        reply->deleteLater();
        if (completed == urls.size()) QCoreApplication::quit();
    }

    void onProgress(qint64, qint64) {
        // 개별 진행률 표시 가능
    }

private:
    QNetworkAccessManager manager;
    QStringList urls;
    int completed = 0;
};

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);
    QStringList urls = {"https://example.com", "https://example.org", "https://example.net"};
    Downloader d(urls);
    QTimer::singleShot(0, &d, &Downloader::start);
    return app.exec();
}

```


## 3️⃣ Java (HttpClient + CompletableFuture + AtomicInteger)

```java
import java.net.URI;
import java.net.http.*;
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;

public class Main {
    public static void main(String[] args) {
        String[] urls = {
            "https://example.com",
            "https://example.org",
            "https://example.net"
        };

        HttpClient client = HttpClient.newHttpClient();
        AtomicInteger completed = new AtomicInteger();
        CompletableFuture<?>[] futures = new CompletableFuture<?>[urls.length];

        for (int i = 0; i < urls.length; i++) {
            String url = urls[i];
            futures[i] = client.sendAsync(
                    HttpRequest.newBuilder(URI.create(url)).build(),
                    HttpResponse.BodyHandlers.ofByteArray())
                .thenAccept(resp -> {
                    int count = completed.incrementAndGet();
                    System.out.println("[" + count + "/" + urls.length + "] " +
                        url + " - " + resp.body().length + " bytes");
                });
        }

        CompletableFuture.allOf(futures).join();
    }
}
```


## 4️⃣ Python (asyncio + aiohttp + asyncio.CancelledError)
```python
import asyncio
import aiohttp
import signal

urls = [
    "https://example.com",
    "https://example.org",
    "https://example.net"
]

async def download(session, url, progress):
    async with session.get(url) as resp:
        data = await resp.read()
        progress[0] += 1
        print(f"[{progress[0]}/{len(urls)}] {url} - {len(data)} bytes")

async def main():
    progress = [0]
    async with aiohttp.ClientSession() as session:
        tasks = [asyncio.create_task(download(session, u, progress)) for u in urls]
        await asyncio.gather(*tasks)

if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    for sig in (signal.SIGINT, signal.SIGTERM):
        loop.add_signal_handler(sig, loop.stop)
    try:
        loop.run_until_complete(main())
    except KeyboardInterrupt:
        print("Download cancelled.")

```

## 📌 특징 비교

| 기능 요소               | C# (async/await)              | Java (CompletableFuture)       | Python (asyncio)              | C++ (Qt/QNetwork)                  |
|--------------------------------|---------------------------|-----------------------------|------------------------------|-------------------------------------|
| **동시 N건 다운로드**  | `Task.WhenAll`로 손쉽게 구현  | `CompletableFuture.allOf` 지원   | `asyncio.gather`로 병렬 처리   | QNetworkAccessManager + QEventLoop |
| **취소 지원**    | `CancellationToken` 내장 지원     | `CompletableFuture.cancel` 가능   | `asyncio.Task.cancel` 사용         | QNetworkReply::abort()     |
| **진행률 표시**   | `IProgress<T>`로 UI 업데이트 용이  | Progress Listener 직접 구현 필요  | 콜백/async generator로 구현 가능   | Signal/Slot으로 UI 안전 업데이트 |
| **예외 처리**  | try/await/catch로 간결       | exceptionally(), handle() 메서드  | try/except + gather(return_exceptions=True) | Signal/Slot 기반 예외 전파  |
| **장점**     | 문법 간결, .NET 통합성 우수       | 체이닝 강력, Executor 제어 가능   | 문법 간단, 다양한 async 라이브러리 | 네이티브 성능, UI와 자연스러운 연동 |


---



