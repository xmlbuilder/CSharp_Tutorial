## 📘 C# `unsafe` 포인터 사용 정리

- C#의 `unsafe`에서는 C/C++과 유사하게 포인터를 사용할 수 있다.
- 단, **GC가 관리하는 메모리와 직접 할당한 네이티브 메모리의 수명 및 소유권을 구분** 해야 한다.

### 📌 1. 기본 포인터

```csharp
unsafe
{
    int value = 100;
    int* ptr = &value;
    Console.WriteLine(*ptr); // 100
    *ptr = 200;
    Console.WriteLine(value); // 200
}
```

| 연산 | 의미 |
|---|---|
| `&value` | 주소 얻기 |
| `*ptr` | 역참조 |
| `ptr++` | 포인터 이동 |
| `ptr[i]` | 인덱스 접근 |
| `ptr->field` | 구조체 멤버 접근 |
| `(byte*)ptr` | 포인터 형변환 |

- 포인터가 가리키는 타입은 일반적으로 `unmanaged` 타입이어야 한다.

### 📌 2. 관리 배열 고정: `fixed`

- GC는 관리 객체를 이동시킬 수 있으므로 배열 주소를 사용할 때는 `fixed`로 고정한다.

```csharp
unsafe
{
    int[] values = { 10, 20, 30, 40 };
    fixed (int* ptr = values)
    {
        for (int i = 0; i < values.Length; ++i)
            Console.WriteLine(ptr[i]);
        ptr[0] = 100;
    }
}
```

- 고정은 `fixed` 블록 동안만 유지된다.
- 블록 종료 후 저장해 둔 포인터를 계속 사용해서는 안 된다.
- 네이티브 함수가 포인터를 나중에 사용하도록 보관하는 경우 별도 수명 관리가 필요하다.

### 📌 3. 네이티브 힙 할당

#### 🔹 `Marshal.AllocHGlobal` / `Marshal.FreeHGlobal`

```csharp
using System;
using System.Runtime.InteropServices;

unsafe
{
    int count = 100;
    int* ptr = (int*)Marshal.AllocHGlobal(count * sizeof(int));
    try
    {
        for (int i = 0; i < count; ++i)
            ptr[i] = i * 10;
    }
    finally
    {
        Marshal.FreeHGlobal((IntPtr)ptr);
    }
}
```

#### 🔹 .NET 6 이상: `NativeMemory`

```csharp
using System.Runtime.InteropServices;

unsafe
{
    int* ptr = (int*)NativeMemory.Alloc(100, (nuint)sizeof(int));
    try
    {
        ptr[0] = 123;
    }
    finally
    {
        NativeMemory.Free(ptr);
    }
}
```

- 할당 함수와 해제 함수를 반드시 짝지어 사용한다.
- C++의 `new[]`로 만든 메모리를 C#의 `FreeHGlobal`로 해제하는 식으로 혼용해서는 안 된다.

### 📌 4. 구조체 포인터

```csharp
unsafe struct Point3D
{
    public double X, Y, Z;
}

unsafe
{
    Point3D point = new Point3D { X = 1, Y = 2, Z = 3 };
    Point3D* ptr = &point;
    ptr->X = 10;
    Console.WriteLine(point.X); // 10
}
```

### 📌 5. 스택 메모리: `stackalloc`

```csharp
unsafe
{
    double* values = stackalloc double[100];
    for (int i = 0; i < 100; ++i)
        values[i] = i * 0.5;
}
```

- 별도 해제가 필요 없다.
- 현재 메서드 실행 범위에서만 유효하다.
- 큰 배열은 스택 오버플로 위험이 있으므로 피한다.
- `stackalloc` 메모리는 기본적으로 초기화되지 않을 수 있으므로 사용 전 값을 설정한다.

### 📌 6. C++ 라이브러리와 배열 공유 (P/Invoke)

#### 🔹 C++ (`Geometry.dll`):

```cpp
extern "C" __declspec(dllexport)
void __cdecl TransformPoints(double* points, int count)
{
    for (int i = 0; i < count; ++i)
        points[i * 3] += 10.0;
}
```

#### 🔹 C#:

```csharp
using System;
using System.Runtime.InteropServices;

static class NativeMethods
{
    [DllImport("Geometry.dll", CallingConvention = CallingConvention.Cdecl)]
    public static extern unsafe void TransformPoints(double* points, int count);
}

unsafe
{
    double[] points = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
    fixed (double* ptr = points)
    {
        NativeMethods.TransformPoints(ptr, 3);
    }
    Console.WriteLine(points[0]); // 11
}
```

- 이 예제는 동기 호출 동안 관리 배열을 고정하여 **추가 배열 복사 없이** 네이티브 함수에 전달한다.
- 네이티브 함수는 전달된 버퍼 길이(`3 * count`)를 초과해서 접근하면 안 된다.

### 📌 7. `unsafe` 포인터와 `Span<T>`

```csharp
Span<double> values = stackalloc double[100];
for (int i = 0; i < values.Length; ++i)
    values[i] = i * 0.5;
```

| 구분 | `T*` | `Span<T>` |
|---|---|---|
| 포인터 산술 | 가능 | 불가능 |
| 범위 검사 | 없음 | 있음 |
| 관리 배열 접근 | 보통 `fixed` 필요 | 가능 |
| 네이티브 메모리 접근 | 가능 | 가능(적절한 생성/수명 관리 필요) |
| C++ 함수에 직접 전달 | 가능 | 고정 또는 포인터 추출 필요 |

- 일반적인 배열 처리는 `Span<T>`를 우선 검토하고, 네이티브 API와의 직접 포인터 교환에는 `unsafe`를 사용한다.

### 📌 8. 프로젝트 설정

- Visual Studio의 **프로젝트 속성 → Build → Allow unsafe code** 또는 `.csproj`:

```xml
<PropertyGroup>
  <AllowUnsafeBlocks>true</AllowUnsafeBlocks>
</PropertyGroup>
```

### 📌 9. 메모리 방식 비교

| 방식 | 메모리 소유/관리 | 주요 용도 |
|---|---|---|
| `T*` | 포인터 자체는 소유권을 나타내지 않음 | 직접 주소 접근 |
| `fixed` | GC 관리 객체를 일시 고정 | 관리 배열을 C++에 전달 |
| `stackalloc` | 스택 프레임 | 소규모 임시 버퍼 |
| `Marshal.AllocHGlobal` | 직접 할당/해제 | 비관리 메모리 |
| `NativeMemory.Alloc` | 직접 할당/해제 | .NET 6+ 네이티브 버퍼 |

### 📌 CAD/FEM 데이터 공유 시 핵심

1. **C# 소유 배열 → C++ 호출:** `fixed`로 호출 중 고정하고, C++이 포인터를 보관하지 않도록 한다.
2. **C++ 소유 버퍼 → C# 접근:** C++에서 할당/해제 API를 제공하고, C#에서는 유효한 기간에만 포인터로 접근한다.
3. 수백만 노드/요소 데이터는 복사 비용보다도 **소유권, 해제 시점, 버퍼 크기, 구조체 레이아웃** 을 먼저 검증한다.
4. `unsafe`는 범위 검사나 수명 검증을 자동 제공하지 않는다.

---

