## 📘 Unsafe.Add 개요

| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 네임스페이스             | `System.Runtime.CompilerServices`                                    |
| 주요 기능                | ref 기반 포인터 연산처럼 메모리 참조 위치를 이동                     |
| 사용 목적                | 고성능, 저수준 메모리 접근, Span 처리, Native interop 등             |
| 안전 여부                | ❌ 타입 안전 보장 안 됨. 런타임 오류 가능성 있음                      |
| C++ 유사성               | `*(ptr + offset)` 또는 `&arr[i]`와 유사한 동작                       |
| 대표 메서드              | `Unsafe.Add`, `Unsafe.As`, `Unsafe.Read`, `Unsafe.Write` 등           |
| 사용 조건                | `unsafe` 키워드 없이도 사용 가능 (단, 조심해서 써야 함)              |
| JIT 최적화 영향          | JIT 컴파일러가 최적화에 활용 가능 (성능 향상 목적)                   |



### 📌 코드 흐름 해설
```csharp
int[] a = new[] {123, 234, 345, 456};
```

- 배열 선언. 메모리상 연속된 int 값들이 저장됨.

### 📌 코드 흐름 해설
```csharp
int[] a = new[] {123, 234, 345, 456};
ref int r1 = ref Unsafe.Add(ref a[0], 1); // a[1] 참조
```
- a[0] 기준으로 +1 위치 → a[1]인 234를 참조

```csharp
ref int r2 = ref Unsafe.Add(ref r1, 2); // a[3] 참조
```

- r1 기준으로 +2 위치 → a[3]인 456을 참조
```csharp
ref int r3 = ref Unsafe.Add(ref r2, -3); // a[0] 참조
```
- r2 기준으로 -3 위치 → 다시 a[0]인 123 참조

### 📌 주의할 점
- Unsafe.Add는 배열 경계를 검사하지 않기 때문에, 잘못된 offset을 주면 메모리 오류나 예측 불가능한 동작이 발생할 수 있음.
- C++처럼 포인터 연산을 흉내내지만, C#의 타입 안전성과 런타임 보호를 우회하는 방식이기 때문에 정말 필요한 경우에만 사용해야 합니다.


### 📌 네이티브에서 넘어온 int[4] 배열을 Unsafe로 분해
```csharp
using System;
using System.Runtime.InteropServices;
using System.Runtime.CompilerServices;

class Program
{
    static void Main()
    {
        // 네이티브에서 넘어온 배열을 시뮬레이션
        int[] nativeArray = new int[] { 100, 200, 300, 400 };

        // 배열을 unmanaged 메모리로 복사
        IntPtr unmanagedPtr = Marshal.AllocHGlobal(sizeof(int) * nativeArray.Length);
        Marshal.Copy(nativeArray, 0, unmanagedPtr, nativeArray.Length);

        try
        {
            // Unsafe를 사용하여 포인터처럼 접근
            unsafe
            {
                int* basePtr = (int*)unmanagedPtr;

                // ref로 접근
                ref int r0 = ref Unsafe.AsRef<int>(basePtr);
                ref int r1 = ref Unsafe.Add(ref r0, 1);
                ref int r2 = ref Unsafe.Add(ref r0, 2);
                ref int r3 = ref Unsafe.Add(ref r0, 3);

                Console.WriteLine($"r0: {r0}"); // 100
                Console.WriteLine($"r1: {r1}"); // 200
                Console.WriteLine($"r2: {r2}"); // 300
                Console.WriteLine($"r3: {r3}"); // 400
            }
        }
        finally
        {
            Marshal.FreeHGlobal(unmanagedPtr); // 메모리 해제
        }
    }
}
```

### 📌 핵심 포인트

| 항목                     | 설명                                                                 |
|--------------------------|----------------------------------------------------------------------|
| 메모리 복사              | `Marshal.Copy`로 managed → unmanaged 메모리로 복사                   |
| 포인터 접근              | `IntPtr`를 `int*`로 캐스팅하여 포인터처럼 접근                       |
| ref 변환                 | `Unsafe.AsRef<T>()`로 포인터를 `ref T`로 변환                         |
| 포인터 연산              | `Unsafe.Add(ref, offset)`로 C++처럼 메모리 위치 이동                  |
| 메모리 해제              | `Marshal.FreeHGlobal`로 unmanaged 메모리 누수 방지                    |
| unsafe 블록              | 포인터 사용을 위해 `unsafe` 키워드 필요                              |
| 안정성 경고              | 경계 검사 없음 → 잘못된 offset은 예외 없이 잘못된 메모리 접근 유발    |
| 성능 이점                | 경계 검사 제거 + JIT 최적화로 고성능 처리 가능                        |
| 사용 시기                | Native interop, 구조체 분해, 고성능 네트워크 처리 등에서 유용         |




### 📌 C#이 C++처럼 쓸 수 있는 이유
| 기능 영역               | C#에서의 구현 방식                                      | C++과의 유사성                           |
|------------------------|--------------------------------------------------------|------------------------------------------|
| 포인터 연산             | `unsafe`, `fixed`, `Unsafe.Add`, `IntPtr`             | C++의 `int*`, `ptr + offset`             |
| 스택 메모리 할당        | `stackalloc`, `Span<T>`, `ref struct`                 | C++의 스택 기반 배열 (`int arr[10]`)     |
| 구조체 메모리 제어      | `[StructLayout]`, `FieldOffset`, `Marshal`            | C++의 `struct` 메모리 배치 제어          |
| 메모리 캐스팅           | `Unsafe.As<TFrom, TTo>()`, `MemoryMarshal.Cast`       | C++의 `reinterpret_cast`                 |
| Native 코드 연동        | `DllImport`, `Marshal`, `GCHandle`, `IntPtr`          | C++의 extern linkage, 직접 호출          |
| 경계 없는 접근          | `Unsafe.Read`, `Unsafe.Write`, `Add`, `SkipInit` 등   | C++의 unchecked 배열 접근                |
| 성능 최적화             | `AggressiveInlining`, `Span<T>`, `SIMD`, `JIT` 힌트    | C++의 컴파일러 인라인 및 최적화          |
| 메모리 직접 제어        | `Marshal.AllocHGlobal`, `FreeHGlobal`, `GCHandle`     | C++의 `malloc


### 📌 GC가 만드는 차이점
- C#은 객체 생명주기를 자동으로 관리하지만, 그만큼 예측 불가능한 시점에 GC가 작동.
- C++은 개발자가 직접 new/delete 또는 RAII로 메모리를 관리하므로 제어권이 완전히 개발자에게 있음.
- C#에서 GCHandle, fixed, pinned object 등을 써서 GC의 영향을 줄일 수는 있지만, 완전히 없앨 수는 없음.

---

