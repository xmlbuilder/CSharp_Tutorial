# 🧠 default의 의미 비교: Java vs C#

| 언어   | 개념                      | 설명                                                                 |
|--------|---------------------------|----------------------------------------------------------------------|
| Java   | `default` 메서드 (interface) | 인터페이스에 **구현이 포함된 메서드**를 정의할 때 사용 (`default void method()`) |
| C#     | `default(T)` 표현식         | 값 형식의 **기본값을 생성**할 때 사용 (`default(MyStruct)` → 모든 필드 초기화됨) |
| C#     | 인터페이스 default 구현     | C# 8.0부터 인터페이스에 **구현 포함 가능** (`void Method() => Console.WriteLine(...)`) |



## 🔍 Java의 default 메서드
```java
interface Printer {
    default void print() {
        System.out.println("기본 출력");
    }
}
```

- Java에서는 default 키워드가 인터페이스 메서드 구현을 의미해요.
- 기존 구현체에 영향을 주지 않고 기능을 확장할 수 있게 해줍니다.

## 🔍 C#의 default(T) 표현식
```csharp
var s = default(MyStruct);
// 모든 필드가 초기값으로 설정됨
```

- C#에서는 default는 값 형식의 초기 상태를 생성하는 키워드예요.
- 예: int는 0, bool은 false, string은 null, 구조체는 모든 필드가 초기화됨

## 🔍 C# 8.0 이후 인터페이스 구현 포함 가능
```csharp
interface IPrinter
{
    void Print() => Console.WriteLine("기본 출력");
}
```

- Java처럼 C#도 이제 인터페이스에 구현을 포함할 수 있어요.
- 하지만 default 키워드는 사용하지 않고, 그냥 메서드 본문을 포함시킵니다.

## 🧩 정리
- Java의 default는 인터페이스 메서드 구현을 위한 키워드
- C#의 default는 값 초기화를 위한 표현식
- C#도 인터페이스에 구현을 넣을 수 있지만, default 키워드는 사용하지 않음


