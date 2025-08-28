# 🧾 설명 요약
- System.IO.Stream은 추상 클래스로, 모든 스트림의 기반이 됩니다.
- FileStream, MemoryStream, NetworkStream, SqlFileStream은 이를 상속하여 각각의 입출력 시나리오에 맞게 구현됩니다.
- System.Object → MarshalByRefObject → Stream 순으로 상속 계층이 이어집니다.
- 각 스트림 클래스는 Read, Write 메서드를 통해 byte[] 배열을 다룹니다.

```
using System.IO 선언

// FileStream
// 파일 입출력을 다루는 기본 클래스

System.Object 
    System.MarshalByRefObject
        System.IO.Stream
            System.IO.FileStream

//byte[] 배열로 데이터를 읽거나 저장함.

```

```mermaid
classDiagram
    System_Object <|-- System_MarshalByRefObject
    System_MarshalByRefObject <|-- System_IO_Stream
    System_IO_Stream <|-- System_IO_FileStream
    System_IO_Stream <|-- System_IO_MemoryStream
    System_IO_Stream <|-- System_IO_NetworkStream
    System_IO_Stream <|-- System_IO_SqlFileStream

    class System_Object {
    }

    class System_MarshalByRefObject {
    }

    class System_IO_Stream {
        <<abstract>>
        +Read(byte[])
        +Write(byte[])
        +Seek()
        +Flush()
    }

    class System_IO_FileStream {
        +FileStream(path, mode)
        +Read(byte[])
        +Write(byte[])
    }

    class System_IO_MemoryStream {
        +MemoryStream()
        +Read(byte[])
        +Write(byte[])
    }

    class System_IO_NetworkStream {
        +NetworkStream(socket)
        +Read(byte[])
        +Write(byte[])
    }

    class System_IO_SqlFileStream {
        +SqlFileStream(path, transactionContext)
        +Read(byte[])
        +Write(byte[])
    }

```
