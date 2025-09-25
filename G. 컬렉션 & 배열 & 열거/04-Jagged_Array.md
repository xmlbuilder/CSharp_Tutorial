# Jagged Array (들쭉날쭉 배열) — C# vs Java vs C++

> **정의**  
> *Jagged array*는 “배열의 배열” 구조로, 각 내부 배열의 길이가 **서로 달라질 수 있는** 자료구조입니다.  
> 2차원 “정방” 배열과 달리 한 행(row)의 길이가 가변적입니다.

---

## 1) C#에서의 Jagged Array

### 기본 예제
```csharp
int[][] a = new int[8][];           
for (int i = 0; i < a.Length; i++)
    a[i] = new int[10];             

for (int i = 0; i < a[2].Length; i++)
    Console.WriteLine(a[2][i]);     
```

### 서로 다른 길이의 내부 배열
```csharp
int[][] jagged = new int[4][];
jagged[0] = new int[2];
jagged[1] = new int[4];
jagged[2] = new int[6];
jagged[3] = new int[7];

foreach (var row in jagged)
    Console.WriteLine(row.Length);  // 2, 4, 6, 7
```

---

## 2) Java에서의 대응

Java의 2차원 배열은 본질적으로 **배열의 배열**입니다.

```java
int[][] a = new int[4][];     
for (int i = 0; i < a.length; i++)
    a[i] = new int[i + 1];    

for (int i = 0; i < a.length; i++) {
    System.out.println(a[i].length);
}
```

---

## 3) C++에서의 대응

### (A) `std::vector<std::vector<int>>`
```cpp
#include <vector>
#include <iostream>

int main() {
    std::vector<std::vector<int>> j(4);
    j[0] = std::vector<int>(2);
    j[1] = std::vector<int>(4);
    j[2] = std::vector<int>(6);
    j[3] = std::vector<int>(7);

    for (const auto& row : j)
        std::cout << row.size() << "
";
}
```

### (B) 포인터 배열 (권장 X)
```cpp
int** a = new int*[4];
a[0] = new int[2]{};
a[1] = new int[4]{};

// 해제 필요
for(int i=0; i<4; i++)
{
  delete[] a[i]; 
}
delete[] a;

```

---

## 4) 공통 패턴

### 값 채우기
- **C#**
  ```csharp
  int[][] j = new int[3][];
  for (int i = 0; i < j.Length; i++)
      j[i] = Enumerable.Range(0, i + 2).ToArray();
  ```
- **Java**
  ```java
  int[][] j = new int[3][];
  for (int i = 0; i < j.length; i++) {
      j[i] = new int[i + 2];
      for (int k = 0; k < j[i].length; k++) j[i][k] = k;
  }
  ```
- **C++**
  ```cpp
  std::vector<std::vector<int>> j(3);
  for (int i = 0; i < 3; ++i) {
      j[i].resize(i + 2);
      for (int k = 0; k < (int)j[i].size(); ++k) j[i][k] = k;
  }
  ```

### 순회
- **C#**
  ```csharp
  foreach (var row in j)
      foreach (var v in row)
          Console.WriteLine(v);
  ```
- **Java**
  ```java
  for (int[] row : j)
      for (int v : row)
          System.out.println(v);
  ```
- **C++**
  ```cpp
  for (const auto& row : j)
      for (int v : row)
          std::cout << v << "
";
  ```

---

## 5) 한눈 비교표

| 항목                 | C# `int[][]`                   | Java `int[][]`                | C++ `std::vector<std::vector<int>>` |
|----------------------|--------------------------------|-------------------------------|--------------------------------------|
| 구조                 | 배열의 배열                    | 배열의 배열                   | 벡터의 벡터                          |
| 내부 길이 가변       | 가능                           | 가능                          | 가능                                 |
| 기본값               | 값형은 0, 참조는 null          | 값형은 0, 참조는 null         | 초기화 시 0 보장 (`vector`)          |
| 초기화 실수 위험     | 미할당 시 `NullReferenceException` | 미할당 시 NPE                 | 포인터 사용 시 누수 위험             |
| 메모리 연속성        | 행마다 분리(비연속)            | 행마다 분리(비연속)           | 행마다 분리(비연속)                  |
| 정방 2D 대안         | `int[,]`                       | 없음                          | 평면 버퍼 + 인덱싱                   |

---

## 6) 요약
- **Jagged array**는 “행 길이 가변”을 표현하는 **배열의 배열**.  
- **C#·Java**: `T[][]` 구조. **C++**: `std::vector<std::vector<T>>`가 안전하고 실용적.  
- **정방 2D 연산**에는 연속 메모리 배열이 더 적합.
