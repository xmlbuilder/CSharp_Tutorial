# C# `Action`, `Func`, `Predicate` vs Java 함수형 인터페이스 비교

이 문서는 C#의 **표준 델리게이트**(`Action`, `Func<T>`,
`Predicate<T>`)를 Java의 **함수형 인터페이스**(`Consumer`, `Function`,
`Predicate` 등)와 비교합니다.\
각각의 사용 예제와 특징을 코드와 함께 정리했습니다.

------------------------------------------------------------------------

## 1) 매핑 표

  --------------------------------------------------------------------------------
  C# (System)       의미              Java                    비고
                                      (java.util.function)    
  ----------------- ----------------- ----------------------- --------------------
  `Action`          파라미터 없음,    `Runnable`              `run()` 메서드 실행
                    반환 없음                                 

  `Action<T>`       `T` 소비, 반환    `Consumer<T>`           체이닝: C#은 `+=`,
                    없음                                      Java는 `andThen`

  `Action<T1,T2>`   두 개 인자 소비   `BiConsumer<T1,T2>`     동일

  `Func<TResult>`   반환만 있음       `Supplier<TResult>`     공급자 패턴

  `Func<T,R>`       `T` → `R`         `Function<T,R>`         합성 지원

  `Func<T1,T2,R>`   `(T1,T2)` → `R`   `BiFunction<T1,T2,R>`   동일

  `Predicate<T>`    `T` → `bool`      `Predicate<T>`          합성 API는 Java가 더
                                                              풍부

  (없음)            `(T1,T2)` →       `BiPredicate<T1,T2>`    C#에서는
                    `bool`                                    `Func<T1,T2,bool>`
                                                              사용
  --------------------------------------------------------------------------------

------------------------------------------------------------------------

## 2) C# 코드 예제

``` csharp

public static void Print(string msg) => Console.WriteLine(msg);
public static int FuncPrint(string msg, string sub) {
    Console.WriteLine(nameof(FuncPrint));
    return 1;
}
public static bool PredictPrint(string msg) {
    Console.WriteLine(msg);
    return true;
}

Action<string> _action1 = Print;
Func<string, string, int> func1 = FuncPrint;
Predicate<string> predicate = PredictPrint;

_action1.Invoke("sample");                   // "sample"
func1.Invoke("message", "message2");         // "FuncPrint"
predicate.Invoke("Sample");                  // "Sample"

// 무명 메서드
Action<string, string> _action2 = delegate(string s, string s1) {
    Console.WriteLine($"{s}, {s1}");
};
_action2("Sample1", "Sample2");              // "Sample1, Sample2"

// 람다식
Action<int, int> _action3 = (x, y) => Console.WriteLine($"{x}, {y}");
_action3(10, 23);                            // "10, 23"


```

------------------------------------------------------------------------

## 3) Java 대응 코드

``` java
import java.util.function.*;

public class Program {
    public static void main(String[] args) {
        // 메서드 참조 (C#의 메서드 그룹)
        Consumer<String> action1 = Program::print;
        BiFunction<String, String, Integer> func1 = Program::funcPrint;
        Predicate<String> predicate = Program::predictPrint;

        action1.accept("sample");                 // "sample"
        func1.apply("message", "message2");       // "FuncPrint"
        predicate.test("Sample");                 // "Sample"

        // 익명 클래스 (C#의 delegate 유사)
        BiConsumer<String, String> action2 = new BiConsumer<>() {
            @Override public void accept(String s, String s1) {
                System.out.println(s + ", " + s1);
            }
        };
        action2.accept("Sample1", "Sample2");

        // 람다식
        BiConsumer<Integer, Integer> action3 = (x, y) -> System.out.println(x + ", " + y);
        action3.accept(10, 23);                   // "10, 23"
    }

    public static void print(String msg) {
        System.out.println(msg);
    }
    public static int funcPrint(String msg, String sub) {
        System.out.println("FuncPrint");
        return 1;
    }
    public static boolean predictPrint(String msg) {
        System.out.println(msg);
        return true;
    }
}
```

------------------------------------------------------------------------

## 4) 합성/체이닝 비교

### Consumer / Action

-   **Java**

    ``` java
    Consumer<String> a = s -> System.out.println("A:" + s);
    Consumer<String> b = s -> System.out.println("B:" + s);
    a.andThen(b).accept("x"); // A:x, B:x
    ```

-   **C#**

    ``` csharp
    Action<string> a = s => Console.WriteLine("A:" + s);
    Action<string> b = s => Console.WriteLine("B:" + s);
    (a + b)("x"); // A:x, B:x
    ```

### Function / Func

-   **Java**

    ``` java
    Function<Integer,Integer> f = x -> x + 1;
    Function<Integer,Integer> g = x -> x * 2;
    Function<Integer,Integer> fg = f.andThen(g);   // g(f(x))
    ```

-   **C#**

    ``` csharp
    Func<int,int> f = x => x + 1;
    Func<int,int> g = x => x * 2;
    Func<int,int> fg = x => g(f(x));               // 직접 합성
    ```

### Predicate

-   **Java**

    ``` java
    Predicate<String> notEmpty = s -> s != null && !s.isEmpty();
    Predicate<String> hasA = s -> s.contains("a");
    Predicate<String> both = notEmpty.and(hasA);
    ```

-   **C#**

    ``` csharp
    Predicate<string> notEmpty = s => s != null && s.Length > 0;
    Predicate<string> hasA = s => s.Contains("a");
    Predicate<string> both = s => notEmpty(s) && hasA(s);
    ```

------------------------------------------------------------------------

## 5) 문법/시그니처 차이

-   **호출**
    -   C#: `Invoke()` 또는 `_action("msg")`
    -   Java: `accept`, `apply`, `test`, `run`
-   **멀티캐스트**
    -   C#: `+=`/`-=` 지원 (여러 메서드 호출)
    -   Java: `andThen`으로 연결
-   **반환형 위치**
    -   C#: `Func<T1,T2,...,TResult>` (마지막 인자가 반환형)
    -   Java: `Function<T,R>` (두 번째가 반환형)

------------------------------------------------------------------------

## 6) 요약

-   **C# `Action`, `Func`, `Predicate`** ↔ **Java `Consumer`,
    `Function`, `Predicate`**는 개념적으로 동일.
-   차이점:
    -   C#은 **멀티캐스트 델리게이트**가 장점.
    -   Java는 **합성 메서드**(`andThen`, `compose`, `negate` 등)가
        풍부.
-   변환 시 주의할 점:
    -   호출 메서드명 (`Invoke` vs `accept/apply/test`)
    -   반환형 위치 제네릭 순서
    -   이항 Predicate는 C#엔 없어 `Func<T1,T2,bool>` 사용해야 함.

---
