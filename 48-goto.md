# C#에서 `goto` 사용 사례와 의미

| 케이스 | 위치                     | 설명                                                                 |
|--------|--------------------------|----------------------------------------------------------------------|
| Case 1 | `InfiniteHello()`        | 무한 루프 생성. `goto sayHello;`로 레이블 반복 호출 → CPU 100% 사용 가능성 |
| Case 2 | `switch` 문 내           | `goto case Permissions.Read;` 사용 → 다른 case 블록으로 흐름 이동         |
| Case 3 | 예외 재시도 루프         | `retry:` 레이블과 `goto retry;`로 예외 발생 시 최대 3회 재시도 구현       |

## 💡 goto를 쓰면 안 되는 이유
- 가독성 저하: 흐름이 명확하지 않아 유지보수가 어려워짐
- 디버깅 난이도 증가: 예외나 버그 발생 시 추적이 복잡해짐
- 구조적 프로그래밍 위반: 함수, 루프, 조건문 중심의 구조적 흐름을 깨뜨림

## ✅ 그래도 쓸 수 있는 경우
- switch 문 내 goto case 또는 goto default
- 복잡한 예외 재시도 로직 (단, while이나 for로 대체 가능)
- 성능이 극도로 중요한 루프 (하지만 거의 쓰지 않음)

## 🔁 다중 for문 탈출 방법 비교
| 언어   | 방법 1: 플래그 변수 사용         | 방법 2: 함수로 분리 + return | 방법 3: 예외 활용 (비추천)       | 방법 4: goto (C++/C#만 가능)     |
|--------|----------------------------------|------------------------------|----------------------------------|----------------------------------|
| C#     | `breakOuter = true;`로 제어      | 루프를 함수로 분리 후 `return` | `throw` + `catch`로 흐름 제어     | `goto label;`로 루프 탈출         |
| Java   | `breakOuter = true;`로 제어      | 루프를 함수로 분리 후 `return` | `throw` + `catch`로 흐름 제어     | ❌ `goto` 없음                   |
| C++    | `breakOuter = true;`로 제어      | 루프를 함수로 분리 후 `return` | `throw` + `catch`로 흐름 제어     | `goto label;`로 루프 탈출         |



## 💡 예시 코드들

### ✅ C# – goto 사용 예시
```csharp
for (int i = 0; i < 5; i++)
{
    for (int j = 0; j < 5; j++)
    {
        if (i + j > 6)
            goto EndLoop;
    }
}
EndLoop:
Console.WriteLine("탈출 완료");
```


### ✅ Java – 함수 분리로 탈출
```java
public static void runLoop() {
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            if (i + j > 6)
                return;
        }
    }
    System.out.println("탈출 완료");
}
```

### ✅ C++ – goto 사용 예시
```cpp
for (int i = 0; i < 5; ++i)
{
    for (int j = 0; j < 5; ++j)
    {
        if (i + j > 6)
            goto EndLoop;
    }
}
EndLoop:
std::cout << "탈출 완료\n";
```


## 🚨 주의할 점
- goto는 가독성과 유지보수에 악영향을 줄 수 있어요. 정말 필요한 경우에만!
- 함수 분리는 가장 깔끔하고 안전한 방법이에요.
- 예외를 흐름 제어용으로 쓰는 건 권장되지 않아요 (성능 저하 + 의미 불명확)



## ✅ 다중 for문 탈출 방법 (goto 없이)
| 언어   | 방법 1: 플래그 변수 사용       | 방법 2: 함수 분리 + return       | 방법 3: break label (Java만 가능) |
|--------|-------------------------------|----------------------------------|-----------------------------------|
| C#     | `bool exit = false;`로 제어   | 루프를 함수로 분리 후 `return`   | ❌ 지원 안 함                     |
| Java   | `boolean exit = false;` 사용  | 루프를 함수로 분리 후 `return`   | `break outer;`로 라벨 탈출 가능   |
| C++    | `bool exit = false;` 사용     | 루프를 함수로 분리 후 `return`   | ❌ 라벨 break 없음                |



## 🔍 예시 코드
### 🔹 C# – 플래그 변수 사용
```csharp
bool exit = false;
for (int i = 0; i < 5 && !exit; i++)
{
    for (int j = 0; j < 5; j++)
    {
        if (i + j > 6)
        {
            exit = true;
            break;
        }
    }
}
Console.WriteLine("탈출 완료");
```

### 🔹 Java – 라벨 사용 (break label)
```java
outer:
for (int i = 0; i < 5; i++)
{
    for (int j = 0; j < 5; j++)
    {
        if (i + j > 6)
            break outer;
    }
}
System.out.println("탈출 완료");
```

🔹 C++ – 함수 분리로 탈출
```cpp
void runLoop() {
    for (int i = 0; i < 5; ++i)
    {
        for (int j = 0; j < 5; ++j)
        {
            if (i + j > 6)
                return;
        }
    }
    std::cout << "탈출 완료\n";
}
```



## 💡 추천 방식
- 함수 분리 + return: 가장 깔끔하고 테스트하기 쉬운 구조입니다.
- Java의 라벨 break: 유일하게 언어 차원에서 다중 루프 탈출을 지원합니다.
- 플래그 변수: 모든 언어에서 사용 가능하며, 구조적 흐름을 유지할 수 있습니다.


