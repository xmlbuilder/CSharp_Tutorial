# C#의 NamedPipeServerStream과 NamedPipeClientStream
프로세스 간 통신(IPC: Inter-Process Communication)을 위한 대표적인 방식 중 하나죠.
이 개념은 C++의 Shared Memory와 유사한 목적을 갖고 있으며, Java와 Python에서도 비슷한 기능을 제공하는 구조들이 존재합니다.
아래는 각 언어별로 Named Pipe 또는 Shared Memory와 유사한 IPC 방식을 비교하고, 그 개념과 사용법을 자세히 설명한 내용입니다.


### 🔑 IPC 핵심 개념 비교

| 개념            | 설명                                                                 | 장점                                      | 단점                                      | 사용 예시                   |
|-----------------|---------------------------------------------|--------------------------------|--------------------------------|----------------------------|
| Named Pipe      | OS가 관리하는 이름 있는 파이프. 프로세스 간에 바이트 스트림을 주고받음 | 구현 간단, OS 지원, 동기식 통신 가능    | 로컬 전용, 구조적 메시지 처리 어려움       | C# `NamedPipeServerStream`, Windows API |
| Shared Memory   | 여러 프로세스가 동일한 메모리 공간을 공유하여 데이터 교환    | 매우 빠른 속도, 대용량 데이터 처리 가능 | 동기화 필요, 구현 복잡   | C++ `shmget`, Python `shared_memory` |
| Socket          | 네트워크 기반 통신. 로컬 또는 원격 프로세스 간 데이터 교환 가능   | 유연한 구조, 원격 통신 가능        | 상대적으로 느림, 연결 관리 필요  | Java `Socket`, Python `socket` |
| Message Queue   | 메시지를 큐에 넣고 꺼내는 방식. 비동기 통신에 적합   | 비동기 처리, 구조적 메시지 교환 가능  | 큐 관리 필요, 지연 발생 가능  | POSIX MQ, RabbitMQ, ZeroMQ     |
| Memory-Mapped File | 파일을 메모리에 매핑하여 공유. Shared Memory의 파일 기반 버전   | 파일 기반 공유, OS 지원  | 파일 I/O 성능에 의존 | Java `MappedByteBuffer`, C# `MemoryMappedFile` |


## 📘 C# – Named Pipe 예시
C#에서는 NamedPipeServerStream과 NamedPipeClientStream을 사용하여 동일 머신 내 프로세스 간 통신을 구현할 수 있습니다.
```csharp
// 서버
using (var server = new NamedPipeServerStream("mypipe"))
{
    server.WaitForConnection();
    server.WriteByte(100);
    Console.WriteLine(server.ReadByte());
}

// 클라이언트
using (var client = new NamedPipeClientStream("mypipe"))
{
    client.Connect();
    Console.WriteLine(client.ReadByte());
    client.WriteByte(200);
}
```

- 장점: 간단한 API, OS 수준에서 안정적 관리
- 단점: 네트워크 통신은 불가능 (로컬 IPC 전용)

## 📗 C++ – Shared Memory 예시
C++에서는 POSIX 또는 Windows API를 통해 Shared Memory를 사용할 수 있습니다.
```cpp
// POSIX 예시 (Linux)
int shm_id = shmget(IPC_PRIVATE, 1024, IPC_CREAT | 0666);
char* shm_ptr = (char*)shmat(shm_id, nullptr, 0);
strcpy(shm_ptr, "Hello from process A");
```

- shmget, shmat, shmdt 등을 사용
- 빠른 데이터 교환이 가능하지만 동기화(세마포어 등) 필요

## 📙 Java – 유사 개념
Java에는 Named Pipe가 직접적으로 존재하지 않지만, 다음과 같은 방식으로 유사한 IPC를 구현할 수 있습니다:
```java
### 1. Socket (로컬 통신)
// 서버
ServerSocket server = new ServerSocket(9999);
Socket socket = server.accept();
InputStream in = socket.getInputStream();
System.out.println(in.read());

// 클라이언트
Socket client = new Socket("localhost", 9999);
OutputStream out = client.getOutputStream();
out.write(100);
```

- Named Pipe처럼 동작 가능
- 멀티 플랫폼, 네트워크 확장 가능

### 2. Memory-Mapped File (Shared Memory 유사)
```java
RandomAccessFile file = new RandomAccessFile("shared.dat", "rw");
FileChannel channel = file.getChannel();
MappedByteBuffer buffer = channel.map(FileChannel.MapMode.READ_WRITE, 0, 1024);
buffer.put((byte) 123);
```


- MappedByteBuffer를 통해 파일 기반 공유 메모리 구현 가능

## 🐍 Python – 유사 개념
Python에서는 다양한 IPC 방식이 존재하며, 특히 multiprocessing 모듈이 강력합니다.
### 1. Pipe
```python
from multiprocessing import Pipe, Process

def worker(conn):
    conn.send(100)
    print(conn.recv())

parent_conn, child_conn = Pipe()
p = Process(target=worker, args=(child_conn,))
p.start()
print(parent_conn.recv())
parent_conn.send(200)
p.join()
```

- 양방향 통신 가능
- Named Pipe와 유사한 구조
### 2. Shared Memory
```python
from multiprocessing import shared_memory

shm = shared_memory.SharedMemory(create=True, size=10)
shm.buf[0] = 42
print(shm.buf[0])
shm.close()
shm.unlink()
```

- Python 3.8+부터 shared_memory 모듈 제공
- 빠른 데이터 교환, 동기화는 직접 구현 필요


## 🔄 IPC 방식 비교 – Named Pipe vs Shared Memory

| 언어   | Named Pipe 유사 방식         | Shared Memory 유사 방식             | 특징 및 설명                                  |
|--------|------------------------------|-------------------------------------|-----------------------------------------------|
| C#     | `NamedPipeServerStream`      | `MemoryMappedFile`                 | OS 지원 Named Pipe, 파일 기반 공유 메모리     |
| C++    | `CreateNamedPipe` (Windows)  | `shmget`, `mmap`, `CreateFileMapping` | 고성능, 동기화 필요                            |
| Java   | `Socket`, `PipedInputStream` | `MappedByteBuffer`                 | 네트워크 확장 가능, 파일 기반 공유 메모리     |
| Python | `multiprocessing.Pipe`       | `multiprocessing.shared_memory`    | 간단한 API, 프로세스 간 통신에 적합           |

---
