# C# Casting
C#의 Cast<T>(), OfType<T>(), 명시적 캐스팅 (T), 그리고 as 연산자까지 다양한 캐스팅 방식 예제

### 🧠 C# 캐스팅 연산 핵심 요약
| 캐스팅 방식     | 설명                                       | 실패 시 동작             |
|----------------|--------------------------------------------|--------------------------|
| `Cast<T>()`    | 모든 요소를 T로 강제 캐스팅                | ❌ `InvalidCastException` 발생 |
| `OfType<T>()`  | T 타입인 요소만 필터링 후 캐스팅           | ✅ 해당 타입만 반환       |
| `(T)`          | 명시적 캐스팅                              | ❌ `InvalidCastException` 발생 |
| `as T`         | 안전한 캐스팅 (참조/nullable 타입만 가능) | ✅ 실패 시 `null` 반환    |

## C# 예제
```csharp
namespace GrammarTest
{
    interface IThing
    {
        
    }
    class Thing : IThing
    {
    }
    class Program
    {
        public static void Main(string[] args)
        {
            IEnumerable<IThing> things =  new IThing[] {new Thing()};
            IEnumerable<Thing> things2 = things.Cast<Thing>();
            IEnumerable<Thing> things3 = things.OfType<Thing>();
            
            Console.WriteLine(things2.Count());
            //1
            Console.WriteLine(things3.Count());
            //2
            
            //double[] doubles = new[] {1, 2, 3}.Cast<double>().ToArray(); //Runtime Error
            double[] doubles1 = new[] {1, 2, 3}.Select(i => (double) i).ToArray();
            foreach (var val in doubles1)
            {
                Console.WriteLine(val);
            }
            /*
             1
             2
             3
             */
            
            object value = "-1";
            int? number = value as int?;
            
            Console.WriteLine(number == null);
            //True

            if (number.HasValue)
            {
                Console.WriteLine(Math.Abs(number.Value));
            }
        }
    }
}

```

## 🧩 C++ 예제
```cpp
#include <iostream>
#include <vector>
#include <memory>
#include <typeinfo>

struct IThing {
    virtual ~IThing() = default;
};

struct Thing : IThing {};

int main() {
    std::vector<std::shared_ptr<IThing>> things = { std::make_shared<Thing>() };

    // Cast: dynamic_pointer_cast (safe)
    std::vector<std::shared_ptr<Thing>> things2;
    for (auto& item : things) {
        if (auto casted = std::dynamic_pointer_cast<Thing>(item)) {
            things2.push_back(casted);
        }
    }

    std::cout << things2.size() << std::endl; // 1

    // double casting
    std::vector<int> ints = {1, 2, 3};
    std::vector<double> doubles;
    for (int i : ints) {
        doubles.push_back(static_cast<double>(i));
    }

    for (double d : doubles) {
        std::cout << d << std::endl;
    }

    // Safe cast with typeid
    std::shared_ptr<IThing> value = std::make_shared<Thing>();
    if (typeid(*value) == typeid(Thing)) {
        std::cout << "It's a Thing!" << std::endl;
    }
}

```

☕ Java 예제
```java
import java.util.*;

interface IThing {}

class Thing implements IThing {}

public class CastExample {
    public static void main(String[] args) {
        List<IThing> things = Arrays.asList(new Thing());

        // Cast: unsafe, requires instanceof
        List<Thing> things2 = new ArrayList<>();
        for (IThing item : things) {
            if (item instanceof Thing) {
                things2.add((Thing)item);
            }
        }

        System.out.println(things2.size()); // 1

        // double casting
        List<Integer> ints = Arrays.asList(1, 2, 3);
        List<Double> doubles = new ArrayList<>();
        for (int i : ints) {
            doubles.add((double)i);
        }

        for (double d : doubles) {
            System.out.println(d);
        }

        // Safe cast with instanceof
        Object value = "-1";
        Integer number = (value instanceof Integer) ? (Integer)value : null;
        System.out.println(number == null); // true
    }
}

```

🐍 Python 예제
```python
class IThing:
    pass

class Thing(IThing):
    pass

things = [Thing()]

# Cast: isinstance check
things2 = [x for x in things if isinstance(x, Thing)]
print(len(things2))  # 1

# double casting
ints = [1, 2, 3]
doubles = [float(i) for i in ints]
for d in doubles:
    print(d)

# Safe cast
value = "-1"
number = int(value) if isinstance(value, int) else None
print(number is None)  # True
```


✅ 비교 요약
| 언어   | 캐스팅 방식               | 안전성       | 실패 시 동작           | 필터링 기능        | 예시 코드 요약                          |
|--------|---------------------------|--------------|------------------------|--------------------|-----------------------------------------|
| C#     | `Cast<T>()`               | ❌ 예외 발생 | `InvalidCastException` | ❌ 모든 요소 시도   | `things.Cast<Thing>()`                  |
|        | `OfType<T>()`             | ✅ 안전      | 무시됨 (필터링됨)      | ✅ 해당 타입만 추출 | `things.OfType<Thing>()`                |
|        | `(T)` 명시적 캐스트       | ❌ 예외 발생 | `InvalidCastException` | ❌                 | `(int)value`                            |
|        | `as T`                    | ✅ 안전      | `null` 반환             | ❌                 | `value as int?`                         |
| C++    | `static_cast<T>()`        | ❌ 위험      | 컴파일 또는 런타임 오류 | ❌                 | `static_cast<double>(i)`                |
|        | `dynamic_cast<T>()`       | ✅ 안전      | `nullptr` 반환          | ❌                 | `dynamic_cast<Thing*>(ptr)`            |
| Java   | `(T)` 명시적 캐스트       | ❌ 예외 발생 | `ClassCastException`    | ❌                 | `(Thing)item`                           |
|        | `instanceof` + 캐스트     | ✅ 안전      | 조건문으로 필터링       | ✅ 수동 구현 가능   | `if (item instanceof Thing)`           |
| Python | `isinstance()` + 캐스트   | ✅ 안전      | `None` 또는 예외         | ✅ 리스트 필터링    | `[x for x in things if isinstance(x, Thing)]` |
|        | `float(i)` 등 명시적 변환 | ✅ 안전      | `ValueError` 등 발생    | ❌                 | `float(i)`                              |

---



# 🔍 차이 요약
| 메서드            | 동작 방식                                      | 실패 시 처리             |
|-------------------|-----------------------------------------------|--------------------------|
| `Cast<Thing>()`   | 모든 요소를 `Thing`으로 **강제 캐스팅**         | 타입 불일치 시 `InvalidCastException` 발생 |
| `OfType<Thing>()` | `Thing` 타입인 요소만 **필터링**하여 반환        | 타입 불일치 요소는 **무시됨**           |



## 💡 왜 결과가 다르냐면…
IEnumerable<IThing> things = new IThing[] { new Thing() };


- things2 = things.Cast<Thing>()
→ IThing 배열의 모든 요소를 Thing으로 강제 캐스팅
→ 요소가 Thing이므로 성공 → Count = 1

- things3 = things.OfType<Thing>()
→ IThing 배열에서 Thing 타입인 것만 필터링
→ 요소가 Thing이므로 포함됨 → Count = 1

그런데 왜 things3.Count()가 2로 나왔는지 의문이죠?

## 🧪 가능성 있는 원인
- 혹시 things에 new Thing() 외에 다른 Thing 객체가 추가된 상태였을 수 있어요?
- 또는 OfType<Thing>()는 하위 타입도 포함하기 때문에, Thing을 상속한 다른 타입이 들어있다면 그것도 포함됩니다.

## ✅ 확인 팁
foreach (var item in things)
{
    Console.WriteLine(item.GetType().Name);
}
이렇게 출력해보면 things에 실제로 어떤 타입이 들어있는지 확인할 수 있어요.
---

