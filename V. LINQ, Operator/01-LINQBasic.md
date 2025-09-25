# LINQ Basic
- 데이터를 SQL처럼 다룰 수 있는 문법
- 컬렉션을 필터링, 정렬, 그룹화, 집계하는 도구
- 함수형 프로그래밍 스타일로 간결하고 선언적
- Where, Select, GroupBy, OrderBy, Aggregate 등 풍부한 메서드 제공


## 📌 LINQ 기능별 비교 요약

| 기능             | C# (LINQ)                          | Python                             | Java (Stream API)                          | C++ (STL / Ranges)                          |
|------------------|------------------------------------|-------------------------------------|--------------------------------------------|---------------------------------------------|
| 필터링           | `Where(...)`                       | `filter(...)`, list comprehension   | `stream().filter(...)`                     | `std::ranges::views::filter`, `std::copy_if` |
| 정렬             | `OrderBy(...)`, `ThenBy(...)`      | `sorted(..., key=...)`              | `stream().sorted(...)`                     | `std::sort`, `std::ranges::sort`            |
| 최대/최소        | `Max(...)`, `Min(...)`             | `max(...)`, `min(...)`              | `stream().max(...)`, `min(...)`            | `std::max_element`, `std::min_element`      |
| 평균             | `Average(...)`                     | `sum(...) / len(...)`               | `Collectors.averagingInt(...)`             | 직접 합산 후 나누기                         |
| 첫 번째 요소     | `First(...)`                       | `[0]`, `next(iter(...))`            | `findFirst()`                              | `*std::find_if(...)`                        |
| 그룹화           | `GroupBy(...)`                     | `defaultdict(list)`, `groupby()`    | `Collectors.groupingBy(...)`               | `std::unordered_map`, `ranges::group_by`    |
| 포함 여부        | `Contains(...)`                    | `in`                                | `contains(...)`, `anyMatch(...)`           | `std::find(...) != end`                     |
| 중복 제거        | `Distinct()`                       | `set(...)`, `dict.fromkeys(...)`    | `distinct()`                               | `std::set`, `std::unique()`                 |
| 선택/투영        | `Select(...)`                      | `map(...)`, list comprehension      | `map(...)`                                 | `std::transform(...)`, `views::transform`   |


## C# 
```csharp
namespace  GrammarTest
{
    public class Program
    {
        public class Novel
        {
            public string Title { get; set; }
            public string Author { get; set; }
            public int Published { get; set; }
            public override string ToString()
            {
                return $"[Title = {Title}, Author={Author}, Published={Published}]";
            }
        }
        
        public static void Main(String[] args)
        {
            Novel[] novels = new[]
            {
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
                new Novel()
                {
                    Author = "제임스 P. 호건",
                    Title = "별의 계승자",
                    Published = 1977,
                },
            };
            var settings = new XmlWriterSettings()
            {
                Encoding = new System.Text.UTF8Encoding(false),
                Indent = true,
                IndentChars = " "
            };
            
            using (var writer = XmlWriter.Create("/Users/jeongjunghwan/Downloads/SampleTest.xml"))
            {
                var serializer = new DataContractSerializer(novels.GetType());
                serializer.WriteObject(writer, novels);
            }
            using (var reader = XmlReader.Create("/Users/jeongjunghwan/Downloads/SampleTest.xml"))
            {
                var serializer = new DataContractSerializer(typeof(Novel[]));
                var novel1 = serializer.ReadObject(reader) as Novel[];
                //Console.WriteLine(novel1);
                foreach (var novel in novel1)
                {
                    Console.WriteLine(novel);   
                }
            }
        }
    }
}


```


## 🐍 Python 예시
```python
from statistics import mean

books = Library.Books  # 가정: Book 객체 리스트

# 최대 가격
max_price = max(b.price for b in books if b.CategoryId == 1)

# 최소 제목 길이
min_title_len = min(len(b.Title) for b in books)

# 첫 번째 조건 만족 책
book1 = next(b for b in books if len(b.Title) == min_title_len)

# 평균 가격 이상
avg_price = mean(b.Price for b in books)
books1 = [b for b in books if b.Price > avg_price]

# 발행연도 정렬
years = sorted(set(b.PublishedYear for b in books))

# 복합 정렬
books2 = sorted(books, key=lambda b: (b.CategoryId, -b.PublishedYear))

# 특정 연도 포함
target_years = {2013, 2016}
books3 = [b for b in books if b.PublishedYear in target_years]

# 그룹화
from collections import defaultdict
groups = defaultdict(list)
for b in books:
    groups[b.PublishedYear].append(b)

```


## ☕ Java 예시 (Stream API)
```java
import static java.util.stream.Collectors.*;

List<Book> books = Library.getBooks();

// 최대 가격
int maxPrice = books.stream()
    .filter(b -> b.getCategoryId() == 1)
    .mapToInt(Book::getPrice)
    .max().orElse(0);

// 최소 제목 길이
int minTitleLen = books.stream()
    .mapToInt(b -> b.getTitle().length())
    .min().orElse(0);

// 첫 번째 조건 만족 책
Book book1 = books.stream()
    .filter(b -> b.getTitle().length() == minTitleLen)
    .findFirst().orElse(null);

// 평균 이상 가격
double avg = books.stream().mapToInt(Book::getPrice).average().orElse(0);
List<Book> books1 = books.stream()
    .filter(b -> b.getPrice() > avg)
    .collect(toList());

// 발행연도 정렬
List<Integer> years = books.stream()
    .map(Book::getPublishedYear)
    .distinct()
    .sorted()
    .collect(toList());

// 복합 정렬
List<Book> books2 = books.stream()
    .sorted(comparing(Book::getCategoryId)
    .thenComparing(Book::getPublishedYear, reverseOrder()))
    .collect(toList());

// 특정 연도 포함
Set<Integer> targetYears = Set.of(2013, 2016);
List<Book> books3 = books.stream()
    .filter(b -> targetYears.contains(b.getPublishedYear()))
    .collect(toList());

// 그룹화
Map<Integer, List<Book>> groups = books.stream()
    .collect(groupingBy(Book::getPublishedYear));

```

## 💻 C++ 예시 (C++20 ranges 또는 STL)
```cpp
#include <ranges>
#include <algorithm>
#include <map>
#include <vector>
#include <numeric>

std::vector<Book> books = Library::Books;

// 최대 가격
auto max_price = std::ranges::max(books | std::views::filter([](auto& b){ return b.CategoryId == 1; }),
                                  {}, &Book::Price);

// 최소 제목 길이
auto min_title_len = std::ranges::min(books, {}, [](auto& b){ return b.Title.size(); }).Title.size();

// 첫 번째 조건 만족 책
auto it = std::find_if(books.begin(), books.end(), [&](auto& b){ return b.Title.size() == min_title_len; });
Book book1 = (it != books.end()) ? *it : Book{};

// 평균 이상 가격
double avg = std::accumulate(books.begin(), books.end(), 0.0,
                             [](double sum, const Book& b){ return sum + b.Price; }) / books.size();
std::vector<Book> books1;
std::copy_if(books.begin(), books.end(), std::back_inserter(books1),
             [&](const Book& b){ return b.Price > avg; });

// 발행연도 정렬
std::set<int> years;
for (auto& b : books) years.insert(b.PublishedYear);

// 복합 정렬
std::sort(books.begin(), books.end(), [](const Book& a, const Book& b){
    return std::tie(a.CategoryId, -a.PublishedYear) < std::tie(b.CategoryId, -b.PublishedYear);
});

// 특정 연도 포함
std::vector<int> targetYears = {2013, 2016};
std::vector<Book> books2;
std::copy_if(books.begin(), books.end(), std::back_inserter(books2),
             [&](const Book& b){ return std::ranges::contains(targetYears, b.PublishedYear); });

// 그룹화
std::map<int, std::vector<Book>> groups;
for (auto& b : books) groups[b.PublishedYear].push_back(b);

```



