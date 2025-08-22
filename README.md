# 📘 CSharp Tutorial (for C++ Developers)

## 🎯 프로젝트 소개

이 리포지토리는 **C++ 개발자가 C#을 학습할 때 꼭 알아야 할 개념**들을
정리한 튜토리얼입니다.\
특히 C++, C#, (필요 시 Java) 간의 **동일 개념 비교**와 **표 기반
정리**를 통해\
언어 차이를 한눈에 이해할 수 있도록 구성했습니다.

> ❗ ChatGPT와 같은 AI가 빠르게 답을 줄 수 있는 시대지만,\
> **정리된 학습 흐름과 검증된 예제 코드**는 여전히 필요합니다.\
> 이 문서는 "사전처럼 검색"하는 자료가 아니라, **교재처럼 체계적 학습
> 경로**를 제공합니다.

------------------------------------------------------------------------

## 📂 문서 구성

주요 주제는 다음과 같이 정리되어 있으며, 각 문서는 `C# vs C++` (필요시
Java) 비교 예제와 설명을 포함합니다.

### ✅ 언어 기초 비교

-   [Action / Func /
    Predicate](./002-03-Delegates_Action_Func_Predicate.md)
-   [Lambda & Delegate](./002-06-Lambda_Delegate_CSharp_vs_CPP.md)
-   [Closure](./002-07-Closure_CSharp_vs_CPP.md)
-   [Local Function vs Lambda](./002-08-LocalFunction_vs_Lambda.md)

### ✅ Null / 예외 처리

-   [Null 병합 연산자 (??)](./010-NullSafety_Operators.md)
-   [Null 조건부 연산자 (?. /
    ?\[\])](./011-NullConditional_Operators.md)
-   [Action & Exception Handling](./003-02-Action_Exception.md)

### ✅ 형변환 / 동등성

-   [Implicit & Explicit Cast](./005-Implicit_Explicit_CSharp_vs_CPP.md)
-   [Equals & GetHashCode](./006-Equals_GetHashCode_CSharp_vs_CPP.md)
-   [String Equality](./007-String_Equality_CSharp_Java_CPP.md)

### ✅ 컬렉션 / 순회

-   [foreach / ForEach 비교](./008-ForEach_CSharp_Java_CPP.md)

------------------------------------------------------------------------

## 🧾 특징

-   **비교 표 중심**: 각 개념은 `C# vs C++ (vs Java)` 표로 먼저 제시 →
    직관적 이해.
-   **실행 가능한 예제 코드**: 모든 문서는 컴파일 가능한 C#, C++ 예제
    포함.
-   **실무 적용성**: 단순 문법 소개가 아니라, CAD/로보틱스/대용량 데이터
    처리 등 공학적 응용을 염두에 둠.
-   **AI 보완**: AI가 생성하는 답변과 달리, **일관된 맥락과 학습 흐름**
    제공.

------------------------------------------------------------------------

## 🚀 실행 방법

### C

``` bash
# 새 프로젝트 생성
dotnet new console -o SampleApp
cd SampleApp

# 빌드 & 실행
dotnet build
dotnet run
```

### C++

``` bash
g++ -std=c++17 sample.cpp -o sample
./sample
```

------------------------------------------------------------------------

## 📅 로드맵

-   [x] Delegate / Lambda / Closure
-   [x] Null 병합 / 조건부 연산자
-   [x] Implicit / Explicit 변환
-   [x] Equals & GetHashCode
-   [ ] LINQ vs STL (진행 예정)
-   [ ] async/await vs C++20 coroutine
-   [ ] Generics vs Templates
-   [ ] GC vs RAII / IDisposable

------------------------------------------------------------------------

## 🤝 Contributing

-   오타 수정, 예제 코드 추가, 다른 언어(Java 등) 비교 제안 모두
    환영합니다.
-   PR을 통해 기여하거나, Issue로 아이디어를 남겨주세요.

------------------------------------------------------------------------

## 📜 License

MIT License
