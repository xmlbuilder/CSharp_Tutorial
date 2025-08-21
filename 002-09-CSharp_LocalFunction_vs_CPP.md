# C# 로컬 함수 vs C++: 무엇이 되며, 무엇은 안 되나?

## 개요

-   **C#**: 메서드 내부에 **로컬 함수**(중첩 함수)를 "이름 있는 함수"로
    선언할 수 있음. `yield`, `async/await`와도 잘 작동.
-   **C++**: **이름 있는 중첩 함수는 지원하지 않음.** 함수 안에 또 다른
    "이름 있는 함수"를 정의할 수 없음.\
    ➜ 실무에선 **람다(lambda)**, **정적 헬퍼(파일 스코프 정적 함수)**,
    **펑터(구조체의 operator())**로 대체.

------------------------------------------------------------------------

## 1) 원기둥 부피 계산 예제

### C

``` csharp
public static double GetCylinderVolume(double radius, double height)
{
    return GetVolume();

    double GetVolume()
    {
        double GetCircleArea(double r) => Math.PI * r * r;
        return GetCircleArea(radius) * height;
    }
}
```

### C++ 대체 (람다 사용)

``` cpp
#include <cmath>

double GetCylinderVolume(double radius, double height) {
    auto GetVolume = [&]() {
        auto GetCircleArea = [](double r) { return M_PI * r * r; };
        return GetCircleArea(radius) * height;
    };
    return GetVolume();
}
```

------------------------------------------------------------------------

## 2) 로컬 이터레이터 + yield

### C

``` csharp
public static IEnumerable<TSource> Where<TSource>(this IEnumerable<TSource> source,
    Func<TSource, bool> predicate)
{
    return iterator();

    IEnumerable<TSource> iterator()
    {
        foreach (var element in source)
            if (predicate(element))
                yield return element;
    }
}
```

### C++ 대체 (C++20 Ranges)

``` cpp
#include <ranges>
#include <vector>

template <class R, class Pred>
auto Where(R&& source, Pred pred) {
    return std::forward<R>(source) | std::views::filter(pred);
}
```

> 참고: 표준 C++에는 `yield`가 없으므로, 지연 시퀀스는 Ranges로 처리.\
> 진짜 `yield` 제너레이터는 외부 라이브러리(cppcoro 등) 필요.

------------------------------------------------------------------------

## 3) async/await 로컬 함수

### C

``` csharp
async Task WriteEmailsAsync()
{
    async Task<IEnumerable<string>> GetEmailsFromFileAsync(string fileName) { ... }
    async Task writeLinesToFileAsync(IEnumerable<string> lines, string fileName) { ... }
}
```

### C++ 대체 (`std::async` + 람다)

``` cpp
#include <future>
#include <fstream>
#include <regex>
#include <string>
#include <vector>

std::vector<std::string> ExtractEmails(const std::string& text) {
    std::regex email(R"((?i)[a-z0-9_.+-]+@[a-z0-9-]+\.[a-z0-9-.]+)");
    std::vector<std::string> out;
    for (std::sregex_iterator it(text.begin(), text.end(), email), end; it != end; ++it)
        out.push_back(it->str());
    return out;
}

int main() {
    auto readFile = [](const std::string& path) {
        std::ifstream ifs(path);
        std::string all((std::istreambuf_iterator<char>(ifs)), {});
        return all;
    };

    auto fut1 = std::async(std::launch::async, [&]{ return ExtractEmails(readFile("input1.txt")); });
    auto fut2 = std::async(std::launch::async, [&]{ return ExtractEmails(readFile("input2.txt")); });

    auto emails1 = fut1.get();
    auto emails2 = fut2.get();
    emails1.insert(emails1.end(), emails2.begin(), emails2.end());

    std::ofstream ofs("output.txt");
    for (auto& e : emails1) ofs << e << "\n";
}
```

------------------------------------------------------------------------

## 결론

-   **C#**: 로컬 함수, 로컬 async, 로컬 iterator(yield)까지 지원.
-   **C++**: 이름 있는 로컬 함수는 없음 → **람다로 대체**.
-   지연 시퀀스 → **C++20 ranges::filter** 권장.\
-   async → **std::async + 람다** 또는 Asio/코루틴 라이브러리 필요.

👉 따라서, C#의 내부 함수는 C++에서 직접 구현 불가. **람다를 이용해
대체**해야 한다.
