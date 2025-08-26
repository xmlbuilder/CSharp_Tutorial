# sealed
sealed(C#), final(Java, C++) 키워드는 상속과 오버라이딩을 제어하는 데 사용되는 제한자로, 
객체지향 설계에서 매우 중요한 역할을 합니다. 
아래에 각 언어별로 클래스와 메서드에 적용되는 방식, 차이점, 사용 목적을 자세히 정리해드릴게요.

## 🔒 sealed / final 키워드 핵심 개념 요약

| 언어   | 키워드       | 적용 대상       | 효과 및 설명                                                  |
|--------|--------------|------------------|---------------------------------------------------------------|
| C#     | `sealed`     | 클래스            | 해당 클래스를 다른 클래스가 상속할 수 없도록 제한             |
| C#     | `sealed`     | 메서드 (override) | 해당 메서드를 자식 클래스에서 다시 오버라이딩하지 못하게 함   |
| Java   | `final`      | 클래스            | 해당 클래스를 상속할 수 없도록 제한                           |
| Java   | `final`      | 메서드            | 해당 메서드를 오버라이딩할 수 없도록 제한                     |
| C++11+ | `final`      | 클래스            | 해당 클래스를 상속할 수 없도록 제한                           |
| C++11+ | `final`      | 메서드 (virtual)  | 해당 가상 메서드를 파생 클래스에서 오버라이딩하지 못하게 함   |




## 🔹 C#에서 sealed 사용 방식
### 1. 클래스에 적용
```csharp
sealed class B : A {}
// class C : B {} // ❌ 컴파일 에러: B는 sealed 클래스

```
- sealed 클래스는 더 이상 상속할 수 없음
- 보안, 안정성, 설계 고정 목적
### 2. 메서드에 적용
```csharp
public class Parent
{
    public sealed override string ToString()
    {
        return "Do not override me";
    }
}
```

- sealed override는 자식 클래스에서 재오버라이딩 금지
- 상속은 허용하되, 특정 메서드만 고정하고 싶을 때 사용

## ☕ Java에서 final 사용 방식
### 1. 클래스에 적용
```java
final class B extends A {}
// class C extends B {} // ❌ 컴파일 에러
```


- final 클래스는 상속 불가
- 대표 예: java.lang.String은 final 클래스
### 2. 메서드에 적용
```java
public class Parent {
    public final String toString() {
        return "Do not override me";
    }
}
```

- final 메서드는 오버라이딩 불가
- 보안, API 안정성, 예측 가능한 동작 보장

## 💻 C에서 final 사용 방식 (C11 이후)
### 1. 클래스에 적용
```cpp
class Base1 final {};
// class Derived1 : public Base1 {}; // ❌ 컴파일 에러
```

- final 클래스는 상속 불가
- 문법적으로 final은 클래스 이름 뒤에 붙임
### 2. 메서드에 적용
```cpp
class Base2 {
public:
    virtual int func() final;
};
```

- final 메서드는 오버라이딩 불가
- override와 함께 사용하면 명시적이고 안전

## 🔍 비교 요약표 (Markdown)
# 🔒 sealed / final 키워드 비교 요약

| 언어   | 클래스 상속 제한 키워드 | 메서드 오버라이딩 제한 키워드 | 특징 및 주의사항                          |
|--------|--------------------------|-------------------------------|-------------------------------------------|
| C#     | `sealed`                 | `sealed override`             | 클래스 또는 메서드에 적용 가능             |
| Java   | `final`                  | `final`                       | 클래스 또는 메서드에 적용 가능             |
| C++    | `final` (C++11 이상)     | `final`                       | 클래스 이름 뒤 또는 메서드 선언 뒤에 사용  |



## ✨ 언제 사용하나요?
- 보안: 외부에서 상속하거나 오버라이딩해서 기능을 변경하지 못하게
- 설계 고정: 특정 클래스나 메서드는 더 이상 확장되지 않도록
- 성능 최적화: 일부 컴파일러는 final/sealed를 통해 가상 테이블 최적화 가능
---
