## 📘 ReadOnlySpan<T>

### 📌 1. 개요

- `ReadOnlySpan<T>`은 연속된 메모리 영역을 **복사하지 않고 읽기 전용으로 참조하는 경량 View 타입** 이다.
- C++의 `std::span<const T>`와 유사한 개념이다.

```csharp
int[] data = { 10, 20, 30, 40, 50 };

ReadOnlySpan<int> span = data.AsSpan(1, 3);

// 20, 30, 40
foreach (int value in span)
    Console.WriteLine(value);
```

- 새로운 배열을 생성하지 않고 원본 배열의 일부를 참조한다.

### 📌 2. 주요 특징

| 특징 | 설명 |
|---|---|
| Zero-copy | 데이터 복사 없이 기존 메모리 참조 |
| Read-only | Span을 통한 데이터 수정 불가 |
| 연속 메모리 | 배열, Native 메모리 등 참조 |
| Slice | 메모리 일부를 복사 없이 참조 |
| `ref struct` | 메모리 수명 관련 사용 제한 |
| GC 관리 | 메모리 소유권 및 수명 관리 기능 없음 |
| Native 연동 | C/C++ 포인터를 직접 참조 가능 |

- **주의:** Read-only는 원본 메모리가 변경되지 않는다는 뜻이 아니다. 다른 코드에서 원본 데이터를 수정할 수 있다.

### 📌 3. Span<T>과 비교

```csharp
int[] data = { 1, 2, 3 };

Span<int> writable = data.AsSpan();
writable[0] = 100;  // 가능

ReadOnlySpan<int> readonlySpan = data;
int value = readonlySpan[0];

// readonlySpan[0] = 200; // 컴파일 오류
```

- `Span<T>`: 읽기 및 쓰기 가능
- `ReadOnlySpan<T>`: 읽기만 가능

- 둘 다 기본적으로 데이터를 소유하지 않는 View이다.

### 📌 4. Native C/C++ 메모리 참조

#### 🔹 C++ DLL

```cpp
struct Vertex
{
    double x, y, z;
};

extern "C" __declspec(dllexport)
const Vertex* __cdecl GetVertices(int* count);
```

#### 🔹 C# P/Invoke

```csharp
[StructLayout(LayoutKind.Sequential)]
public struct Vertex
{
    public double x, y, z;
}

[DllImport("MeshKernel.dll",
    CallingConvention = CallingConvention.Cdecl)]
public static extern IntPtr GetVertices(out int count);
```

#### 🔹 ReadOnlySpan 사용

```csharp
unsafe
{
    IntPtr ptr = GetVertices(out int count);

    if (count < 0 || (count > 0 && ptr == IntPtr.Zero))
        throw new InvalidOperationException();

    ReadOnlySpan<Vertex> vertices =
        new ReadOnlySpan<Vertex>(ptr.ToPointer(), count);

    for (int i = 0; i < vertices.Length; ++i)
    {
        Console.WriteLine(vertices[i].x);
    }
}
```

- C++ 배열을 C# 배열로 복사하지 않고 직접 읽는다.
- 단, Native 구조체와 C# 구조체의 크기 및 필드 Offset이 일치해야 한다.

### 📌 5. Slice와 복사

```csharp
ReadOnlySpan<int> span = data;

// 복사 없음
ReadOnlySpan<int> part = span.Slice(1, 2);

// 새로운 배열 생성 및 복사
int[] copied = span.ToArray();
```

- `Slice()`는 새로운 메모리를 할당하지 않는다.
- `ToArray()`는 데이터를 복사하고 별도의 관리형 배열을 생성한다.

### 📌 6. ref struct 제약

- `ReadOnlySpan<T>`은 `ref struct`이므로 일반적인 클래스 필드에 저장할 수 없다.

```csharp
class MeshView
{
    // 컴파일 오류
    // private ReadOnlySpan<Vertex> vertices;
}
```

- 주요 제약:
  - 일반 클래스의 인스턴스 필드로 저장 불가
  - Boxing 불가
  - 일반적인 배열의 요소 타입으로 사용 불가
  - 비동기 함수에서는 수명 및 사용 범위에 제한이 있음
  - 일반적으로 함수 내부의 짧은 범위에서 사용


### 📌 7. Native 메모리 수명 주의

```cpp
const Vertex* ptr = vertices.data();
vertices.push_back(newVertex);
```

- `std::vector`가 재할당되면 이전 포인터는 무효화될 수 있다.
- `ReadOnlySpan<T>`은 Native 메모리를 보호하지 않는다.

- 따라서 Native 메모리를 참조하는 동안 다음 조건을 보장해야 한다.
  - 1. 메모리가 해제되지 않을 것
  - 2. 메모리 주소가 변경되지 않을 것
  - 3. 다른 스레드에서 안전하지 않은 동시 수정이 발생하지 않을 것

- 필요하면 Native API에서 `AcquireView()` / `ReleaseView()` 방식으로 메모리 수명을 관리한다.

### 📌 8. 대용량 CAD/FEM 데이터 활용

- Vertex 1,000만 개를 `double x, y, z`로 저장하면 약 240MB의 메모리가 필요하다.

| 접근 방식 | 추가 CPU 데이터 복사 |
|---|---|
| `Marshal.Copy` 등으로 별도 배열 생성 | 발생 |
| Native Pointer + `ReadOnlySpan<T>` | 없음 |
| `ReadOnlySpan<T>.ToArray()` | 발생 |

- `ReadOnlySpan<T>`을 사용하면 C++과 C# 사이의 불필요한 CPU 메모리 복사를 제거할 수 있다.
- 단, GPU 렌더링을 위한 버퍼 업로드는 별도 과정이다.

---

### 📌 핵심 정리

- **`ReadOnlySpan<T>` = Pointer + Length 기반의 읽기 전용 메모리 View**
  - 데이터를 소유하거나 복사하지 않는다.
  - `Slice()`로 일부 영역을 효율적으로 참조한다.
  - `ToArray()`는 실제 메모리 복사를 수행한다.
  - Native 메모리의 수명은 별도로 보장해야 한다.
  - 대용량 C/C++ 라이브러리와 C# GUI 연동에 유용하다.

---
