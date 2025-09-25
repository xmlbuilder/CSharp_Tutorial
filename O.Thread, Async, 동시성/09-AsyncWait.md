# C# await/wait
await가 **비동기 작업이 끝날 때까지 메서드 실행을 “일시 중단”**하고,
그 동안 스레드를 점유하지 않으며 제어를 호출자에게 반환하는 전형적인 async/await 패턴입니다.


## 📌 핵심 개념 — await와 wait의 차이
### - await
- 비동기 작업이 끝날 때까지 메서드 실행을 중단하지만 스레드를 차단하지 않음
- 완료 시점에 나머지 코드를 이어서 실행
### - wait (동기 대기)
- 현재 스레드를 블로킹하여 작업이 끝날 때까지 아무 일도 못 함
- UI나 이벤트 루프를 멈추게 할 수 있음

## C# 소스
```csharp
/*
await 연산자는 피연산자가 나타내는 비동기 작업이 완료될 때까지 바깥쪽 await 메서드의 평가를 일시 중단합니다. 
비동기 작업이 완료되면 await 연산자는 작업 결과를 반환합니다(있는 경우). 
이미 완료된 작업을 나타내는 피연산자에 await 연산자가 적용되면 바깥쪽 메서드를 일시 중단하지 않고 작업 결과를 즉시 반환합니다. 
await 연산자는 비동기 메서드를 평가하는 스레드를 차단하지 않습니다. 
await 연산자가 바깥쪽 비동기 메서드를 일시 중단하면 제어가 메서드 호출자에게 반환됩니다.
*/

class Program
{
    public static async Task Main(string[] args)
    {
        Task<int> downloading = DownloadDocsMainPageAsync();
        Console.WriteLine($"{nameof(Main)}: Launched downloading."); //->2
        
        int bytesLoaded = await downloading;

        Console.WriteLine($"{nameof(Main)}: Downloaded {bytesLoaded} bytes."); //->4
        
        //DownloadDocsMainPageAsync: About to start downloading.
        //Main: Launched downloading.
        //DownloadDocsMainPageAsync: Finished downloading.
        //Main: Downloaded 43577 bytes.
    }
    /*
    다음 예제에서 HttpClient.GetByteArrayAsync 메서드는 완료 시 바이트 배열을 생성하는 비동기 작업을 나타내는 Task<byte[]> 인스턴스를 반환합니다. 
    작업이 완료될 때까지 await 연산자는 DownloadDocsMainPageAsync 메서드를 일시 중단합니다. 
    DownloadDocsMainPageAsync가 일시 중단되면 제어는 DownloadDocsMainPageAsync의 호출자인 Main 메서드에 반환됩니다.
    ain 메서드는 DownloadDocsMainPageAsync 메서드가 수행한 비동기 작업의 결과가 필요할 때까지 실행됩니다. 
    GetByteArrayAsync가 모든 바이트를 가져오면 나머지 DownloadDocsMainPageAsync 메서드가 평가됩니다. 그 후에는 나머지 Main 메서드가 평가됩니다.
    비동기 메서드로 만들려면 메서드에 async 키워드를 지정하고 반환값을 Task형으로 지정해야한다. 
    반환값이 없는 메서드일 경우에는 Task형으로 지정하고 반환값이 있다면 Task<Tresult>형으로 지정합니다.
    */

    private static async Task<int> DownloadDocsMainPageAsync()
    {
        Console.WriteLine($"{nameof(DownloadDocsMainPageAsync)}: About to start downloading."); //->1
        var client = new HttpClient();
        byte[] content = await client.GetByteArrayAsync("https://docs.microsoft.com/en-us/");
        Console.WriteLine($"{nameof(DownloadDocsMainPageAsync)}: Finished downloading."); //->3
        return content.Length;
    }
}
```


## 1️⃣ C++ — std::future::get() vs C++20 co_await
### 동기 대기 (wait/get)
```cpp
#include <future>
#include <iostream>
#include <thread>

int download() {
    std::this_thread::sleep_for(std::chrono::seconds(2));
    return 12345;
}

int main() {
    std::future<int> fut = std::async(std::launch::async, download);
    std::cout << "Launched downloading\n";
    int bytes = fut.get(); // 여기서 스레드 블로킹
    std::cout << "Downloaded " << bytes << " bytes\n";
}
```

- get()은 스레드를 차단합니다.
- UI 스레드에서 쓰면 멈춘 것처럼 보일 수 있음.
## 비동기 대기 (co_await, C++20)
```cpp
#include <coroutine>
#include <iostream>

// 실제 네트워크 IO는 Boost.Asio, cppcoro 등과 결합 필요
// 추후에 보강
```

- C++ 표준만으로는 네트워크 IO를 co_await로 직접 처리하기 어렵고,
보통 Boost.Asio나 Qt의 이벤트 루프와 결합합니다.

## 2️⃣ Java — CompletableFuture.join() vs thenApplyAsync()
### 동기 대기 (join/get)
```java
import java.net.URI;
import java.net.http.*;
import java.util.concurrent.*;

public class Main {
    public static void main(String[] args) {
        HttpClient client = HttpClient.newHttpClient();
        CompletableFuture<byte[]> downloading =
            client.sendAsync(
                HttpRequest.newBuilder(URI.create("https://example.com")).build(),
                HttpResponse.BodyHandlers.ofByteArray()
            ).thenApply(HttpResponse::body);

        System.out.println("Launched downloading");
        int bytes = downloading.join().length; // 블로킹
        System.out.println("Downloaded " + bytes + " bytes");
    }
}
```
- join()/get()은 스레드를 차단합니다.

### 비동기 처리 (thenAccept, thenApply)
```java
downloading.thenAccept(data -> {
    System.out.println("Downloaded " + data.length + " bytes");
});
System.out.println("Main continues without blocking");
```


- 체이닝으로 논블로킹 실행 가능.

## 3️⃣ Python — await vs .result()
### 동기 대기 (result())
```python
import asyncio

async def download():
    await asyncio.sleep(2)
    return 12345

loop = asyncio.get_event_loop()
task = asyncio.ensure_future(download())
print("Launched downloading")
bytes_loaded = loop.run_until_complete(task)  # 블로킹
print(f"Downloaded {bytes_loaded} bytes")
```

### 비동기 대기 (await)
```python
import asyncio

async def download():
    await asyncio.sleep(2)
    return 12345

async def main():
    task = asyncio.create_task(download())
    print("Launched downloading")
    bytes_loaded = await task  # 스레드 차단 없이 대기
    print(f"Downloaded {bytes_loaded} bytes")

asyncio.run(main())

```
- await는 이벤트 루프를 멈추지 않고 다른 작업을 병행 실행 가능.

## 📊 비교표 (ASCII Markdown)
| 언어    | 동기 대기 메서드         | 비동기 대기 방식       | 차단 여부         | 특징 |
|---------|--------------------------|------------------------|-------------------|------|
| C#      | Task.Wait(), Result       | await                  | await는 차단 안 함 | async/await 내장, CancellationToken 지원 |
| C++     | future.get(), wait()      | co_await (C++20)       | get()/wait() 차단 | 네트워크 async는 Boost.Asio/Qt 필요 |
| Java    | Future.get(), join()      | thenApply, thenAccept  | join/get 차단     | CompletableFuture로 체이닝 가능 |
| Python  | future.result(), run_until_complete | await          | await 차단 안 함 | asyncio 이벤트 루프 기반 |



## 💡 정리
- 동기 wait: 단순하지만 스레드를 멈춤 → UI/이벤트 루프 환경에서는 지양
- 비동기 await: 스레드 차단 없이 다른 작업과 병행 → 고성능 IO 처리에 필수
- C++은 표준만으로는 네트워크 await가 부족해 외부 라이브러리 필요
- Java/Python은 이벤트 루프나 체이닝 API로 자연스럽게 논블로킹 구현 가능
---
