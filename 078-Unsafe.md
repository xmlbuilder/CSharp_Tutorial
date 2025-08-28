# 🔐 C#의 unsafe 키워드 개요
C#은 기본적으로 메모리 안전성과 타입 안정성을 보장하는 관리형 언어입니다.
하지만 unsafe 키워드를 사용하면 비관리형 코드처럼 포인터를 직접 다룰 수 있는 컨텍스트를 열 수 있습니다.
```csharp
unsafe static void FastCopy(byte* ps, byte* pd, int count) { ... }
```

- unsafe는 포인터를 사용하는 모든 메서드, 블록, 타입 선언에 필요합니다.
- 컴파일 시 /unsafe 옵션을 명시해야 합니다.

## 🧪 포인터 연산 예제
```csharp
unsafe static void SquarePtrProgram(int* p)
{
    *p *= *p;
}

public unsafe static void Main(string[] args)
{
    int i = 5;
    SquarePtrProgram(&i);
    Console.WriteLine(i); // 출력: 25
}
```

- &i는 변수 i의 주소를 가져옵니다.
- *p는 해당 주소의 값을 직접 수정합니다.

## 📌 fixed 문을 통한 배열 포인터
```csharp
unsafe static void UnsafeSquareArray(int[] pArr)
{
    int len = pArr.Length;
    fixed (int* fixedPointer = &(pArr[0]))
    {
        int* p = fixedPointer;
        for (int i = 0; i < len; i++)
        {
            *p *= *p;
            p++;
        }
    }
}
```

- fixed는 가비지 컬렉터가 배열을 이동시키지 못하도록 메모리를 고정합니다.
- 포인터는 fixed 블록 내에서만 유효하며 읽기 전용입니다.

## 🧬 구조체 내 고정 배열 사용
```csharp
unsafe struct Example
{
    public fixed byte someField[8];
    public fixed char AnotherField[64];
}
```

- fixed 배열은 구조체 내부에서 고정 크기의 버퍼를 선언할 때 사용됩니다.
- 주로 Interop이나 성능 최적화에 활용됩니다.

## 🔤 문자열 포인터 수정 예제
```csharp
var myStr = "Hello World!";
unsafe
{
    fixed (char* ptr = myStr)
    {
        ptr[0] = 'M';
    }
    Console.WriteLine(myStr); // 출력: Mello World!
}
```

- 문자열도 fixed를 통해 포인터로 접근 가능
- 단, 문자열은 불변이므로 이 방식은 특별한 상황에서만 사용해야 합니다

## ⚠️ unsafe 사용 시 주의사항
| 항목                 | 설명                                                                 |
|----------------------|----------------------------------------------------------------------|
| 메모리 안정성 저하    | 포인터를 잘못 사용하면 메모리 손상, 버퍼 오버런 등의 위험 발생         |
| 타입 안전성 약화      | 컴파일러가 타입 체크를 생략하므로 런타임 오류 가능성 증가               |
| 가비지 컬렉션 우회    | 객체가 GC에 의해 이동되면 포인터가 무효화될 수 있음                     |
| 플랫폼 제한           | 일부 환경에서는 `unsafe` 코드 실행이 제한될 수 있음                     |
| 디버깅 난이도 상승    | 포인터 연산은 디버깅과 유지 보수에 어려움을 줄 수 있음                  |



## 🧬 C# `unsafe` vs 다른 언어 비교

| 항목               | C# (`unsafe`)                            | C++                                        | Java                                       |
|--------------------|-------------------------------------------|---------------------------------------------|---------------------------------------------|
| 포인터 지원        | ✅ `unsafe` 블록 내에서만 사용 가능         | ✅ 자유롭게 사용 가능                         | ❌ 직접적인 포인터 사용 불가                  |
| 메모리 직접 접근   | ✅ 가능 (`*`, `&`, `fixed`, `stackalloc`) | ✅ 가능 (`*`, `&`, `new`, `delete`)           | ❌ 불가능 (JVM이 메모리 관리)                 |
| 타입 안전성        | ⚠️ 약화됨 (`unsafe` 사용 시 타입 체크 생략) | ❌ 낮음 (컴파일러가 타입 체크 제한적)         | ✅ 높음 (모든 객체는 타입 안전하게 관리됨)    |
| 가비지 컬렉션      | ✅ 있음 (단, `fixed`로 메모리 고정 필요)    | ❌ 없음 (수동 메모리 관리 필요)               | ✅ 있음 (JVM이 자동 관리)                     |
| 안전성 제어        | ✅ 기본은 안전, `unsafe`로 선택적 비안전 허용 | ❌ 없음 (전체가 비안전 환경)                  | ✅ 완전한 안전 환경                           |
| 사용 목적          | 고성능 연산, Interop, 시스템 프로그래밍     | 시스템 프로그래밍, 성능 최적화, 저수준 제어   | 애플리케이션 개발, 안정성 중심               |
| 실행 환경 제한     | ⚠️ 일부 플랫폼에서 `unsafe` 제한 가능        | ❌ 없음                                       | ✅ JVM 기반 환경에서만 실행 가능              |



## ✅ 언제 사용하면 좋을까?
- 성능 최적화가 필요한 경우 (예: 이미지 처리, 버퍼 연산)
- Interop: C/C++ 라이브러리와 연동 시 포인터 기반 API 필요
- 시스템 프로그래밍: 메모리 제어가 필요한 저수준 작업
---
