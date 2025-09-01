# FileSystemWatcher

FileSystemWatcher를 사용해 디렉토리 내 파일의 생성, 변경, 삭제, 이름 변경, 오류 등을 실시간으로 감지하고 처리.

## C# 코드

```cpp
namespace GrammarTest
{
    public class Program
    {
        public static void Main(String[] args)
        {
            using var watcher = new FileSystemWatcher("/Users/jeongjunghwan/Downloads");
            watcher.NotifyFilter = NotifyFilters.Attributes
                                   | NotifyFilters.CreationTime
                                   | NotifyFilters.DirectoryName
                                   | NotifyFilters.FileName
                                   | NotifyFilters.LastAccess
                                   | NotifyFilters.Security
                                   | NotifyFilters.Size;
            watcher.Changed += OnChanged;
            watcher.Deleted += OnDeleted;
            watcher.Created += OnCreated;
            watcher.Renamed += OnRenamed;
            watcher.Error += OnError;
            watcher.Filter = "*";
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            Console.WriteLine("Press enter to exit");
            Console.ReadLine();
        }
        private static void OnChanged(object sender, FileSystemEventArgs arg)
        {
            if (arg.ChangeType != WatcherChangeTypes.Changed)
            {
                return;
            }
            
            Console.WriteLine($"Changed : {arg.FullPath}");
        }
        private static void OnCreated(object sender, FileSystemEventArgs arg)
        {
            Console.WriteLine($"Created : {arg.FullPath}");
        }
        private static void OnDeleted(object sender, FileSystemEventArgs arg)
        {
            Console.WriteLine($"Deleted : {arg.FullPath}");
        }
        private static void OnRenamed(object sender, RenamedEventArgs arg)
        {
            Console.WriteLine($"Renamed : {arg.OldFullPath} -> {arg.FullPath}" );
        }
        private static void OnError(object sender, ErrorEventArgs arg)
        {
            PrintException(arg.GetException());
        }
        private static void PrintException(Exception? ex)
        {
            if (ex != null)
            {
                Console.WriteLine($"Message : {ex.Message}");
                
            }
        }
    }
}
```

## 🐍 Python — watchdog 라이브러리 기반
```python
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler
import time

class MyHandler(FileSystemEventHandler):
    def on_modified(self, event):
        print(f"Changed : {event.src_path}")
    def on_created(self, event):
        print(f"Created : {event.src_path}")
    def on_deleted(self, event):
        print(f"Deleted : {event.src_path}")
    def on_moved(self, event):
        print(f"Renamed : {event.src_path} -> {event.dest_path}")

path = "/Users/jeongjunghwan/Downloads"
observer = Observer()
observer.schedule(MyHandler(), path, recursive=True)
observer.start()

try:
    print("Press Ctrl+C to exit")
    while True:
        time.sleep(1)
except KeyboardInterrupt:
    observer.stop()
observer.join()
```

- watchdog 설치 필요: pip install watchdog
- recursive=True로 하위 디렉토리 포함
- 이벤트 핸들러 메서드로 각각의 변화 감지

## ☕ Java — WatchService (Java NIO)
```java
import java.nio.file.*;
import static java.nio.file.StandardWatchEventKinds.*;

public class Watcher {
    public static void main(String[] args) throws Exception {
        Path path = Paths.get("/Users/jeongjunghwan/Downloads");
        WatchService watchService = FileSystems.getDefault().newWatchService();
        path.register(watchService, ENTRY_CREATE, ENTRY_DELETE, ENTRY_MODIFY);

        System.out.println("Press Ctrl+C to exit");

        while (true) {
            WatchKey key = watchService.take();
            for (WatchEvent<?> event : key.pollEvents()) {
                WatchEvent.Kind<?> kind = event.kind();
                Path changed = (Path) event.context();
                System.out.println(kind.name() + " : " + changed);
            }
            key.reset();
        }
    }
}
```

- WatchService는 단일 디렉토리만 감지 (하위 디렉토리는 별도 등록 필요)
- 이벤트 종류: ENTRY_CREATE, ENTRY_DELETE, ENTRY_MODIFY

## 💻 C++ — std::filesystem + OS별 감지 API
C++ 표준에는 실시간 감지 기능이 없기 때문에 OS별 API 또는 라이브러리를 사용해야 합니다.  
아래는 Linux 기준 inotify를 사용한 예시입니다:
```cpp
#include <iostream>
#include <sys/inotify.h>
#include <unistd.h>
#include <limits.h>

int main() {
    int fd = inotify_init();
    int wd = inotify_add_watch(fd, "/Users/jeongjunghwan/Downloads", IN_CREATE | IN_DELETE | IN_MODIFY | IN_MOVED_FROM | IN_MOVED_TO);

    char buffer[4096];
    std::cout << "Press Ctrl+C to exit\n";

    while (true) {
        int length = read(fd, buffer, sizeof(buffer));
        int i = 0;
        while (i < length) {
            struct inotify_event *event = (struct inotify_event *)&buffer[i];
            if (event->len) {
                if (event->mask & IN_CREATE)
                    std::cout << "Created : " << event->name << std::endl;
                if (event->mask & IN_DELETE)
                    std::cout << "Deleted : " << event->name << std::endl;
                if (event->mask & IN_MODIFY)
                    std::cout << "Changed : " << event->name << std::endl;
                if (event->mask & IN_MOVED_FROM || event->mask & IN_MOVED_TO)
                    std::cout << "Renamed : " << event->name << std::endl;
            }
            i += sizeof(struct inotify_event) + event->len;
        }
    }

    inotify_rm_watch(fd, wd);
    close(fd);
    return 0;
}
```

## 💻 C++ (Windows) — ReadDirectoryChangesW 사용 예시
```cpp
#include <windows.h>
#include <iostream>

int main() {
    std::wstring directory = L"C:\\Users\\junghwanjeong\\Downloads";

    HANDLE hDir = CreateFileW(
        directory.c_str(),
        FILE_LIST_DIRECTORY,
        FILE_SHARE_READ | FILE_SHARE_WRITE | FILE_SHARE_DELETE,
        nullptr,
        OPEN_EXISTING,
        FILE_FLAG_BACKUP_SEMANTICS,
        nullptr
    );

    if (hDir == INVALID_HANDLE_VALUE) {
        std::cerr << "Failed to open directory." << std::endl;
        return 1;
    }

    char buffer[1024];
    DWORD bytesReturned;

    std::cout << "Watching for changes... Press Ctrl+C to exit\n";

    while (true) {
        BOOL success = ReadDirectoryChangesW(
            hDir,
            &buffer,
            sizeof(buffer),
            TRUE, // TRUE = watch subdirectories
            FILE_NOTIFY_CHANGE_FILE_NAME |
            FILE_NOTIFY_CHANGE_DIR_NAME |
            FILE_NOTIFY_CHANGE_ATTRIBUTES |
            FILE_NOTIFY_CHANGE_SIZE |
            FILE_NOTIFY_CHANGE_LAST_WRITE |
            FILE_NOTIFY_CHANGE_CREATION,
            &bytesReturned,
            nullptr,
            nullptr
        );

        if (!success) {
            std::cerr << "ReadDirectoryChangesW failed." << std::endl;
            break;
        }

        FILE_NOTIFY_INFORMATION* info = reinterpret_cast<FILE_NOTIFY_INFORMATION*>(buffer);
        std::wstring fileName(info->FileName, info->FileNameLength / sizeof(WCHAR));

        switch (info->Action) {
            case FILE_ACTION_ADDED:
                std::wcout << L"Created : " << fileName << std::endl;
                break;
            case FILE_ACTION_REMOVED:
                std::wcout << L"Deleted : " << fileName << std::endl;
                break;
            case FILE_ACTION_MODIFIED:
                std::wcout << L"Changed : " << fileName << std::endl;
                break;
            case FILE_ACTION_RENAMED_OLD_NAME:
                std::wcout << L"Renamed from : " << fileName << std::endl;
                break;
            case FILE_ACTION_RENAMED_NEW_NAME:
                std::wcout << L"Renamed to : " << fileName << std::endl;
                break;
        }
    }

    CloseHandle(hDir);
    return 0;
}
```


## ✅ 주요 특징
- 하위 디렉토리 감지 가능 (TRUE 설정)
- 다양한 이벤트 감지: 생성, 삭제, 수정, 이름 변경 등
- 유니코드 경로 지원 (std::wstring 사용)
- 고성능이지만 비동기 처리나 오버랩 I/O를 쓰면 더 안정적

### 📌 요약: C++에서 Windows 감지 방식
| 항목               | 내용                                 |
|--------------------|--------------------------------------|
| 감지 방식          | `ReadDirectoryChangesW`              |
| 하위 디렉토리 감지 | `TRUE` 설정으로 가능                 |
| 감지 이벤트 종류   | `FILE_NOTIFY_CHANGE_*` 플래그 사용   |
| 지원 이벤트        | 생성, 삭제, 수정, 이름 변경 등       |
| 난이도             | 중간~높음 (Win32 API 직접 사용 필요) |


- macOS에서는 FSEvents, Windows에서는 ReadDirectoryChangesW 사용
- 실무에서는 Boost.Filesystem이나 FileSystemWatcher 같은 라이브러리 활용 권장


### 📌 파일 시스템 감지 기능 비교 요약
| 언어/플랫폼 | 감지 방식           | 하위 디렉토리 지원 | 감지 이벤트 종류                            | 난이도 |
|-------------|---------------------|---------------------|---------------------------------------------|--------|
| C#         | `FileSystemWatcher` | ✅                  | Created, Deleted, Changed, Renamed, Error  | 쉬움  |
| Python     | `watchdog`          | ✅                  | Created, Deleted, Modified, Moved          | 쉬움  |
| Java       | `WatchService`      | ❌ (직접 등록 필요) | ENTRY_CREATE, ENTRY_DELETE, ENTRY_MODIFY    | 중간  |
| C++        | `inotify`/`FSEvents`/`ReadDirectoryChangesW`| ❌/✅ | OS별로 다름 (Create, Modify, Delete, Move 등)|어려움|

---




