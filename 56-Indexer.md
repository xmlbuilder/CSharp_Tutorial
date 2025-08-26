# 인덱서
인덱서에서는 클래스나 구조체의 인스턴스를 배열처럼 인덱싱할 수 있습니다. 
인덱싱 값은 형식이나 인스턴스 멤버를 명시적으로 지정하지 않고도 설정하거나 검색할 수 있습니다. 
인덱서는 해당 접근자가 매개 변수를 사용한다는 점을 제외하면 속성과 유사합니다.


## C# 샘플

다음 예제에서는 간단한 get 및 set 접근자 메서드를 사용해서 값을 할당하거나 검색하는 제네릭 클래스를 정의합니다. 
Program 클래스는 이 클래스의 인스턴스를 만들어 문자열을 저장합니다.

```csharp
class SampleCollection<T>
{
    // Declare an array to store the data elements.
    private T[] arr = new T[100];
    // Define the indexer to allow client code to use [] notation.
    public T this[int i]
    {
        get { return arr[i]; }
        set { arr[i] = value; }
    }
}

class Program
{
    static void Main()
    {
        var stringCollection = new SampleCollection<string>();
        stringCollection[0] = "Hello, World";
        Console.WriteLine(stringCollection[0]);
        //Hello, World
    }
}
//다음 예제에서 처럼 배열이 아니더라고 set, get으로 기능을 구현해 주면 됩니다.
namespace GrammarTest
{
    public class IndexableClass
    {
        public int this[int index]
        {
            set
            {
                Console.WriteLine("{0} was assigned to index {1}", value, index);
            }
            get
            {
                return index;
            }
        }
    }
    class Program
    {
        public static void Main(string[] args)
        {
            var foo = new IndexableClass()
            {
                [0] = 10,
                [1] = 20
            };
            //10 was assigned to index 0
            //20 was assigned to index 1
        }
    }
}

```
# C++ 샘플

- 배열요소에 접근할 때 사용하는 [ ] 연산자 오버로딩
- C, C++의 기본 배열은 배열의 경계 검사를 하지 않는  단점이 있습니다.
- 다음과 같이 잘못된 인덱스를 참조 하는 것을 막을 수 없다.

```cpp

#include <iostream>
using namespace std;

int main(void){
    int arr[3] ={1, 2, 3};
    cout<<arr[-1]<<endl;
    //-858993460
    cout<<arr[-2]<<endl;
    //-858993460
    cout<<arr[3]<<endl;
    //-858993460
    cout<<arr[4]<<endl;
    //-858993460
}

```

- 아래와 같이 함수형태를 파악할 수 있다.(반환형 int는 임의로 결정)
```cpp
  int operator[ ] (int idx) { .... }
```

```cpp
#include <iostream>

using namespace std;
class BoundCheckIntArray{
private:
    int * arr;
    int arrlen;

public:
    BoundCheckIntArray(int len) : arrlen(len)
    {
        arr = new int[len];
    }

    int& operator[] (int idx)
    {
        if(idx<0 || idx>=arrlen)
        {
            cout<<"Array index out of bound exception"<<endl;
            exit(1);
        }
        return arr[idx];
    }

    ~BoundCheckIntArray()  {
        delete [] arr;
    }
};

int main(void)
{
    BoundCheckIntArray arr(5);

    for(int i=0; i<5; i++)
    {
        arr[i] = (i+1) * 11;
    }

    for(int i=0; i<6; i++)
    {
        cout << arr[i] << endl;
    }

    /*
     11
     22
     33
     44
     55
     Array index out of bound exception
     */
    return 0;
}

```



## 언어별 인덱서(Indexer) 비교 요약

| 언어       | 인덱서 지원 | 문법 예시                     | 설명 |
|------------|-------------|-------------------------------|------|
| **C#**     | ✅ 있음      | `this[int index]`             | 클래스나 구조체에서 배열처럼 인덱싱 가능. 속성과 유사한 문법으로 `get`/`set` 접근자 사용. 경계 검사나 로직 삽입 가능. |
| **C++**    | ✅ 있음      | `operator[]`                  | 연산자 오버로딩으로 인덱싱 구현. 배열처럼 사용 가능하며, 경계 검사 등 사용자 정의 로직 삽입 가능. 포인터 기반 배열보다 안전하게 만들 수 있음. |
| **Java**   | ❌ 없음      | `get(index), set(index)`      | 컬렉션 클래스에서만 인덱싱 가능. 배열 외에는 `[]` 문법 사용 불가. 인덱서 개념 부재로 문법적 불편함 존재. |
| **Python** | ✅ 있음      | `__getitem__`, `__setitem__` | 클래스에 특별 메서드를 정의해 인덱싱 가능. 리스트, 딕셔너리 등 기본 자료형 외에도 커스텀 클래스에 자유롭게 적용 가능. |



## 🧠 Python 인덱서 예시
Python은 __getitem__과 __setitem__을 오버라이드해서 C#처럼 인덱싱을 구현할 수 있음:
class MyCollection:
    def __init__(self):
        self.data = {}

    def __getitem__(self, key):
        return self.data.get(key, None)

    def __setitem__(self, key, value):
        self.data[key] = value

### 사용 예
```python
col = MyCollection()
col[0] = "Hello"
print(col[0])  # Hello
```

Python은 문법적으로 인덱싱을 매우 자유롭게 지원해서, 사실상 C#의 인덱서보다 더 유연한 편. 
다만 타입 안정성이나 명시성 면에서는 C#이 더 엄격.
