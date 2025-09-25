# C# `Predicate<T>` vs C++ `std::function` 비교

## 1. C# `Predicate<T>`란?

-   **정의**\
    `Predicate<T>`는 **반드시 `bool` 값을 반환**하는 델리게이트
    타입입니다.\
    즉, 입력으로 제네릭 타입 `T`를 받고, 결과로 `bool`을 반환하는 함수만
    참조할 수 있습니다.

``` csharp
public delegate bool Predicate<in T>(T obj);
```

-   **특징**
    -   반환형은 항상 `bool`이므로, 명시할 필요가 없습니다.
    -   매개변수 타입만 지정하면 됩니다.
    -   보통 `Array.Find`, `List<T>.Find`, `Exists`, `TrueForAll` 등
        **조건 판별이 필요한 메서드**와 함께 사용됩니다.

------------------------------------------------------------------------

## 2. C# 사용 예제

``` csharp
using System;
using System.Drawing;

class Program
{
    static void Main(string[] args)
    {
        Point[] points =
        {
            new Point(100, 200),
            new Point(150, 250),
            new Point(250, 375),
            new Point(275, 395),
            new Point(295, 450)
        };

        Predicate<Point> predicate = FindPoints;

        Point? first = Array.Find(points, predicate);

        Console.WriteLine("Found X = {0}, Y = {1}", first?.X, first?.Y);
        // 출력: Found X = 275, Y = 395
    }

    private static bool FindPoints(Point pt)
    {
        return pt.X * pt.Y > 100000;
    }
}
```

-   `Array.Find`는 배열에서 `Predicate<T>` 조건을 만족하는 첫 번째
    원소를 반환합니다.
-   조건식: `pt.X * pt.Y > 100000` → 곱이 100000을 초과하는 첫 번째 점을
    찾음.

------------------------------------------------------------------------

## 3. C++의 대응 구현

C++에는 `Predicate<T>`와 같은 **고정 델리게이트 타입**은 없습니다.\
대신, **`std::function<bool(T)>`** 또는 **람다 함수**와 함께
`<algorithm>`의 `std::find_if`를 사용합니다.

``` cpp
#include <iostream>
#include <list>
#include <functional>
#include <algorithm>

struct Point {
    int X, Y;
    Point(int x, int y) : X(x), Y(y) {}
};

int main() {
    std::list<Point> lists = {
        Point(100, 200),
        Point(150, 250),
        Point(250, 375),
        Point(275, 395),
        Point(295, 450)
    };

    std::function<bool(Point)> findPoint = [&](Point pt){
        return pt.X * pt.Y > 100000;
    };

    auto itrFind = std::find_if(lists.begin(), lists.end(), findPoint);

    if(itrFind != lists.end()){
        std::cout << itrFind->X << ", " << itrFind->Y << std::endl;
        // 출력: 275, 395
    }
}
```

-   `std::function<bool(Point)>`는 `Predicate<Point>`와 동일하게 **Point
    → bool** 시그니처를 가진 함수 객체를 정의합니다.
-   `std::find_if`는 컨테이너에서 조건을 만족하는 첫 번째 원소를
    반환합니다.

------------------------------------------------------------------------

## 4. 비교 정리

  -------------------------------------------------------------------------------------
  항목     C# `Predicate<T>`                C++ `std::function` + `std::find_if`
  -------- -------------------------------- -------------------------------------------
  정의     `bool Predicate<T>(T obj)`       `std::function<bool(T)>`
           델리게이트                       

  반환     **항상 `bool` (고정)**           제네릭, 보통 `bool`
  타입                                      

  사용     `Array.Find`, `List<T>.Find`,    `<algorithm>`의 `std::find_if`
  메서드   `Exists` 등                      

  작성     `Predicate<Point> p = Method;`   `std::function<bool(Point)> p = lambda;`
  방식                                      

  문법     반환형 생략 가능 →               반환형 명시 필요
  단순화   `Predicate<Point>`               (`std::function<bool(Point)>`)

  성능     컴파일러 최적화된 델리게이트     `std::function`은 가상 호출 오버헤드 가능
                                            (람다 캡처시 inline 최적화됨)
  -------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 5. 핵심 차이

-   **C#**
    -   `Predicate<T>`는 **불리언 판정 전용**으로 특화된 델리게이트.
    -   API에 자주 등장 (`Array.Find`, `Exists` 등).
    -   반환형을 명시하지 않아도 됨 (항상 `bool`).
-   **C++**
    -   `std::function`은 **범용 함수 래퍼** → 반환형을 직접 지정해야
        함.
    -   `std::find_if` 등 `<algorithm>`과 결합해 조건 기반 검색을 구현.
    -   람다식을 사용할 때 inline 최적화가 적용되어 `Predicate<T>`처럼
        효율적으로 동작 가능.

------------------------------------------------------------------------

✅ **정리**:\
C#의 `Predicate<T>`는 **단항 불리언 판정 함수**만을 위해 만들어진 특화
델리게이트이고,\
C++에서는 `std::function<bool(T)>`와 `std::find_if` 조합이 사실상 같은
역할을 합니다.\
즉, 두 언어 모두 *"조건을 만족하는 원소를 찾는 전용 인터페이스"*를
제공하지만,\
C#은 **전문화된 델리게이트**를 제공하는 반면 C++은 **범용 함수 객체**를
활용하는 차이가 있습니다.

---
