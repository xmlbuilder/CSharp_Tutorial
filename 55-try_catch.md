# 🧠 예외 처리란?
프로그래밍에서 **예외(Exception)**는 프로그램 실행 중 발생할 수 있는 비정상적인 상황이나 오류를 의미합니다.
예외 처리를 통해 프로그램이 예기치 못한 상황에서도 안정적으로 동작할 수 있도록 만들 수 있습니다.

## 🔍 if문 vs 예외 처리 메커니즘
| 방식 | 특징 | 단점 |
|-------|--------|---------| 
| if문을 통한 처리 | 조건을 직접 검사하여 오류 방지 | 흐름 제어와 예외 처리가 섞여 있어 가독성 저하 | 
| try-catch를 통한 처리 | 예외 발생 시 별도의 블록에서 처리 | 성능에 약간의 오버헤드가 있음 | 


### ✅ 예외 처리 메커니즘을 사용하면 정상 흐름과 오류 처리 흐름을 명확히 분리할 수 있어 코드 가독성이 향상됩니다.


## ⚙️ C# 예외 처리 흐름
- try 블록에서 예외 발생 가능 코드 실행
- 예외 발생 시 throw를 통해 예외 객체를 던짐
- catch 블록에서 해당 예외를 받아 처리
- 예외가 처리되지 않으면 CLR(Common Language Runtime)이 호출 스택을 역으로 탐색하여 처리 가능한 catch 블록을 찾음
- 끝까지 처리할 수 없으면 스레드를 종료하고 프로그램이 중단됨

## 🧪 코드 분석
```csharp
int b = 0;
try
{
    int a = 10 / b; // 0으로 나누기 → DivideByZeroException 발생
}
catch (Exception ex)
{
    throw; // 예외를 다시 던짐 → 상위 호출자에게 전달
}
```

- int a = 10 / b;에서 0으로 나누기 때문에 DivideByZeroException 발생
- catch 블록에서 throw;를 사용해 예외를 다시 던짐
- 이 경우 Main 메서드가 최상위이므로 CLR은 호출 스택을 역으로 탐색하지만 처리할 블록이 없으면 프로그램 종료

## 🎯 조건식에서의 예외 처리
string first = args.Length >= 1 
    ? args[0] 
    : throw new ArgumentException("Please supply at least one argument.");


- args.Length가 1보다 작으면 throw를 통해 즉시 예외 발생
- 조건식 안에서 throw를 사용할 수 있는 C#의 유용한 기능
- ArgumentException은 잘못된 인자 전달에 대한 예외

## 🧾 전체 흐름 요약
- 프로그램 시작
- b = 0 설정
- 10 / b → 예외 발생
- catch에서 throw → 예외 다시 던짐
- args.Length 검사 → 없으면 ArgumentException 발생
- 예외가 처리되지 않으면 CLR이 스레드 종료

## ✅ 핵심 포인트 정리
- try-catch는 예외 처리 전용 구조로 가독성 향상에 도움
- throw는 예외를 직접 발생시키거나 다시 던질 때 사용
- CLR은 예외 발생 시 호출 스택을 역으로 탐색하여 처리 가능한 블록을 찾음
- 조건식에서도 throw를 사용할 수 있어 간결한 코드 작성 가능
---

## 🧱 C++ 예외 처리 구조
```cpp
try {
    int a = 10 / 0;
} catch (const std::exception& e) {
    std::cerr << "Error: " << e.what();
}
```

- throw: 예외 객체를 던짐 (throw std::runtime_error("message");)
- try: 예외 발생 가능 코드
- catch: 예외 타입에 따라 처리
- C++은 finally가 없고, 대신 RAII(Resource Acquisition Is Initialization) 패턴을 사용해 자원 정리를 함

## ☕ Java 예외 처리 구조
```java
try {
    int a = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());
} finally {
    System.out.println("Always runs");
}
```

- throw new Exception("message"): 예외 발생
- try-catch-finally: 구조는 C#과 거의 동일
- Java는 checked exception과 unchecked exception을 구분함

## 🐍 Python 예외 처리 구조
```python
try:
    a = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
finally:
    print("Always runs")


- raise Exception("message"): 예외 발생
- try-except-finally: 구조는 간결하고 직관적
- Python은 예외가 객체이므로 다양한 정보 포함 가능

## 🔍 비교 요약
| 언어 | 예외 발생 | 예외 처리 | finally 지원 | 특징 | 
|------|---------|----------|-------------|------|
| C# | throw | try-catch | ✅ 있음 | 조건식에서도 throw 가능 | 
| C++ | throw | try-catch | ❌ 없음 | RAII로 자원 정리 | 
| Java | throw | try-catch | ✅ 있음 | checked/unchecked 구분 | 
| Python | raise | try-except | ✅ 있음 | 간결하고 유연한 문법 | 

---



