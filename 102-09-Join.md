# Join

=C#의 Join 메서드는 두 컬렉션을 공통 키를 기준으로 병합하는 기능을 제공합니다


## 🔗 C# Join 요약
```csharp
namespace  GrammarTest
{
    public class Program
    {
        public class TableA
        {
            public string Code { get; set; }
            public string ColumnA { get; set; }
        }
        public class TableB
        {
            public string Code { get; set; }
            public string ColumnB { get; set; }
        }
       
        public static void Main(String[] args)
        {
            List<TableA> tableA = new List<TableA>()
            {
                new TableA()
                {
                    Code = "A",
                    ColumnA = "A"
                },
                new TableA()
                {
                    Code = "B",
                    ColumnA = "B"
                },
                new TableA()
                {
                    Code = "C",
                    ColumnA = "C"
                },
                
            };
            List<TableB> tableB = new List<TableB>()
            {
                new TableB()
                {
                    Code = "A",
                    ColumnB = "A1"
                },
                new TableB()
                {
                    Code = "B",
                    ColumnB = "B1"
                },
                new TableB()
                {
                    Code = "C",
                    ColumnB = "C1"
                },
            };
           
            var join1 = tableA.Join(
                tableB,
                a => a.Code, b => b.Code,
                (a, b) => new { a.ColumnA, b.ColumnB });
            
            foreach (var item in join1)
            {
                Console.WriteLine(item);
            } 
            /*
            { ColumnA = A, ColumnB = A1 }
            { ColumnA = B, ColumnB = B1 }
            { ColumnA = C, ColumnB = C1 }
             */
        }
    }
}


namespace  GrammarTest
{
    public class Program
    {
        public class Region
        {
            public int ID;
            public string RegionDescription;
            public Region(int id, string regionDescription = null)
            {
                ID = id;
                RegionDescription = regionDescription;
            }
        }
      
        public static void Main(String[] args)
        {
            var first = new List<Region>()
            {
                new Region(1), new Region(2), new Region(3), new Region(4)
            };
            var second = new List<Region>()
            {
                new Region(1, "Eastern"), new Region(2, "Western"),
                new Region(3, "Northern"), new Region(4, "Southern")
            };

            var result =  first.Join(second, a => a.ID, b => b.ID,
                (a, b) => new {a.ID, b.RegionDescription});
            
                
            
            foreach (var region in result)
            {
                Console.WriteLine($"{region.ID}, {region.RegionDescription}");
            }
            /*
            1, Eastern
            2, Western
            3, Northern
            4, Southern
             */
        }
    }
}

```

```csharp
var join1 = tableA.Join(
    tableB,
    a => a.Code, b => b.Code,
    (a, b) => new { a.ColumnA, b.ColumnB });
```


🧠 Join은 LINQ에서 두 컬렉션을 키(Code) 기준으로 병합하고, 원하는 형태로 결과를 생성합니다.

## 🧩 C++ 버전
```cpp
#include <iostream>
#include <vector>
#include <string>

struct TableA {
    std::string Code;
    std::string ColumnA;
};

struct TableB {
    std::string Code;
    std::string ColumnB;
};

int main() {
    std::vector<TableA> tableA = {
        {"A", "A"}, {"B", "B"}, {"C", "C"}
    };
    std::vector<TableB> tableB = {
        {"A", "A1"}, {"B", "B1"}, {"C", "C1"}
    };

    for (const auto& a : tableA) {
        for (const auto& b : tableB) {
            if (a.Code == b.Code) {
                std::cout << "{ ColumnA = " << a.ColumnA
                          << ", ColumnB = " << b.ColumnB << " }" << std::endl;
            }
        }
    }

    return 0;
}
```

📝 이중 루프를 사용해 Code가 같은 항목을 병합합니다.

☕ Java 버전
```java
import java.util.*;

class TableA {
    String code;
    String columnA;
    TableA(String code, String columnA) {
        this.code = code;
        this.columnA = columnA;
    }
}

class TableB {
    String code;
    String columnB;
    TableB(String code, String columnB) {
        this.code = code;
        this.columnB = columnB;
    }
}

public class JoinExample {
    public static void main(String[] args) {
        List<TableA> tableA = Arrays.asList(
            new TableA("A", "A"),
            new TableA("B", "B"),
            new TableA("C", "C")
        );

        List<TableB> tableB = Arrays.asList(
            new TableB("A", "A1"),
            new TableB("B", "B1"),
            new TableB("C", "C1")
        );

        for (TableA a : tableA) {
            for (TableB b : tableB) {
                if (a.code.equals(b.code)) {
                    System.out.println("{ ColumnA = " + a.columnA +
                                       ", ColumnB = " + b.columnB + " }");
                }
            }
        }
    }
}
```

📝 Java에서도 이중 루프 방식으로 Join을 구현합니다.

## 🐍 Python 버전
```python
class TableA:
    def __init__(self, code, columnA):
        self.code = code
        self.columnA = columnA

class TableB:
    def __init__(self, code, columnB):
        self.code = code
        self.columnB = columnB

tableA = [TableA("A", "A"), TableA("B", "B"), TableA("C", "C")]
tableB = [TableB("A", "A1"), TableB("B", "B1"), TableB("C", "C1")]

for a in tableA:
    for b in tableB:
        if a.code == b.code:
            print(f"{{ ColumnA = {a.columnA}, ColumnB = {b.columnB} }}")
```

📝 Python에서도 동일하게 code 기준으로 병합합니다.

## ✅ 핵심 요약 (Markdown 표)
| 언어   | Join 구현 방식         | 설명                                |
|--------|------------------------|-------------------------------------|
| C#     | `Join()` (LINQ)        | 키 기준 병합, 람다로 결과 생성     |
| C++    | 이중 루프              | 조건 비교 후 병합 출력              |
| Java   | 이중 루프              | `equals()`로 키 비교 후 병합        |
| Python | 이중 루프              | `==` 비교로 키 기준 병합            |

---



