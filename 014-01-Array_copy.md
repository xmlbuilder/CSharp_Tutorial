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

---


# C#의 Array.Clone() 개념 요약

- Clone()은 배열의 구조를 복사하지만, 참조형 요소는 참조만 복사합니다.
- 즉, **값형(int, double 등)**은 완전히 복사되고, **참조형(string, object 등)**은 주소만 복사됩니다.
- ReferenceEquals(nArray1, nCloneArray)는 false → 서로 다른 배열 인스턴스

## C# 
```csharp
class Program
{
    static int Main(string[] args)
    {
        /*
        Array.Clone() 함수는 배열의 전체를 한꺼번에 복사하는 함수이다. 
        배열안의 값이 참조 값 일때는 참조값만 복사된다.
        */

        int[] nArray1 = { 1, 2, 3, 4, 5 };
        int[] nCloneArray = (int[])nArray1.Clone();

        nCloneArray[2] = 30;

        Console.WriteLine(ReferenceEquals(nArray1, nCloneArray));
        //False
        
        foreach(int value in nArray1)
        {
            Console.WriteLine(value); //1 2 3 4 5
        }

        foreach (int value in nCloneArray)
        {
            Console.WriteLine(value); // 1 2 30 4 5
        }

        string[] Days = { "일", "월", "화", "수", "목", "금", "토" };
        string[] newDays = (string[])Days.Clone();

        foreach(string str in newDays)
        {
            Console.WriteLine(str); //일 월 화 수 목 금 토 
        }
        return 0;
        
    }
}
```

## 🧠 C++에서의 배열 복사 개념
C++에서는 Array.Clone() 같은 내장 메서드는 없지만, 배열 복사는 다음과 같이 처리됩니다:

### ✅ 기본 배열 복사 (값형 배열)
```cpp
int arr1[] = {1, 2, 3, 4, 5};
int arr2[5];

for (int i = 0; i < 5; ++i) {
    arr2[i] = arr1[i]; // 값 복사
}
```

- **값형 배열(int, double 등)**은 직접 복사하면 깊은 복사처럼 작동
- 배열 자체는 스택에 존재하며, 요소 하나하나를 복사함

### ⚠️ 포인터 배열 또는 객체 배열
```cpp
std::string* arr1[3] = { new std::string("월"), new std::string("화"), new std::string("수") };
std::string* arr2[3];

for (int i = 0; i < 3; ++i) {
    arr2[i] = arr1[i]; // 얕은 복사 (주소만 복사됨)
}
```

- 포인터를 복사하면 얕은 복사가 발생 → 같은 객체를 참조
- 깊은 복사를 하려면 new로 새 객체를 생성해야 함

### ✅ 깊은 복사 예시
```cpp
for (int i = 0; i < 3; ++i) {
    arr2[i] = new std::string(*arr1[i]); // 깊은 복사
}
```


## 🔍 Java에서의 배열 복사

### ✅ 얕은 복사 (Shallow Copy)
```java
int[] arr1 = {1, 2, 3};
int[] arr2 = arr1.clone(); // 얕은 복사

arr2[1] = 99;
System.out.println(Arrays.toString(arr1)); // [1, 2, 3]
System.out.println(Arrays.toString(arr2)); // [1, 99, 3]
```

### ✅ Java에서 기본형 배열 복사: 깊은 복사처럼 작동
```java
int[] arr1 = {1, 2, 3};
int[] arr2 = arr1.clone(); // 얕은 복사라고 부르지만...

arr2[1] = 99;
System.out.println(Arrays.toString(arr1)); // [1, 2, 3]
System.out.println(Arrays.toString(arr2)); // [1, 99, 3]
```

- arr1.clone()은 새로운 배열 객체를 생성합니다.
- 배열 요소가 int 같은 **기본형(primitive type)**일 경우, 값 자체가 복사됩니다.
- 즉, arr1과 arr2는 서로 다른 배열 객체이고, 내부 값도 독립적입니다.
👉 그래서 결과적으로는 깊은 복사처럼 보이지만, 기술적으로는 얕은 복사입니다. 왜냐하면 clone()은 배열의 요소를 그대로 복사할 뿐, 참조형일 경우에는 주소만 복사하거든요.

## 🔍 참조형 배열일 경우
```java
String[] arr1 = {"월", "화", "수"};
String[] arr2 = arr1.clone();

arr2[0] = "일";
System.out.println(Arrays.toString(arr1)); // [월, 화, 수]
System.out.println(Arrays.toString(arr2)); // [일, 화, 수]
```

- String은 참조형이지만 **불변 객체(immutable)**라서 직접 수정은 안 돼요.
- 하지만 arr1[0] == arr2[0]는 true → 같은 주소를 참조

## 📌 배열 복사 요약 (Java 기준)

| 타입             | 복사 방식       | 복사 결과               | 설명                                      |
|------------------|----------------|--------------------------|-------------------------------------------|
| `int`, `double`  | `clone()`      | 값 자체 복사됨           | 기본형은 값이 직접 복사되어 깊은 복사처럼 작동 |
| `String`, `Object` | `clone()`    | 참조값만 복사됨          | 참조형은 주소만 복사되어 얕은 복사로 작동     |


💡 그래서 Java에서 clone()은 얕은 복사 메서드지만, 기본형 배열에는 깊은 복사처럼 동작한다는 점이 핵심이에요.

## 📌 불변 객체 vs 가변 객체 배열 복사 요약

| 객체 유형       | 복사 방식       | 참조 공유 여부 | 원본 영향 여부 | 설명 |
|------------------|----------------|----------------|----------------|------|
| **불변 객체** (`String`, `Integer`) | 참조 복사       | ✅ 공유됨       | ❌ 영향 없음     | 참조는 공유되지만 객체 자체는 변경 불가 |
| **가변 객체** (`ArrayList`, 사용자 정의 클래스 등) | 참조 복사       | ✅ 공유됨       | ✅ 영향 있음     | 참조된 객체의 내부 상태 변경 가능 |

- clone()은 C#과 마찬가지로 배열 자체는 새로 생성되지만, 참조형 요소는 주소만 복사됨

### ✅ 깊은 복사 (Deep Copy)
```java
String[] days = {"월", "화", "수"};
String[] newDays = new String[days.length];
for (int i = 0; i < days.length; i++) {
    newDays[i] = new String(days[i]); // 새로운 객체 생성
}
```


## 🐍 Python에서의 배열 복사
### ✅ 얕은 복사
```python
arr1 = [1, 2, 3]
arr2 = arr1.copy()  # 또는 arr1[:]

arr2[1] = 99
print(arr1)  # [1, 2, 3]
print(arr2)  # [1, 99, 3]
```

- list.copy() 또는 슬라이싱 [:]은 얕은 복사
- 참조형 객체가 들어있으면 주소만 복사됨
### ✅ 깊은 복사
```python
import copy

arr1 = [["월", "화"], ["수", "목"]]
arr2 = copy.deepcopy(arr1)

arr2[0][0] = "일"
print(arr1)  # [['월', '화'], ['수', '목']]
print(arr2)  # [['일', '화'], ['수', '목']]
```


## 🧬 배열 복사 개념 비교 요약

| 언어     | 복사 방식       | 사용 메서드 / 문법        | 참조형 처리 방식         | 특징 요약                         |
|----------|----------------|---------------------------|--------------------------|----------------------------------|
| **C#**   | 얕은 복사       | `Array.Clone()`           | 참조만 복사              | 배열 구조는 복사되지만 참조형은 주소 공유 |
| **Java** | 얕은 복사       | `clone()`                 | 참조만 복사              | `Object.clone()` 구현 필요       |
| **Python** | 얕은 복사     | `list.copy()` 또는 `[:]`  | 참조만 복사              | 리스트 구조만 복사됨             |
| **Python** | 깊은 복사     | `copy.deepcopy()`         | 참조 객체까지 복사       | 중첩 리스트나 객체까지 완전 복사 |
| **C++**  | 얕은/깊은 복사  | 직접 루프 또는 `std::copy` | 포인터는 얕은 복사, 값형은 깊은 복사 | 자동 복사 없음, 명시적 구현 필요 |

> 💡 C++은 자동 복사 메서드가 없기 때문에, 복사 방식은 **프로그래머가 직접 제어**해야 합니다.
> 💡 **얕은 복사**는 배열 자체는 새로 만들지만, 내부의 참조형 요소는 같은 주소를 공유합니다.  
> 💡 **깊은 복사**는 내부 요소까지 모두 새로 복사하여 완전히 독립된 구조를 만듭니다.

---



