# 📦 배열 복사 방법 비교: C# vs Java vs C++
| 복사 방식 | C# | Java | C++ |
|----------|----|------|-----| 
| ✅ 일정 개수만 복사 | Array.Copy(src, dst, length) | System.arraycopy(src, 0, dst, 0, length) | std::copy(src, src + length, dst) | 
| ✅ 지정된 인덱스에 복사 | src.CopyTo(dst, index) | System.arraycopy(src, 0, dst, index, src.length) | std::copy(src, src + length, dst + index) | 
| ✅ 전체 복사본 생성 | var clone = (int[])src.Clone() | int[] clone = Arrays.copyOf(src, src.length) | std::vector<int> clone(src, src + length) | 



## 💡 각 언어별 예제 코드
### 🔷 C# 예제
```csharp
// 1. 지정된 개수만 복사
Array.Copy(sourceArray1, destinationArray1, 3);

// 2. 지정된 인덱스에 복사
sourceArray2.CopyTo(destinationArray2, 2);

// 3. 전체 복사본 생성
var destinationArray3 = (int[])sourceArray3.Clone();
```


### 🔶 Java 예제
```java
// 1. 지정된 개수만 복사
System.arraycopy(sourceArray1, 0, destinationArray1, 0, 3);

// 2. 지정된 인덱스에 복사
System.arraycopy(sourceArray2, 0, destinationArray2, 2, sourceArray2.length);

// 3. 전체 복사본 생성
int[] destinationArray3 = Arrays.copyOf(sourceArray3, sourceArray3.length);

```

### 🔷 C++ 예제
```cpp
#include <algorithm>
#include <vector>

// 1. 지정된 개수만 복사
std::copy(sourceArray1, sourceArray1 + 3, destinationArray1);

// 2. 지정된 인덱스에 복사
std::copy(sourceArray2, sourceArray2 + 3, destinationArray2 + 2);

// 3. 전체 복사본 생성
// 원본 배열 정의
int sourceArray3[] = { 11, 12, 7 };
std::vector<int> destinationArray3(sourceArray3, sourceArray3 + 3);
```


## 🧠 참고 포인트
- C#의 CopyTo는 인스턴스 메서드지만, Java와 C++에서는 명시적으로 시작 인덱스를 지정해야 함
- C++은 배열보다 std::vector를 사용하는 것이 더 안전하고 유연함
- Java는 Arrays.copyOf를 통해 복사본을 쉽게 생성할 수 있음
