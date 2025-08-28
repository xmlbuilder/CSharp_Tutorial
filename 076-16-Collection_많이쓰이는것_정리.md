# 🧩 주요 컬렉션 구조 요약
| 컬렉션 종류     | 특징                                      | C#                          | C++                          | Java                          | Python                       |
|----------------|-------------------------------------------|-----------------------------|------------------------------|-------------------------------|------------------------------|
| 배열 리스트     | 동적 크기, 인덱스 접근 가능               | `List<T>`, `ArrayList`     | `std::vector`                | `ArrayList`                   | `list`                       |
| 해시 테이블     | 키-값 구조, 빠른 검색                     | `Hashtable`, `Dictionary`  | `std::unordered_map`         | `HashMap`, `Hashtable`        | `dict`                       |
| 큐 (FIFO)       | 선입선출 구조                             | `Queue<T>`                  | `std::queue`                 | `Queue`, `LinkedList`         | `collections.deque`          |
| 스택 (LIFO)     | 후입선출 구조                             | `Stack<T>`                  | `std::stack`                 | `Stack`, `Deque`              | `list`, `collections.deque`  |
| 집계 함수       | 합계, 평균 등                             | `Sum()`, `Average()`        | `std::accumulate`, 직접 구현 | `stream().mapToInt().sum()`   | `sum()`, `statistics.mean()` |



## 🔹 배열 리스트 (Array List)
### C#
```csharp
List<int> list = new List<int> {1, 2, 3};
list.Add(4); list.Remove(2);
```

### C++
```cpp
std::vector<int> vec = {1, 2, 3};
vec.push_back(4); vec.erase(vec.begin() + 1);
```

### Java
```java
ArrayList<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
list.add(4); list.remove(Integer.valueOf(2));
```

### Python
```python
lst = [1, 2, 3]
lst.append(4); lst.remove(2)
```


## 🔹 해시 테이블 (Hashtable / Dictionary)
### C#
```csharp
Dictionary<string, string> dict = new Dictionary<string, string>();
dict["사과"] = "apple";
```
### C++
```cpp
std::unordered_map<std::string, std::string> dict;
dict["사과"] = "apple";
```

### Java
```java
Map<String, String> map = new HashMap<>();
map.put("사과", "apple");
```

### Python
```python
d = {"사과": "apple", "토마토": "tomato"}
d["감자"] = "potato"
```


## 🔹 큐 (Queue)
### C#
```csharp
Queue<string> q = new Queue<string>();
q.Enqueue("one"); q.Dequeue();
```

### C++
```cpp
std::queue<std::string> q;
q.push("one"); q.pop();
```

### Java
```java
Queue<String> q = new LinkedList<>();
q.add("one"); q.poll();
```

### Python
```python
from collections import deque
q = deque(["one", "two"])
q.popleft()
```


## 🔹 스택 (Stack)
### C#
```csharp
Stack<string> stack = new Stack<string>();
stack.Push("book"); stack.Pop();
```

### C++
```cpp
std::stack<std::string> stack;
stack.push("book"); stack.pop();
```

### Java
```java
Stack<String> stack = new Stack<>();
stack.push("book"); stack.pop();
```

### Python
```python
stack = []
stack.append("book"); stack.pop()
```


## 🔹 집계 함수 (Sum, Average 등)
### C#

```csharp
var nums = new List<int> {1, 2, 3};
Console.WriteLine(nums.Sum()); // 6
Console.WriteLine(nums.Average()); // 2.0
```

### C++
```cpp
std::vector<int> nums = {1, 2, 3};
int sum = std::accumulate(nums.begin(), nums.end(), 0);
double avg = static_cast<double>(sum) / nums.size();
```

### Java
```java
List<Integer> nums = Arrays.asList(1, 2, 3);
int sum = nums.stream().mapToInt(i -> i).sum();
double avg = nums.stream().mapToInt(i -> i).average().orElse(0);
```

### Python
```python
nums = [1, 2, 3]
print(sum(nums))  # 6
print(sum(nums) / len(nums))  # 2.0

```

## 소스
```csharp
/*
컬렉션은 데이터의 검색과 저장을 위해 특화된 것이라고 말할 수 있습니다. 
자료구조(Data Structure) 라고도 말할 수 있습니다. 
컬렉션에서는 배열 리스트(Array List), 해시 테이블(Hash table), 큐(Queue), 스택(Stack) 등을 지원합니다.

1) 배열 리스트(ArrayList)
배열 리스트란 배열과 가장 비슷하면서도 크기가 유연(동적)합니다
*/
ArrayList alist = new ArrayList();
for (int i = 0; i < 10; i++)
    alist.Add(i);
alist.Remove(5);
Console.WriteLine(String.Join(" ", alist.ToArray()));
//0 1 2 3 4 6 7 8 9
alist.Insert(5, 20);
Console.WriteLine(String.Join(" ", alist.ToArray()));
//0 1 2 3 4 20 6 7 8 9



/*
해시 테이블(Hashtable)
해시 테이블은 키와 값을 가진 요소를 다루는 사전 구조로 이루어져 있으며, 빠른 검색을 목적으로 최적화 되었습니다.
    Dictionary와 유사하다.
*/

Hashtable table = new Hashtable();
table.Add("사과", "apple");
table.Add("토마토", "tomato");
table["감자"] = "potato";
foreach (var varKey in table.Keys)
{
    Console.WriteLine($"{varKey}: {table[varKey]}");
}
/*
사과: apple
토마토: tomato
감자: potato
*/


/*
큐(Queue)
이 큐(Queue)는 선입선출(First-In, First-Out, FIFO)이라는 특징을 지니고 있습니다. 
먼저 들어온 데이터가 먼저 나간다는 의미입니다
*/


Queue queue = new Queue();
queue.Enqueue("one");
queue.Enqueue("two");
queue.Enqueue("three");
queue.Enqueue("four");

while (queue.Count > 0)
      Console.WriteLine("Dequeue: {0}, Count: {1}", 
            queue.Dequeue(), queue.Count);
//Output
//Dequeue: one, Count: 3
//Dequeue: two, Count: 2
//Dequeue: three, Count: 1
//Dequeue: four, Count: 0



/*
스택(Stack)
스택은 한쪽 방향으로만 쌓는 자료구조라고 말할수 있으며, 중간에 데이터를 삽입할 수 있는 ArrayList과는 달리, 
스택은 제일 위에서만 추가할 수 있습니다. 
Queue의 특징 중 하나인 FIFO완 다르게 후입선출(Last-In First-Out)이라고 할 수 있습니다
*/

Stack Book = new Stack();
Book.Push("거침없이 배우는 Unity 3D");
Book.Push("악성코드와 멀웨어 포렌식");
Book.Push("강행돌파! 입문자를 위한 아이폰 & 아이패드 앱 개발");
Book.Push("오라클 엑사데이터 Oracle Exadata");

while (Book.Count > 0)
      Console.WriteLine(Book.Pop());


// Collection에서 공통적으로 사용 할 수 있는 집계 함수에 대해서 추가적으로 알아 보겠습니다.


namespace GrammarTest
{
    public class GrammarTest
    {
        class Book
        {
            public string Title { get; set; }
            public int Price { get; set; }
            public int Pages { get; set; }
        }

        public static void Main(String[] args)
        {
            var numbers = new List<int> {9, 7, 5, 4, 2, 5, 4, 0, 4, 1, 0, 4};
            var average = numbers.Average();
            Console.WriteLine(average);

            var arrays = numbers.ToArray();
            Console.WriteLine(arrays.Average());

            Console.WriteLine(arrays.Sum());

            var books = new List<Book>()
            {
                new Book()
                {
                    Title = "Book1",
                    Price = 100,
                    Pages = 378
                },
                new Book()
                {
                    Title = "Book2",
                    Price = 200,
                    Pages = 408
                },
                new Book()
                {
                    Title = "Book3",
                    Price = 300,
                    Pages = 608
                },
            };

            var book_average = books.Average(x => x.Price);
            Console.WriteLine(book_average);

            var book_sum = books.Sum(x => x.Price);
            Console.WriteLine(book_sum);
        }
    }
}


// 간략하게 Sum이 어떻게 구현되어 있는지 검토해 보겠다.

public static int Sum(this IEnumerable<int> source)
{
      if (source == null)
      {
            ThrowHelper.ThrowArgumentNullException(ExceptionArgument.source);
      }

      int sum = 0;
      checked
      {
            foreach (int v in source)
            {
                  sum += v;
            }
      }
      return sum;
}

// Average도 다음과 같이 구현되어 있다.

public static double Average(this IEnumerable<int> source)
{
      if (source == null)
      {
            ThrowHelper.ThrowArgumentNullException(ExceptionArgument.source);
      }
      using (IEnumerator<int> e = source.GetEnumerator())
      {
            if (!e.MoveNext())
            {
                  ThrowHelper.ThrowNoElementsException();
            }

            long sum = e.Current;
            long count = 1;
            checked
            {
       　　        while (e.MoveNext())
                  {
                        sum += e.Current;
                        ++count;
                  }
            }
            return (double)sum / count;
      }
}

```

### ✨ 마무리 팁
- C#은 LINQ 덕분에 집계나 필터링이 매우 직관적이고 강력합니다.
- C++은 STL로 빠르고 유연하지만, 직접 처리해야 할 부분이 많아요.
- Java는 Stream API로 함수형 스타일을 지원하며, 안정적인 컬렉션 구조가 강점입니다.
- Python은 문법이 간결하고 컬렉션 조작이 매우 직관적이라 빠른 프로토타이핑에 적합하죠.

---
