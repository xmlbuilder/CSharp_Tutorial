# foreach / ForEach 비교 (C#, Java, C++)

## 1. 개념

-   **C# `List<T>.ForEach`**
    `List<T>` 컬렉션의 각 요소에 대해 지정된 `Action<T>`를 실행.
-   **Java `for-each` 구문 & forEach 메서드**
    `for-each` 구문 또는 `Iterable.forEach` 메서드를 사용.
-   **C++ `std::for_each`**
    `<algorithm>`에 정의. 범위의 각 요소에 대해 지정된 함수/함수자 호출.

------------------------------------------------------------------------

## 2. C# 예제

``` csharp
List<string> names = new List<string> { "Bruce", "Alfred", "Tim", "Richard" };

// 메서드 그룹 사용
names.ForEach(Console.WriteLine);

// 람다식
names.ForEach(name => Console.WriteLine(name));

// 무명 델리게이트
names.ForEach(delegate(string name) {
    Console.WriteLine(name);
});
```

출력:

    Bruce
    Alfred
    Tim
    Richard

숫자 예제:

``` csharp
var nums = new List<int>{1, 2, 3, 4, 5};
nums.ForEach(Console.WriteLine);
```

------------------------------------------------------------------------

## 3. Java 예제

``` java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Bruce", "Alfred", "Tim", "Richard");

        // for-each 구문
        for (String name : names) {
            System.out.println(name);
        }

        // forEach 메서드 + 람다
        names.forEach(name -> System.out.println(name));

        // 메서드 참조
        names.forEach(System.out::println);
    }
}
```

출력:

    Bruce
    Alfred
    Tim
    Richard

숫자 예제:

``` java
List<Integer> nums = Arrays.asList(1,2,3,4,5);
nums.forEach(System.out::println);
```

------------------------------------------------------------------------

## 4. C++ 예제

``` cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> a = {1, 2, 3, 4, 5};

    // 람다 함수
    auto print = [](int n) { std::cout << n << " "; };
    std::for_each(a.begin(), a.end(), print);
    std::cout << std::endl;

    // functor 사용
    struct Sum {
        void operator()(int n) { sum += n; }
        int sum{0};
    };

    Sum s = std::for_each(a.begin(), a.end(), Sum());
    std::cout << "sum:  " << s.sum << std::endl;

    return 0;
}
```

출력:

    1 2 3 4 5 
    sum:   15

------------------------------------------------------------------------

## 5. 비교표

| 언어 | 구문/메서드                  | 특징 |
|------|------------------------------|------|
| C#   | `List<T>.ForEach(Action<T>)` | 델리게이트/람다/메서드 그룹 모두 지원 |
| Java | `for-each` 구문 / `Iterable.forEach` | 람다, 메서드 참조 지원 (Java 8+) |
| C++  | `std::for_each`              | 람다, 함수자(functor) 지원, 범위 기반 for도 가능 |


------------------------------------------------------------------------

## 6. 요약

-   **C#**: `List<T>.ForEach`는 LINQ 컬렉션에서 자주 사용.
-   **Java**: `for-each` 구문은 직관적, `forEach` 메서드는 함수형
    스타일.
-   **C++**: `std::for_each` 또는 범위 기반 `for (auto x : vec)`를 주로
    사용.

➡️ 세 언어 모두 공통적으로 **각 요소를 순회하며 특정 작업을 수행**하는
패턴을 제공하되,
표현 방식은 언어 철학(델리게이트/람다/함수자)에 따라 다릅니다.
