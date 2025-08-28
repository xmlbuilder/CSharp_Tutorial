# 🧠 C#의 읽기 전용 컬렉션
- IReadOnlyList<T>는 리스트를 외부에서 수정하지 못하게 하면서도 foreach, LINQ, 인덱싱 등을 허용합니다.
- 내부에서는 List<T>로 데이터를 관리하고, 외부에는 IReadOnlyList<T>로 노출하여 캡슐화를 강화합니다.

```csharp
public IReadOnlyList<int> MyList { get; private set; }
```

## 소스 
```csharp
namespace  GrammarTest
{
    public class MySample{
    
        public IReadOnlyList<int> MyList { get; private set; }

        public MySample()
        {
            MyList = new List<int>()
            {
                1, 2, 3, 4, 5
            };
        }
    }

    public class GrammarTest
    {
        public static void Main(String[] args)
        {
            var obj = new MySample();

            //obj.MyList.Add(6);
            //obj.MyList.RemoveAt(0);

            foreach (var n in obj.MyList)
            {
                Console.WriteLine(n);
            }

            //obj.MyList = new List<int>();
        }
    }
}


class MySample{

    private List<int> _myList;
    
    public IReadOnlyList<int> MyList
    {
        get { return _myList; }
    }

    public MySample()
    {
        _myList = new List<int>()
        {
            1, 2, 3, 4, 5
        };
    }

    public void AddItem(int a)
    {
        _myList.Add(a);
        
    }
    public int getItem(int index)
    {
        return _myList[index];
    }
}

MySample mySample = new MySample();
mySample.AddItem(34);
mySample.AddItem(34);
mySample.AddItem(34);

var lists = mySample.MyList;
foreach (var item in lists)
{
    Console.WriteLine(item);   
}


```


## 🧊 C++에서의 유사 기능
- C++에서는 const 키워드를 활용해 읽기 전용 접근을 구현합니다.
- const std::vector<int>&를 반환하면 외부에서 수정할 수 없습니다.
```cpp
class MySample {
private:
    std::vector<int> myList;

public:
    MySample() : myList{1, 2, 3, 4, 5} {}

    const std::vector<int>& getList() const {
        return myList;
    }
};
```

- const 참조를 통해 읽기만 가능하며, push_back 등 수정 메서드는 호출 불가.

## 🐍 Python에서의 유사 기능
- Python은 기본적으로 모든 것이 동적이지만, tuple을 사용하면 불변 컬렉션을 만들 수 있습니다.
- 또는 property를 통해 읽기 전용 속성을 구현할 수 있습니다.
```python
class MySample:
    def __init__(self):
        self._my_list = [1, 2, 3, 4, 5]

    @property
    def my_list(self):
        return tuple(self._my_list)  # 읽기 전용으로 노출

    def add_item(self, item):
        self._my_list.append(item)
```

- my_list는 tuple로 반환되므로 외부에서 수정 불가.

## ☕ Java에서의 유사 기능
- Java에서는 Collections.unmodifiableList()를 사용해 읽기 전용 리스트를 반환합니다.
```java
import java.util.*;

public class MySample {
    private List<Integer> myList;

    public MySample() {
        myList = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
    }

    public List<Integer> getMyList() {
        return Collections.unmodifiableList(myList);
    }

    public void addItem(int item) {
        myList.add(item);
    }
}
```

- getMyList()로 받은 리스트는 add, remove 등 수정 시 UnsupportedOperationException 발생.

## 🔍 요약 비교
| 언어   | 읽기 전용 방식                          | 외부 수정 가능 여부 | 특징                                 |
|--------|----------------------------------------|---------------------|--------------------------------------|
| C#     | `IReadOnlyList<T>`                     | ❌ 불가능            | LINQ, 인덱싱 가능, 캡슐화에 유리       |
| C++    | `const std::vector<int>&`              | ❌ 불가능            | 컴파일 타임에 수정 방지               |
| Python | `property` + `tuple`                   | ❌ 불가능            | 불변 구조 사용, 동적 언어에 적합       |
| Java   | `Collections.unmodifiableList()`       | ❌ 불가능            | 런타임 예외로 수정 방지               |



이런 방식들은 모두 **캡슐화(encapsulation)**와 **불변성(immutability)**을 지키기 위한 전략이에요.  
객체의 내부 상태를 보호하면서도 외부에 필요한 정보는 안전하게 제공함.
