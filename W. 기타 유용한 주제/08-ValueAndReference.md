# pass-by-value / pass-by-reference/ out parameter
C++, Java, Python에서의 값 전달 (pass-by-value), 참조 전달 (pass-by-reference), 그리고 출력 전달 (out parameter) 개념을 비교해서 정리해드릴게요.

## 🔍 C# 코드 설명 요약
```csharp
int a = 20;
Callee(a);         // 값 전달
CalleeRef(ref a);  // 참조 전달
CalleeOut(out a);  // 출력 전달
```

- Callee(int a) → 값 전달: 복사본을 넘김. 원본 a는 변경되지 않음.
- CalleeRef(ref int a) → 참조 전달: 원본 a를 직접 수정.
- CalleeOut(out int a) → 출력 전달: 반드시 값을 할당해야 하며, 원본 a가 변경됨.

## 소스
```csharp
namespace GrammarTest
{
    class Program
    {
        public static void Main(string[] args)
        {
            int a = 20;
            Callee(a);
            CalleeRef(ref a);
            CalleeOut(out a);
        }

        static void Callee(int a)
        {
            a = 5;
            Console.WriteLine("Inside Callee a : {0}", a);
        }

        static void CalleeRef(ref int a)
        {
            a = 6;
            Console.WriteLine("Inside CalleeRef a : {0}", a);
        }

        static void CalleeOut(out int a)
        {
            a = 7; //comment -> not compiled
            //반드시 a 값을 대체해야 한다.
            Console.WriteLine("Inside CalleeOut a : {0}", a);
        }
    }
}

```


## 🌐 언어별 지원 비교
| 전달 방식       | C#                                | C++                               | Java                              | Python                             |
|----------------|--------------------------------|--------------------------------|-----------------------------------|------------------------------------|
| 값 전달        | ✅ 기본값 전달 (`int a`)            | ✅ 기본값 전달 (`int a`)           | ✅ 기본값 전달 (`int a`)           | ✅ 불변 객체는 값처럼 동작 (`int`)   |
| 참조 전달      | ✅ `ref` 키워드 사용 (`ref int a`)  | ✅ 참조 전달 (`int& a`)            | ❌ 직접적인 참조 전달 불가         | ✅ 가변 객체는 참조로 전달 (`list`) |
| 출력 전달 (out)| ✅ `out` 키워드 사용 (`out int a`)  | ✅ 참조로 처리 (`int& a`)          | ❌ `out` 키워드 없음               | ✅ 다중 리턴으로 처리 (`return a`)  |



## 📌 언어별 특징 요약
### ✅ C++
- int a = 10;
- callee(a); → 값 전달
- calleeRef(int& a); → 참조 전달
- calleeOut(int& a); → 출력 전달도 참조로 처리

### ❌ Java
- 기본 타입은 모두 값 전달
- 객체는 참조지만, 참조 자체는 값으로 전달됨 → 원본 참조 변경 불가
- out 개념 없음 → 리턴값이나 래퍼 객체로 해결

### ✅ Python
- 모든 변수는 객체 참조
- 불변 객체 (int, str)는 값처럼 동작
- 가변 객체 (list, dict)는 참조처럼 동작
- out은 여러 값 리턴으로 자연스럽게 처리

## 🎯 결론
- C++은 값/참조/출력 모두 명시적으로 지원
- Java는 값 전달만 명확하며 참조 전달은 불가능
- Python은 참조 기반이지만 불변 객체는 값처럼, 출력은 다중 리턴으로 처리

---

# 📌 개념: "값을 무시하는 변수" 또는 "받지 않는 리턴값"
## ✅ C#
```csharp
int a;
CalleeOut(out a);        // 값을 받음
CalleeOut(out _);        // 값을 무시함
var (_, b) = GetTuple(); // 튜플 일부 무시
```

- _는 discard로 불리며, 값을 받되 사용하지 않겠다는 의도를 명확히 표현
- out, ref, 튜플 분해 등에서 사용 가능

## 🌐 언어별 비교 (Markdown 표)
| 기능/언어        | C#                                | C++                               | Java                              | Python                             |
|------------------|------------------------------------|------------------------------|-----------------------------------|------------------------------------|
| 값 무시 (`_`)  | ✅ `out _`, `(_, x)` 등 지원    | ❌ `_` 사용 불가, 변수 선언 필요   | ❌ `_` 사용 불가, 변수 선언 필요     | ✅ `_ = func()` 또는 `_, x = ...` 가능 |
| 튜플 분해        | ✅ `(_, x)`                  | ❌ 구조적 분해 미지원 (C++17부터 일부 지원) | ❌ 구조적 분해 미지원           | ✅ `_, x = (1, 2)` 가능              |
| 의미적 discard   | ✅ 명시적 discard (`_`)       | ❌ discard 개념 없음            | ❌ discard 개념 없음              | ✅ 관용적으로 `_` 사용               |



## 🧠 언어별 특징 요약
### ✅ C#
- _는 discard 변수로 공식 지원
- out, ref, 튜플 분해 등에서 사용 가능
- 컴파일러가 _를 실제 변수로 취급하지 않음

### ❌ C++
- _는 일반 변수 이름으로 취급됨
- int _; 가능하지만 discard 의미 없음
- 구조적 분해는 C++17 이후 auto [a, b] = pair; 형태로 일부 지원되지만 _는 discard로 사용 불가

### ❌ Java
- _는 Java 9 이후 예약어로 사용 불가
- 튜플이나 구조적 분해 자체가 없음
- discard 개념 없음

### ✅ Python
- _는 관용적으로 discard로 사용됨
- _, x = func() 또는 _ = some_unused_value
- 실제로는 변수에 값이 들어가지만, 사용하지 않겠다는 의도 표현

## 🎯 결론
- C#과 Python은 _를 통해 값을 무시하는 의도를 명확히 표현할 수 있음
- C++과 Java는 _를 discard로 사용할 수 없으며, 명시적 변수 선언이 필요함


