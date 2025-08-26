# 🧠 C#의 static class란?

- **정적 클래스(static class)**는 인스턴스를 만들 수 없는 클래스입니다.
- 모든 멤버가 static이어야 하며, 객체 없이 직접 호출됩니다.
- 주로 유틸리티 함수, 확장 메서드, 공통 기능 모음에 사용됩니다.
```csharp
public static class MathUtils {
    public static int Square(int x) => x * x;
}
```

사용 예:
```csharp
int result = MathUtils.Square(5); // 25
```


## 🔧 Java에는 static class가 있을까?
Java에는 클래스 자체를 static으로 선언하는 문법은 없습니다.
하지만 **내부 클래스(inner class)**는 static으로 선언할 수 있어요.
```java
public class Outer {
    public static class Inner {
        public static void sayHello() {
            System.out.println("Hello from static inner class");
        }
    }
}
```

사용 예:
```java
Outer.Inner.sayHello();
```


## 🧊 C++에는 static class가 없을까?
C++에는 static class라는 개념은 없습니다.
하지만 비슷한 기능을 다음 방식으로 구현할 수 있어요:
- 클래스 내부에 static 멤버 함수만 정의
- 객체를 만들지 않고 클래스 이름으로 직접 호출
```cpp
class MathUtils {
public:
    static int Square(int x) { return x * x; }
};
```

사용 예:
```cpp
int result = MathUtils::Square(5); // 25
```

C++에서는 클래스 자체를 static으로 선언할 수는 없지만,
생성자를 private으로 막고 객체 생성을 금지하는 패턴도 자주 사용됩니다.

## 💡 확장 메서드와 static 클래스의 조합 (C#)
C#에서는 static class를 활용해 확장 메서드를 만들 수 있어요.
당신이 작성한 StringExt.Reverse()는 바로 그 예죠:
```csharp
public static string Reverse(this string str)
```

이렇게 하면 기존 string 타입에 새로운 메서드를 추가한 것처럼 사용할 수 있어요:
```csharp
string reversed = "JungHwanJeong".Reverse();
```


## 🧩 요약 비교

| 언어 | static 클래스 지원 | 대체 방법 또는 유사 개념 |
|------|------------------|-----------------------| 
| C# | ✅ 있음 | 유틸리티, 확장 메서드 | 
| Java | ❌ 없음 | static inner class | 
| C++ | ❌ 없음 | static 멤버 함수 + 객체 생성 제한 | 


---

## ✅ 1. 객체 생성 방지로 설계 명확성 확보
- static class는 인스턴스를 만들 수 없어요.
- 즉, 상태를 가지지 않는 순수 기능 모음이라는 의도를 명확히 전달할 수 있어요.
- 예: Math, Console, Path 같은 .NET의 대표적인 static 클래스들

## 🧼 2. 불변성과 안전성
- 상태를 가지지 않기 때문에 **부작용(side effect)**이 없어요.
- 병렬 처리나 멀티스레딩 환경에서도 안전하게 사용할 수 있어요.

## 🧩 3. 확장 메서드와의 궁합
- C#의 확장 메서드는 반드시 static 클래스에 정의돼야 해요.
- 기존 타입에 기능을 추가하면서도 원래 타입을 건드리지 않기 때문에 **OCP(Open-Closed Principle)**를 만족시켜요.

```cpp
public static class StringExt {
    public static string Reverse(this string str) { ... }
}
```


## 🧠 4. 유틸리티성 코드의 집합
- 공통 기능을 모아두면 코드 재사용성이 높아지고, 유지보수가 쉬워져요.
- 예: Logger.Log(), Validator.IsValidEmail() 등

## 🚫 5. 의도하지 않은 인스턴스화 방지
- 실수로 객체를 생성하거나 상태를 저장하는 걸 막아줘요.
- 특히 팀 프로젝트에서 설계 실수 방지에 효과적이에요.

## ✨ 보너스: 성능 측면
- static 메서드는 런타임 시 인스턴스 메서드보다 약간 더 빠를 수 있어요.
- 물론 차이는 미미하지만, 대규모 시스템에서는 누적 효과가 있을 수 있죠.

---


