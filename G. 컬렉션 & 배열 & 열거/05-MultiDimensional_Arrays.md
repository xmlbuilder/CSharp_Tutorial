# C#의 다차원 배열(`T[,]`) — 개념과 Java/C++에서의 구현 방법

## 1) 개념 요약
- **직사각형(정방) 다차원 배열**: `T[,]`, `T[,,]` 처럼 **모든 행/열 길이가 고정**이고 **하나의 연속 메모리 블록**에 저장됩니다.
- **Jagged 배열(`T[][]`)과의 차이**: Jagged는 “배열의 배열”로 행 길이가 서로 달라질 수 있고, 각 행이 **서로 다른 메모리 블록**입니다.
- **순회/색인**: `arr[i, j]` 형태의 2D 색인. 차원별 길이는 `GetLength(dim)`으로 얻습니다.

---

## 2) C# 기본 사용법

```csharp
// 생성
int rows = 3, cols = 5;
int[,] grid = new int[rows, cols];

// 길이/순회
Console.WriteLine(grid.Rank);          // 2 (차원 수)
Console.WriteLine(grid.GetLength(0));  // 3 (행 수)
Console.WriteLine(grid.GetLength(1));  // 5 (열 수)
Console.WriteLine(grid.Length);        // 15 (총 원소 수)

// 값 설정/접근
grid[1, 3] = 42;
Console.WriteLine(grid[1, 3]); // 42

// 이중 for 권장
for (int i = 0; i < grid.GetLength(0); i++)
  for (int j = 0; j < grid.GetLength(1); j++)
    grid[i, j] = i * cols + j;
```

### 언제 `[,]`를 쓰고, 언제 `[][]`를 쓸까?
- **`[,]` (정방/연속 메모리)**: 이미지/격자/행렬처럼 **모든 행 길이가 같고**, **캐시 지역성**이 중요한 연속 처리에 유리.
- **`[][]` (Jagged)**: 행마다 길이가 달라지는 데이터나 **행 단위 교체/확장**이 잦을 때 유리.

---

## 3) Java에서는?
Java에는 C#의 `[,]` 같은 **진짜 직사각형 다차원 배열 타입이 없습니다.**

```java
// Jagged 기본
int rows = 3, cols = 5;
int[][] grid = new int[rows][cols]; 

grid[1][3] = 42;
System.out.println(grid[1][3]); // 42

// 연속 메모리 2D가 필요하면 1D 버퍼 + 인덱싱
int[] buf = new int[rows * cols];
int idx = 1 * cols + 3; 
buf[idx] = 42;
System.out.println(buf[1 * cols + 3]);
```

---

## 4) C++에서는?

### (A) 연속 메모리 기반 2D 표현
```cpp
#include <vector>
#include <iostream>

int main() {
    int rows = 3, cols = 5;
    std::vector<int> buf(rows * cols, 0);

    auto at = [&](int i, int j) -> int& { return buf[i * cols + j]; };

    at(1, 3) = 42;
    std::cout << at(1, 3) << "\n"; 
}
```

- **C++23 `std::mdspan`**: 1D 버퍼를 **다차원 뷰**로 보여주는 표준 뷰 타입.
```cpp
#include <mdspan>
#include <vector>
#include <iostream>
using std::extents, std::mdspan, std::layout_right;

int main() {
    int rows = 3, cols = 5;
    std::vector<int> buf(rows * cols);
    mdspan<int, extents<size_t, std::dynamic_extent, std::dynamic_extent>, layout_right>
        A(buf.data(), rows, cols);

    A(1, 3) = 42;
    std::cout << A(1, 3) << "\n"; 
}
```

### (B) Jagged(벡터의 벡터)
```cpp
#include <vector>
std::vector<std::vector<int>> jagged(rows, std::vector<int>(cols));
```

---

## 5) 공통 비교표

| 항목                   | C# `T[,]` (다차원)                 | Java                        | C++                                   |
|------------------------|------------------------------------|-----------------------------|----------------------------------------|
| 형태                   | 진짜 다차원 배열(정방)             | 배열의 배열(Jagged)         | 정적 2D 배열, 1D + 인덱싱, `mdspan`, Jagged |
| 메모리 배치            | **연속 메모리**                     | 행마다 별도 블록(비연속)    | 선택 가능 (연속: 1D/정적배열/`mdspan`, 비연속: 벡터의 벡터) |
| 색인 문법              | `a[i, j]`                           | `a[i][j]`                   | `buf[i*cols + j]`, `a[i][j]`, `A(i,j)`(mdspan) |
| 크기/차원 정보         | `GetLength(d)`, `Rank`, `Length`    | `a.length`, `a[i].length`   | 별도 변수/뷰 필요 |
| 행 길이 가변           | 불가(고정)                          | 가능                         | 가능(Jagged)                           |
| 캐시 지역성/연산 성능  | 좋음(연속)                          | 떨어질 수 있음(비연속)       | 1D/정적/`mdspan`는 좋음, Jagged는 떨어질 수 있음 |

---

## 6) 요약
- C#의 `[,]`는 **연속 메모리의 진짜 다차원 배열**.
- Java는 배열의 배열만 지원 → **1D 버퍼 매핑**으로 흉내 가능.
- C++는 **여러 선택지**가 있으며, **1D + 인덱싱**(또는 C++23 `mdspan`)이 가장 유사.
---

