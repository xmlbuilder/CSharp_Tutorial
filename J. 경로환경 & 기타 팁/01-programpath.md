# 📦 실행 파일 경로 얻기 - C#, Java 정리

## 🧑‍💻 C#
| 목적               | 코드 예시                  | 설명              |
|--------------------|--------------------------------------|-----------------------------|
| 실행 파일 경로     | `Assembly.GetExecutingAssembly().Location`   | 현재 실행 중인 어셈블리의 전체 경로       |
| 실행 디렉토리      | `Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location)` | 실행 파일이 위치한 디렉토리 경로          |
| .NET Core 대안     | `AppContext.BaseDirectory`     | .NET Core에서 안정적인 실행 디렉토리 경로 |



## ☕ Java
| 목적        | 코드 예시                 | 설명                   |
|--------------------|--------------------------------|-------------------------------|
| 실행 클래스 경로| `MyClass.class.getProtectionDomain().getCodeSource().getLocation().getPath()` |`.jar` 또는 클래스 파일의 경로 반환 |
| 현재 작업 디렉토리 | `new File(".").getCanonicalPath()`      | 현재 작업 디렉토리 (실행 위치와 다를 수 있음) |
| JVM 시작 디렉토리  | `System.getProperty("user.dir")`        | JVM이 시작된 디렉토리 경로                |


## 🧠 요약 비교
| 언어   | 실행 파일 경로 제공 | 실행 디렉토리 제공 | 표준 API 여부 |
|--------|----------------------|---------------------|----------------|
| C++    | ❌ (OS API 필요)     | ✅ (`filesystem`)    | ❌             |
| C#     | ✅                   | ✅                   | ✅             |
| Java   | ✅                   | ✅                   | ✅             |




