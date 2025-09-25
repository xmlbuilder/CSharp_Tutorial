# CSharp Tutorial (for C++ Developers)

C++ 개발자가 C#을 학습할 때 자주 부딪히는 개념을 **C# ↔ C++(↔ Java)** 비교 중심으로 정리했습니다.
특히 C++, C#, (필요 시 Java) 간의 동일 개념 비교와 표 기반 정리를 통해
언어 차이를 한눈에 이해할 수 있도록 구성했습니다. 
이 문서는 "사전처럼 검색"하는 자료가 아니라, 교재처럼 체계적 학습 경로를 제공합니다.
 
아래 **📚 목차(바로가기)** 에서 원하는 주제를 곧장 찾아가세요.

---

## 📚 목차(바로가기)

- [A. 시작하기 & 언어 철학](#a-시작하기--언어-철학)
- [B. Delegate  Lambda  Closure  Local  Function](#b-delegate--lambda--closure--local-function)
- [C. 캐스팅 & 타입 검사](#c-캐스팅--타입-검사)
- [D. 동등성  비교](#d-동등성--비교)
- [E. Null & 예외 처리](#e-null--예외-처리)
- [F. 문자열 처리(인코딩포맷)](#f-문자열-처리인코딩포맷)
- [G. 컬렉션 & 배열 & 열거](#g-컬렉션--배열--열거)
- [H. 이벤트 & 데이터 바인딩](#h-이벤트--데이터-바인딩)
- [I. 전처리기 & 언어 기능 모음](#i-전처리기--언어-기능-모음)
- [J. 경로/환경 & 기타 팁](#j-경로환경--기타-팁)
- [K. OOP: 클래스/인터페이스/상속/구조체](#k-oop-클래스인터페이스상속구조체)
- [L. 튜플](#l-튜플)
- [M. 제네릭(설계 철학)](#m-제네릭설계-철학)
- [로드맵 · 기여 · 라이선스](#로드맵--기여--라이선스)

---

## A. 시작하기 & 언어 철학
- [언어 설계의 철학](./A.%20시작하기%20%26%20언어%20철학/01-언어설계의철학.md)
- [.NET CLI로 Hello World](./A.%20시작하기%20%26%20언어%20철학/02-DotNet_CLI_HelloWorld.md)
- [typeof vs decltype (C# ↔ C++)](./A.%20시작하기%20%26%20언어%20철학/03-typeof_vs_decltype.md)
- [기본 자료형 개요](./A.%20시작하기%20%26%20언어%20철학/04-기본자료형.md)

🔼 [맨 위로](#-목차바로가기)

## B. Delegate  Lambda  Closure  Local Function
- [Delegate vs Function](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/01-Delegate_vs_Function.md)
- [Delegates, Func, Action](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/02-Delegates_Func_Action.md)
- [Delegates vs Java Functional](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/03-Delegates_vs_Java_Functional.md)
- [Multicast Delegate](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/04-Multicast_Delegate.md)
- [Predicate vs Function](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/05-Predicate_vs_Function.md)
- [Action 예외 처리: C# vs C++](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/06-Action_Exception_When_vs_CPP.md)
- [Lambda & Delegate: C# vs C++](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/07-Lambda_Delegate_CSharp_vs_CPP.md)
- [Closure: C# vs C++](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/08-CSharp_vs_CPP_Closure.md)
- [Delegate 패턴 모음: C# vs C++](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/09-Delegate_Patterns_CSharp_vs_CPP.md)
- [Local Function vs C++](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/10-CSharp_LocalFunction_vs_CPP.md)
- [Safe Delegate Pattern](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/11-SafeDelegate_Pattern.md)
- [BulletProof Extension Method](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/12-BulletProof_ExtensionMethod_CSharp_vs_CPP.md)
- [Delegates를 인자로 전달](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/13-Passing_Delegates_as_Parameters_CSharp_vs_CPP.md)
- [Extension Method 개요](./B.%20Delegate%20%20Lambda%20%20Closure%20%20Local%20Function/14-extension.md)

🔼 [맨 위로](#-목차바로가기)

## C. 캐스팅 & 타입 검사
- [Implicit/Explicit 변환 (C# vs C++)](./005-Implicit_Explicit_CSharp_vs_CPP.md)
- [Explicit Conversion](./006-Explicit_Conversion.md)
- [`as` 연산자](./021-as.md)

🔼 [맨 위로](#-목차바로가기)

## D. 동등성  비교
- [Equals 기본](./D.%20동등성%20%20비교/01-Equals.md)
- [동등 연산자 오버로딩](./D.%20동등성%20%20비교/02-OverloadingEqualityOperators.md)
- [문자열 동등성](./D.%20동등성%20%20비교/03-StringEquality.md)
- [string equal 정리](./D.%20동등성%20%20비교/04-string_equal.md)

🔼 [맨 위로](#-목차바로가기)

## E. Null & 예외 처리
- [Null 안전 연산자(?? 등)](./010-NullSafety_Operators.md)
- [Null 조건부 연산자(?. / ?[])](./011-NullConditional_Operators.md)
- [try/catch 기본](./055-try_catch.md)
- [checked / unchecked](./043-check_and_unchecked.md)

🔼 [맨 위로](#-목차바로가기)

## F. 문자열 처리(인코딩포맷)
- [UTF-8 정리](./F.%20문자열%20처리(인코딩포맷)/01-utf-8.md)
- [문자 처리 기초](./F.%20문자열%20처리(인코딩포맷)/02.character_처리.md)
- [문자열 치트시트](./F.%20문자열%20처리(인코딩포맷)/03-string-cheatsheet.md)
- [any/all 스타일 검사](./F.%20문자열%20처리(인코딩포맷)/04-string_any_all.md)
- [부분 문자열](./F.%20문자열%20처리(인코딩포맷)/05-string_substring.md)
- [split / join 치트시트](./F.%20문자열%20처리(인코딩포맷)/06-string_split_join_cheatsheet.md)
- [trim / replace / 대문자 치트시트](./F.%20문자열%20처리(인코딩포맷)/07.trim-replace-uppercase-cheatsheet.md)
- [문자열 → 숫자 변환](./F.%20문자열%20처리(인코딩포맷)/08.string_to_number.md)
- 고정폭 포맷 시리즈
  - [Fixed column 문자열 ①](./F.%20문자열%20처리(인코딩포맷)/09-Fixed_column_string.md)
  - [패딩 고정폭 포맷](./F.%20문자열%20처리(인코딩포맷)/10-PaddingFixedFormat.md)
  - [통화 포맷팅](./F.%20문자열%20처리(인코딩포맷)/11-CurrencyFormatting.md)
  - [Fixed column 심화 ①](./F.%20문자열%20처리(인코딩포맷)/12.Fixed_column_string_심화.md)
  - [Fixed column 심화 ②](./F.%20문자열%20처리(인코딩포맷)/13.Fixed_column_string_심화2.md)

🔼 [맨 위로](#-목차바로가기)

## G. 컬렉션 & 배열 & 열거
- 배열/복사
  - [Array copy](./G.%20컬렉션%20%26%20배열%20%26%20열거/01-Array_copy.md)
  - [Array.Clear](./G.%20컬렉션%20%26%20배열%20%26%20열거/02-ArrayClear.md)
  - [Shallow/Deep Copy](./G.%20컬렉션%20%26%20배열%20%26%20열거/03-ShallowDeepCopy.md)
  - [Jagged Array](./G.%20컬렉션%20%26%20배열%20%26%20열거/04-Jagged_Array.md)
  - [다차원 배열](./G.%20컬렉션%20%26%20배열%20%26%20열거/05-MultiDimensional_Arrays.md)
- 열거/순회
  - [foreach / ForEach](./G.%20컬렉션%20%26%20배열%20%26%20열거/06-ForEach.md)
  - [IEnumerable 개요](./G.%20컬렉션%20%26%20배열%20%26%20열거/07-Eumerable.md)
  - [가변 인자(params)](./G.%20컬렉션%20%26%20배열%20%26%20열거/08-VariableArgument.md)
  - [Enum](./G.%20컬렉션%20%26%20배열%20%26%20열거/09-Enum.md)
- 사전(Dictionary)
  - [개념](./G.%20컬렉션%20%26%20배열%20%26%20열거/10-Dictionary개념.md)
  - [요소 추가](./G.%20컬렉션%20%26%20배열%20%26%20열거/11-Dictionary요소추가.md)
  - [Key 다루기](./G.%20컬렉션%20%26%20배열%20%26%20열거/12-DictionaryKey.md)
  - [사용자 정의 Key](./G.%20컬렉션%20%26%20배열%20%26%20열거/13-Dictionary_사용자key.md)
  - [실전 예제](./G.%20컬렉션%20%26%20배열%20%26%20열거/14-Dictionary실전예.md)

🔼 [맨 위로](#-목차바로가기)

## H. 이벤트 & 데이터 바인딩
- [Event 개념](./H.%20이벤트%20%26%20데이터%20바인딩/01-Event개념.md)
- [EventHandler](./H.%20이벤트%20%26%20데이터%20바인딩/02-EventHandler.md)
- [INotifyPropertyChanged](./H.%20이벤트%20%26%20데이터%20바인딩/03-INotifyPropertyChanged.md)
- [BindingList](./H.%20이벤트%20%26%20데이터%20바인딩/04-BindingList.md)
- [Mouse 이벤트 핸들링](./H.%20이벤트%20%26%20데이터%20바인딩/05-MouseEventHandler.md)

🔼 [맨 위로](#-목차바로가기)

## I. 전처리기 & 언어 기능 모음
- [01-CSharp_전처리기.md](./I.%20전처리기%20%26%20언어%20기능%20모음/01-CSharp_전처리기.md)
- [warning disable](./I.%20전처리기%20%26%20언어%20기능%20모음/02-warning_disable.md)
- [define & 조건부 컴파일](./I.%20전처리기%20%26%20언어%20기능%20모음/03-define_and_condition.md)
- [Named Argument](./I.%20전처리기%20%26%20언어%20기능%20모음/04-named_argument.md)
- [using](./I.%20전처리기%20%26%20언어%20기능%20모음/05-using.md)
- [IDisposable/Dispose](./I.%20전처리기%20%26%20언어%20기능%20모음/06-Dispose.md)
- [goto](./07-goto.md)
- [default](./I.%20전처리기%20%26%20언어%20기능%20모음/08-default.md)

🔼 [맨 위로](#-목차바로가기)

## J. 경로/환경 & 기타 팁
- [프로그램 경로 다루기](./027-programpath.md)
- [static 변수](./025-static_variable.md)

🔼 [맨 위로](#-목차바로가기)

## K. OOP: 클래스/인터페이스/상속/구조체
- 클래스 기초/키워드
  - [this](./053-this.md) · [static class](./054-static_class.md)
  - [읽기 전용 속성](./052-읽기전용속성.md)
- 생성자/수명주기
  - [Static Constructor](./065-Static_Constructor.md)
  - [Constructor Overloading](./066-Constructor_Overloading.md)
  - [Optional Constructor](./067-Optional_Constructor.md)
- 상속/오버라이드
  - [base / 상속](./064-base_상속.md)
  - [virtual/override](./063-virtual_override.md)
  - [abstract/override](./062-abstract_override.md)
  - [abstract class](./061-abstract_class.md)
  - [abstract function](./061-abstract_class_abstract_function.md)
  - [sealed](./068-Sealed.md)
  - [익명 클래스](./069-익명클래스.md)
  - [구조체](./070-구조체.md)
- 인터페이스
  - [interface 기초](./058-interface.md)
  - [함수 중복 문제](./059-interface_함수_중복문제.md)
  - [default interface method](./060-interface_default.md)

🔼 [맨 위로](#-목차바로가기)

## L. 튜플
- [Tuple](./050-Tuple.md)
- [Tuple 정렬](./051-TupleSort.md)

🔼 [맨 위로](#-목차바로가기)

## M. 제네릭(설계 철학)
- [generic 설계 철학](./012-generic_설계철학.md)

🔼 [맨 위로](#-목차바로가기)

---

## 로드맵 · 기여 · 라이선스

- **로드맵(예정)**: LINQ vs STL, async/await vs C++20 coroutine, Generics vs Templates, GC vs RAII/IDisposable  
- **기여**: 오타/예제/비교 추가 PR 환영  
- **License**: MIT

