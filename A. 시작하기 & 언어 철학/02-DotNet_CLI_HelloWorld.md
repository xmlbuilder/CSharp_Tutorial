# .NET CLI(Command Line Interface)로 Hello World 만들기

## 1. 작업 폴더 생성

``` bash
mkdir hello_world
cd hello_world
```

-   프로젝트를 담을 디렉터리를 새로 만들고 이동합니다.


## 2. 콘솔 애플리케이션 템플릿 생성

``` bash
dotnet new console
```

- `Program.cs` 기본 파일과 `.csproj` 프로젝트 파일이 생성됩니다.

- 출력 예시:
```
The template "Console Application" was created successfully.
```

## 3. 빌드(Build)

### Release 빌드

``` bash
dotnet build -c Release
```

- 최적화된 실행 파일 생성
- 기본 출력 위치: `bin/Release/netX.X/`

### Debug 빌드

``` bash
dotnet build -c Debug
```

- 디버깅용 심볼과 함께 빌드
- 기본 출력 위치: `bin/Debug/netX.X/`

## 4. 실행(Run)

빌드 없이 바로 실행:

``` bash
dotnet run
```

-   `Program.cs`의 `Main` 함수가 실행됨
-   출력: `Hello, World!`

Release/Debug 지정 실행:

``` bash
dotnet run --configuration Release
```

## 5. 실행 파일 직접 실행

빌드 후 생성된 실행 파일 직접 실행 가능:

Linux/macOS:

``` bash
./bin/Release/net8.0/hello_world
```

Windows PowerShell:

``` powershell
.\bin\Release\net8.0\hello_world.exe
```



## 6. 추가 명령어

- **패키지 추가 (NuGet)**

    ``` bash
    dotnet add package Newtonsoft.Json
    ```

- **의존성 복원**

    ``` bash
    dotnet restore
    ```

- **클린 빌드**

    ``` bash
    dotnet clean
    ```

- **출력 디렉토리 지정**

    ``` bash
    dotnet build -o ./out
    ```

## 7. Debug vs Release 비교

| 구분         | Debug       | Release     |
|--------------|-------------|-------------|
| 최적화       | ❌ 없음      | ✅ 있음      |
| 디버깅 심볼  | ✅ 포함      | ❌ 제거      |
| 실행 속도    | 느림         | 빠름         |
| 주요 용도    | 개발/테스트  | 실제 배포    |

## 결론
- .NET SDK가 설치되어 있으면 **IDE 없이도 CLI만으로 프로젝트 생성, 빌드, 실행, 배포** 까지 가능하다.

---

