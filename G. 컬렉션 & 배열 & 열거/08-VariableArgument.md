# 🧠 C#의 가변 인수: params 키워드
```csharp
private static double Median(params double[] args)
```

- params 키워드를 사용하면 배열처럼 여러 개의 인수를 넘길 수 있음
- 반드시 마지막 매개변수로 선언해야 하며, 1차원 배열 형태여야 함
- params object[]를 사용하면 다양한 타입을 받을 수 있음

## 📌 예시:
```csharp
WriteLog("Time : {0}, Message {1}, User {2}", 1, "Sample", 2.0);
```


## 🧠 C++의 가변 인수: <cstdarg> 헤더와 va_list
### 1. 개수 전달 방식
```cpp
void showVar(int count, ...) {
    va_list vl;
    va_start(vl, count);
    for (int i = 0; i < count; i++) {
        int val = va_arg(vl, int);
        std::cout << val << std::endl;
    }
    va_end(vl);
}
```

- va_list, va_start, va_arg, va_end를 사용
- 첫 번째 인자로 개수를 전달하여 반복 처리

### 2. 종료 값 방식
```cpp
void showUntilNull(const char* first, ...) {
    va_list vl;
    va_start(vl, first);
    const char* str = first;
    while (str != nullptr) {
        std::cout << str << std::endl;
        str = va_arg(vl, const char*);
    }
    va_end(vl);
}
```


- 마지막 인자에 NULL 같은 종료 조건을 넣어 반복
- 타입이 고정되어야 하므로 유연성은 떨어짐

## 🧠 Java의 가변 인수: ... 문법
```java
public static int sum(int... numbers) {
    int total = 0;
    for (int num : numbers) {
        total += num;
    }
    return total;
}
```

- int...처럼 ... 문법을 사용해 배열처럼 처리
- 내부적으로는 배열로 변환되어 동작
- 역시 마지막 매개변수로만 사용 가능
📌 다양한 타입도 가능:
```java
public static void log(String format, Object... args) {
    System.out.printf(format, args);
}
```


## 🔍 세 언어 비교 요약
| 언어 | 키워드/문법 | 타입 제한 | 유연성 | 내부 처리 방식 |
|-----|------------|----------|-------|--------------| 
| C# | params | 배열 타입 | 높음 | 배열로 처리 | 
| C++ | <cstdarg> 사용 | 고정 타입 | 낮음 | va_list로 처리 | 
| Java | ... | 배열 타입 | 높음 | 배열로 처리 | 




## 🚀 C++11 이후의 ... 문법: Parameter Pack
### 1. 함수 템플릿에서 가변 인수 받기
```cpp
#include <iostream>

template<typename... Args>
void printAll(Args... args) {
    (std::cout << ... << args) << std::endl;
}
```

- Args...는 타입의 패키지 (parameter pack)
- args...는 값의 패키지 (argument pack)
- (std::cout << ... << args)는 fold expression으로 모든 인수를 출력
#### 📌 사용 예:
```cpp
printAll(1, "hello", 3.14);  // 출력: 1hello3.14
```


### 2. 재귀적으로 처리하기 (C++11 방식)
```cpp
template<typename T>
void printOne(T t) {
    std::cout << t << std::endl;
}

template<typename T, typename... Rest>
void printAll(T first, Rest... rest) {
    printOne(first);
    printAll(rest...);
}
```

- C++17 이전에는 재귀 템플릿으로 처리했음
- Rest...는 나머지 인수들을 다시 넘김

### ✅ 기존 va_list 방식과의 차이점
| 항목 | va_list 방식 | ... 템플릿 방식 (C++11~) | 
|-----|--------------|------------------------|
| 타입 안전성 | 없음 | 있음 | 
| 타입 다양성 | 고정 타입만 가능 | 어떤 타입이든 가능 | 
| 컴파일 시 오류 탐지 | 어려움 | 쉬움 | 
| 사용 편의성 | 복잡함 | 간결함 | 



### 💡 실전 예제: 포맷팅 함수 만들기
```cpp
template<typename... Args>
void log(const std::string& format, Args... args) {
    // 실제 구현은 std::format (C++20) 또는 fmt 라이브러리 사용
    std::cout << "Formatted log: " << format << std::endl;
}
```
---





