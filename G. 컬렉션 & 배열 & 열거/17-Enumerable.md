# 🔍 C#에서의 Enumerable vs Enumerator
| 구분            | Enumerable (`IEnumerable`)                          | Enumerator (`IEnumerator`)                          |
|-----------------|-----------------------------------------------------|-----------------------------------------------------|
| 정의            | 반복 가능한 컬렉션 객체                             | 컬렉션을 순회하는 반복자 객체                      |
| 역할            | 반복을 시작할 수 있는 진입점 제공                   | 반복을 실제로 수행하며 현재 위치 추적               |
| 주요 메서드     | `GetEnumerator()`                                   | `MoveNext()`, `Current`, `Reset()`                 |
| 사용 예시       | `foreach` 문에서 사용 가능                          | `while` 루프에서 수동으로 순회 가능                |
| 상태 유지 여부  | 상태 없음 (단순히 반복자 반환)                     | 상태 있음 (현재 위치 기억)                         |
| 구현 예시       | `List<T>`, `Array`, `Dictionary`, `HashSet` 등     | `List<T>.Enumerator`, `ArrayEnumerator` 등         |
| 인터페이스      | `IEnumerable`, `IEnumerable<T>`                    | `IEnumerator`, `IEnumerator<T>`                    |
| 반복 방식       | 반복자를 생성해 반복 수행                           | `MoveNext()`로 다음 요소 이동, `Current`로 접근    |


## ✅ IEnumerable
- foreach 문에서 사용 가능
- GetEnumerator() 메서드를 통해 IEnumerator를 반환
- 대표 구현체: List<T>, Array, Dictionary, HashSet, 등
```csharp
IEnumerable<int> numbers = new List<int> {1, 2, 3};
```


## ✅ IEnumerator
- MoveNext()로 다음 요소로 이동
- Current로 현재 요소 접근
- Reset()으로 처음으로 되돌림 (거의 사용 안 함)
```csharp
int[] numbers = {1, 2, 3, 4, 5};
IEnumerator iterator = numbers.GetEnumerator();
while (iterator.MoveNext())
{
    Console.WriteLine(iterator.Current);
}
```

foreach는 내부적으로 GetEnumerator()를 호출하고, MoveNext()와 Current를 자동으로 처리해주는 sugar syntax예요.


## 🌍 다른 언어에서의 유사 개념
###  🧊 C++: iterator와 range-based for
- C++의 STL 컨테이너는 begin()과 end()를 통해 반복자를 반환
- 반복자는 *it으로 값 접근, ++it으로 다음 요소 이동
```cpp
std::vector<int> numbers = {1, 2, 3, 4, 5};
for (auto it = numbers.begin(); it != numbers.end(); ++it) {
    std::cout << *it << std::endl;
}
```

- C++11부터는 for (auto x : numbers)로 간결하게 가능

###  ☕ Java: Iterable과 Iterator
- Iterable 인터페이스는 iterator() 메서드를 제공
- Iterator는 hasNext()와 next() 메서드로 반복
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Iterator<Integer> iterator = numbers.iterator();
while (iterator.hasNext()) {
    System.out.println(iterator.next());
}
```

- for-each 문도 내부적으로 Iterator를 사용

### 🐍 Python: Iterable과 Iterator
- 모든 리스트, 튜플, 딕셔너리 등은 Iterable
- iter()로 Iterator를 얻고, next()로 요소 접근
```python
numbers = [1, 2, 3, 4, 5]
iterator = iter(numbers)
while True:
    try:
        print(next(iterator))
    except StopIteration:
        break
```

- for x in numbers는 내부적으로 iter()와 next()를 사용

## 📊 요약 비교 (Markdown 표)
| 언어   | 반복 가능한 객체 (`Enumerable`) | 반복자 (`Enumerator`)         | 반복 방식                      |
|--------|-------------------------------|-------------------------------|-------------------------------|
| C#     | `IEnumerable`                 | `IEnumerator`                 | `foreach`, `MoveNext()`       |
| C++    | STL 컨테이너 (`vector`, 등)   | `iterator` (`begin`, `end`)   | `for`, `++it`, `*it`          |
| Java   | `Iterable`                    | `Iterator`                    | `for-each`, `hasNext()`, `next()` |
| Python | `Iterable` (`list`, `dict`)   | `Iterator` (`iter()`)         | `for`, `next()`, `StopIteration` |



## ✨ 마무리 팁
- Enumerable은 반복 가능한 객체이고, Enumerator는 반복을 수행하는 도구예요.
- foreach나 for 문은 내부적으로 Enumerator를 사용하지만, 개발자가 직접 다룰 일은 드물어요.
- 하지만 성능 최적화나 커스텀 컬렉션을 만들 때는 IEnumerator를 직접 구현해야 할 수도 있어요.

---



## 🧱 목표: 우리가 직접 만든 컬렉션을 foreach로 순회할 수 있게 만들기
예제: MyNumbers라는 클래스가 1부터 3까지 숫자를 반환하도록 만들기

### 1️⃣ IEnumerator 구현하기
먼저 반복자부터 만들어볼게요. 이 클래스는 실제로 반복을 수행합니다.
```csharp
public class MyEnumerator : IEnumerator<int>
{
    private int _position = 0;

    public int Current
    {
        get
        {
            return _position;
        }
    }

    object IEnumerator.Current => Current;

    public bool MoveNext()
    {
        _position++;
        return _position <= 3;
    }

    public void Reset()
    {
        _position = 0;
    }

    public void Dispose()
    {
        // 리소스 해제 필요 시 사용
    }
}
```

- MoveNext()는 다음 요소로 이동
- Current는 현재 요소 반환
- Reset()은 처음으로 되돌림 (거의 안 씀)

### 2️⃣ IEnumerable 구현하기
이제 MyNumbers 클래스가 IEnumerable<int>를 구현해서 foreach에서 사용 가능하게 만들어요.
```csharp
public class MyNumbers : IEnumerable<int>
{
    public IEnumerator<int> GetEnumerator()
    {
        return new MyEnumerator();
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
}
```

- GetEnumerator()에서 우리가 만든 MyEnumerator를 반환
- IEnumerable과 IEnumerable<T> 둘 다 구현해야 foreach에서 문제 없이 작동

### 3️⃣ 사용 예시
```csharp
class Program
{
    static void Main()
    {
        var numbers = new MyNumbers();
        foreach (var number in numbers)
        {
            Console.WriteLine(number);
        }
    }
}
```

출력:
```
1
2
3
```


## 🧠 핵심 요약

| 개념        | 설명 및 역할                                                                 |
|-------------|------------------------------------------------------------------------------|
| IEnumerator | 반복자의 역할을 수행하며 `MoveNext()`로 이동하고 `Current`로 현재 값을 반환 |
| IEnumerable | 반복 가능한 컬렉션으로 `GetEnumerator()`를 통해 `IEnumerator`를 생성         |
| foreach     | 내부적으로 `GetEnumerator()` → `MoveNext()` → `Current` 순으로 호출하여 반복 |


----


