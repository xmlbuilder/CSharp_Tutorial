#Array.Clear()
C#에서 배열의 일부 또는 전체를 **초기화(값 제거)**하는 데 사용되죠. 
이 개념을 C++, Java, Python에서 어떻게 다루는지도 함께 비교.

## 🧼 C#의 Array.Clear() 개념
```csharp
Array.Clear(array, startIndex, length);
```

- 배열의 startIndex부터 length만큼의 요소를 기본값으로 초기화
- int → 0, string → null, bool → false 등
- 값을 제거하는 게 아니라, 기본값으로 덮어씀
## 예시 결과
```csharp
int[] arr = {1, 2, 3, 4, 5};
Array.Clear(arr, 2, 3); // arr = {1, 2, 0, 0, 0}
```


## 💻 Java에서 배열 초기화
Java에는 Array.Clear() 같은 내장 메서드는 없지만, 수동으로 초기화합니다.

### ✅ 전체 초기화
```java
int[] arr = {1, 2, 3, 4, 5};
Arrays.fill(arr, 0); // 모든 요소를 0으로 설정
```

### ✅ 부분 초기화
```jajva
Arrays.fill(arr, 2, 5, 0); // index 2~4까지 0으로 설정
```

Arrays.fill()은 C#의 Array.Clear()와 유사하지만, 명시적으로 어떤 값으로 채울지 지정해야 해요.


###🐍 Python에서 배열 초기화
Python은 리스트 기반이기 때문에 초기화 방식이 더 유연합니다.

### ✅ 전체 초기화
```python
arr = [1, 2, 3, 4, 5]
arr = [0] * len(arr)  # [0, 0, 0, 0, 0]
```

### ✅ 부분 초기화
```python
arr[2:5] = [0] * 3  # [1, 2, 0, 0, 0]
```

Python은 슬라이싱을 통해 부분 초기화가 간단하게 가능해요.


## 🧠 C++에서 배열 초기화
C++은 배열이 정적 또는 동적일 수 있어서 방식이 다양합니다.

### ✅ 전체 초기화
```cpp
int arr[5] = {1, 2, 3, 4, 5};
std::fill(arr, arr + 5, 0); // 모든 요소를 0으로 설정
```

### ✅ 부분 초기화
```cpp
std::fill(arr + 2, arr + 5, 0); // index 2~4까지 0으로 설정
```

std::fill()은 C++ STL에서 제공하는 유용한 초기화 함수입니다.


## 📌 배열 초기화 비교 요약

| 언어     | 전체 초기화 방법            | 부분 초기화 방법             | 특징 요약                          |
|----------|-----------------------------|-------------------------------|-----------------------------------|
| **C#**   | `Array.Clear(arr, 0, len)`  | `Array.Clear(arr, i, n)`      | 기본값으로 초기화, 자동 타입별 처리 |
| **Java** | `Arrays.fill(arr, 0)`       | `Arrays.fill(arr, i, j, 0)`   | 명시적 값 지정 필요               |
| **Python** | `[0] * len(arr)`          | `arr[i:j] = [0]

----
