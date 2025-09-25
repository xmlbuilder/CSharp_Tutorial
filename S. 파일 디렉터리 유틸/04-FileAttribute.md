# C# File Attribute
 C#에서 파일 속성을 가져오고 수정하는 예제

## ✅ C#
```csharp
string strPath = "/home/jhjeong/test.txt";
FileAttributes attributes = File.GetAttributes(strPath);
if ((attributes & FileAttributes.Hidden) == FileAttributes.Hidden)
{
    // Show the file.
    attributes = RemoveAttribute(attributes, FileAttributes.Hidden);
    File.SetAttributes(strPath, attributes);
    Console.WriteLine("The {0} file is no longer hidden.", strPath);
}
else
{
    // Hide the file.
    File.SetAttributes(strPath, File.GetAttributes(strPath) | FileAttributes.Hidden);
    Console.WriteLine("The {0} file is now hidden.", strPath);
}

private static FileAttributes RemoveAttribute(FileAttributes attributes, FileAttributes attributesToRemove)
{
    return attributes & ~attributesToRemove;
}

```

- File.GetAttributes()로 속성 가져오기
- FileAttributes.Hidden 플래그로 숨김 여부 확인
- File.SetAttributes()로 속성 수정

## 🧠 C++ (Windows 전용, <windows.h> 사용)
```cpp
#include <iostream>
#include <windows.h>

int main() {
    std::string path = "C:\\Users\\jhjeong\\test.txt";
    DWORD attributes = GetFileAttributes(path.c_str());

    if (attributes == INVALID_FILE_ATTRIBUTES) {
        std::cerr << "파일을 찾을 수 없습니다.\n";
        return 1;
    }

    if (attributes & FILE_ATTRIBUTE_HIDDEN) {
        // 숨김 해제
        attributes &= ~FILE_ATTRIBUTE_HIDDEN;
        std::cout << path << " 파일의 숨김 속성을 제거했습니다.\n";
    } else {
        // 숨김 설정
        attributes |= FILE_ATTRIBUTE_HIDDEN;
        std::cout << path << " 파일을 숨김 처리했습니다.\n";
    }

    SetFileAttributes(path.c_str(), attributes);
    return 0;
}
```


## ☕ Java (Unix 기반에서는 숨김 속성은 파일명으로 처리됨)
```java
import java.io.File;
import java.nio.file.*;
import java.nio.file.attribute.*;

public class FileHiddenToggle {
    public static void main(String[] args) throws Exception {
        Path path = Paths.get("/home/jhjeong/test.txt");

        boolean isHidden = Files.isHidden(path);
        System.out.println("현재 숨김 상태: " + isHidden);

        if (System.getProperty("os.name").startsWith("Windows")) {
            DosFileAttributeView view = Files.getFileAttributeView(path, DosFileAttributeView.class);
            if (view != null) {
                view.setHidden(!isHidden);
                System.out.println("숨김 상태를 " + (!isHidden) + "로 변경했습니다.");
            }
        } else {
            // Unix에서는 파일명 앞에 '.' 붙이면 숨김
            File file = path.toFile();
            String newName = isHidden ? "test.txt" : ".test.txt";
            file.renameTo(new File(file.getParent(), newName));
            System.out.println("파일명을 변경하여 숨김 처리했습니다.");
        }
    }
}

```

## 🐍 Python (Windows와 Unix 모두 지원)
```python
import os
import platform

path = "/home/jhjeong/test.txt"

def is_hidden(filepath):
    if platform.system() == "Windows":
        import ctypes
        attrs = ctypes.windll.kernel32.GetFileAttributesW(str(filepath))
        return attrs & 2  # FILE_ATTRIBUTE_HIDDEN
    else:
        return os.path.basename(filepath).startswith('.')

def toggle_hidden(filepath):
    if platform.system() == "Windows":
        import ctypes
        attrs = ctypes.windll.kernel32.GetFileAttributesW(str(filepath))
        if attrs & 2:
            # 숨김 해제
            ctypes.windll.kernel32.SetFileAttributesW(str(filepath), attrs & ~2)
            print(f"{filepath} 숨김 해제됨")
        else:
            # 숨김 설정
            ctypes.windll.kernel32.SetFileAttributesW(str(filepath), attrs | 2)
            print(f"{filepath} 숨김 설정됨")
    else:
        dirname = os.path.dirname(filepath)
        basename = os.path.basename(filepath)
        if basename.startswith('.'):
            newname = basename[1:]
        else:
            newname = '.' + basename
        newpath = os.path.join(dirname, newname)
        os.rename(filepath, newpath)
        print(f"{filepath} → {newpath} 이름 변경으로 숨김 처리됨")

toggle_hidden(path)
```


## 🔍 요약 비교
| 언어     | 숨김 여부 확인                         | 숨김 설정/해제                          |
|----------|----------------------------------------|----------------------------------------|
| C#       | `File.GetAttributes()`                 | `File.SetAttributes()`                 |
| C++      | `GetFileAttributes()`                  | `SetFileAttributes()`                  |
| Java     | `Files.isHidden()`                     | `DosFileAttributeView.setHidden()`     |
| Python   | `os.path.basename().startswith('.')`   | (Unix: 파일명 변경)<br>(Windows: ctypes로 속성 설정) |

---


