# 📘 C# 멀티캐스트 델리게이트 (Multicast Delegate)

## 1. 개념

- **정의**: 하나의 델리게이트 객체에 **여러 메서드를 등록**하여, 호출 시 **등록된 모든 메서드를 순차적으로 실행**할 수 있는 기능
- **핵심 특징**
  - `+=` : 메서드 추가 (구독)
  - `-=` : 메서드 제거 (구독 해제)
  - 등록된 순서대로 실행
  - 반환값은 마지막 메서드의 반환값만 유지됨  
    (이 때문에 **주로 `void` 반환형**에서 많이 사용됨)

---

## 2. 사용 예제

```csharp
using System;

delegate void DelegateType();

class A
{
    public void PrintA()
    {
        Console.WriteLine("Print A");
    }
        
    public void PrintB()
    {
        Console.WriteLine("Print B");
    }
}
 
class Program
{
    static void Main(string[] args)
    {
        A Test = new A();

        // 메서드 구독 추가
        DelegateType DelFunc = Test.PrintA;
        DelFunc += Test.PrintB;

        Console.WriteLine("=== First Call ===");
        DelFunc();
        // Print A
        // Print B

        // 메서드 구독 해제
        DelFunc -= Test.PrintB;

        Console.WriteLine("=== Second Call ===");
        DelFunc();
        // Print A
    }
}
```

---

## 3. 실행 결과

```
=== First Call ===
Print A
Print B
=== Second Call ===
Print A
```

---

## 4. 활용 사례

- **이벤트 처리**  
  여러 개의 이벤트 핸들러를 하나의 이벤트에 연결할 때 사용
- **알림 시스템**  
  데이터 변경 시 여러 구독자에게 동시에 알림 전송
- **UI 이벤트 바인딩**  
  버튼 클릭 → 여러 동작이 동시에 실행되도록 구성

---

## 5. 주의사항

- 반환값이 있는 델리게이트에서 Multicast를 사용하면, **마지막 호출의 반환값만 보존**됩니다.  
  → 결과를 모두 수집하려면 `GetInvocationList()` 활용 필요

```csharp
var delegates = DelFunc.GetInvocationList();
foreach (var d in delegates)
{
    d.DynamicInvoke(); // 각각 개별 호출
}
```

---

👉 멀티캐스트 델리게이트는 **이벤트(event)**의 기반이 되는 핵심 메커니즘입니다.  
