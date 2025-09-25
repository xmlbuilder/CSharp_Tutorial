## ✅ C#의 DefaultIfEmpty() 기능
- 기능: 컬렉션이 비어 있으면 지정된 기본값을 반환, 그렇지 않으면 원래 컬렉션 반환
- 형식:
- collection.DefaultIfEmpty() → 타입의 기본값 반환 (0, null, false 등)
- collection.DefaultIfEmpty(value) → 지정된 기본값 반환
- 예시:
```csharp
var result = new List<int>().DefaultIfEmpty(10); // 빈 리스트 → [10]
var result2 = new List<int> { 1, 2 }.DefaultIfEmpty(10); // → [1, 2]
```

## 전체소스

```csharp
/*
DefaultIfEmpty() 메서드를 호출하는 시퀀스 또는 데이터 집합이 비어 있지 않으면 
시퀀스 또는 데이터 집합이 가지고 있는 데이터가 반환되며, 
데이터가 존재하지 않으면 해당 타입의 Default 값이 반환됩니다.
다음 예제는 int 타입의 List에서 DefaultIfEmpty() 메서드를 호출합니다. 
List에 데이터가 존재하므로 기존 값이 반환됩니다.
*/
class Program
{
    static void Main(string[] args)
    {
        List<int> intList = new List<int>() { 10, 20, 30 };
        IEnumerable<int> result = intList.DefaultIfEmpty();
        foreach(int num in result)
        {
            Console.WriteLine(num);
        }
        /*
        10
        20
        30
         */
    }
}



/*
데이터가 비어있는 경우
다음 예제는 int 타입의 List가 빈 값으로 할당되어 있습니다. 따라서, int 타입의 Default 값인 0이 반환됩니다.
*/

class Program
{
    static void Main(string[] args)
    {
        List<int> intList = new List<int>() { };
        IEnumerable<int> result = intList.DefaultIfEmpty();
        foreach(int num in result)
        {
            Console.WriteLine(num);
        }
        //0
    }
}


/*
다음 예제는 int 타입의 List가 빈 값으로 할당되어 있으며, 
DefaultIfEmpty() 메서드에 10을 전달했습니다. 
따라서, int 타입의 데이터 집합이 비어 있는 경우 10이 반환됩니다.
*/
class Program
{
    static void Main(string[] args)
    {
        List<int> numList = new List<int>();
        IEnumerable<int> result = numList.DefaultIfEmpty(10);
        foreach (int num in result)
        {
            Console.WriteLine(num);
        }
        //10
    }
}

class Program
{
    static void Main(string[] args)
    {
        int[] numbers = {1, 2, 3, 4, 5};
        Console.WriteLine(numbers.ElementAt(2));
        //3
        //Console.WriteLine(numbers.ElementAt(10));
        /*
        Unhandled exception. System.ArgumentOutOfRangeException: Index was out of range. 
            Must be non-negative and less than the size of the collection. (Parameter 'index')
            at System.SZArrayHelper.get_Item[T](Int32 index)
            at System.Linq.Enumerable.ElementAt[TSource](IEnumerable`1 source, Int32 index)
            at Program.Main(String[] args) in D:\Development\csharp\UE\ConsoleApp4\ConsoleApp4\Program.cs:line 9
         */
        Console.WriteLine(numbers.ElementAtOrDefault(10));
        //0
        
        Console.WriteLine(numbers.DefaultIfEmpty(-1).First()); 
        //1
    }
}

```


## 🧊 C++에서의 유사 기능
C++에는 DefaultIfEmpty()와 정확히 같은 함수는 없지만, 조건문과 람다를 활용해 유사한 동작을 구현할 수 있어요.
```cpp
#include <vector>
#include <iostream>

std::vector<int> ensureNotEmpty(const std::vector<int>& input, int defaultValue) {
    return input.empty() ? std::vector<int>{defaultValue} : input;
}

auto result = ensureNotEmpty({}, 10); // → [10]
```

- 특징: 명시적 처리 필요, 템플릿으로 일반화 가능

## ☕ Java에서의 유사 기능
Java에서는 Optional이나 Stream을 활용해 유사한 동작을 구현할 수 있어요.

### 🔹 방법 1: Optional.orElse()
```java
List<Integer> list = new ArrayList<>();
List<Integer> result = list.isEmpty() ? Arrays.asList(10) : list;
```

### 🔹 방법 2: Stream.ofNullable() (Java 9+)
```java
Stream<Integer> stream = list.isEmpty() ? Stream.of(10) : list.stream();
```


- 특징: Optional과 Stream으로 안전하게 처리 가능

## 🐍 Python에서의 유사 기능
Python은 동적 언어라서 훨씬 간단하게 처리할 수 있어요.
```python
def default_if_empty(seq, default):
    return seq if seq else [default]

result = default_if_empty([], 10)  # → [10]
result2 = default_if_empty([1, 2], 10)  # → [1, 2]
```

- 특징: if not seq로 간단하게 처리, 리스트 컴프리헨션과도 잘 어울림

## 📊 요약 비교 (Markdown 표)
| 언어   | 유사 기능 구현 방식                     | 기본값 지정 가능 | 특징 및 메모                          |
|--------|-----------------------------------------|------------------|---------------------------------------|
| C#     | `DefaultIfEmpty()` / `DefaultIfEmpty(val)` | ✅               | LINQ 기반, 예외 없이 안전한 처리       |
| C++    | 조건문 + 래퍼 함수                      | ✅               | 명시적 처리, 템플릿으로 일반화 가능    |
| Java   | `Optional`, `Stream`, 삼항 연산자        | ✅               | `Optional.orElse`, `Stream.of()` 활용 |
| Python | `if not seq` + 기본값 반환              | ✅               | 간결하고 직관적, 함수로 래핑 가능      |



이런 기능은 실무에서 빈 결과 처리, 기본값 보정, 예외 방지, UI 표시용 데이터 보완 등에 자주 활용돼요.
---

