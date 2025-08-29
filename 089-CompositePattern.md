# composite Pattern
 특히 Tree<T> 클래스는 자기 참조적 제네릭 구조를 통해 트리 형태의 객체를 구성하고 있어요.

## 🌳 컴포지트 패턴이란?
컴포지트 패턴은 객체들을 트리 구조로 구성하여 부분-전체 관계를 동일하게 다룰 수 있게 하는 디자인 패턴입니다.
즉, 개별 객체와 그 집합을 동일한 방식으로 처리할 수 있게 해줘요.

## 🔍 C# 예제 분석
```csharp
public abstract class Tree<T> where T : Tree<T>
```
## 전체 소스

```csharp
namespace Grammar
{
    class Program
    {
        public abstract class Tree<T> where T : Tree<T>
        {
            protected Tree(T parent)
            {
                Parent = parent;
                Children = new List<T>();
                if (Parent != null)
                {
                    Parent.Children.Add(this as T);
                }
            }
            public T Parent { get; private set; }
            public List<T> Children { get; private set; }
            public bool IsRoot
            {
                get { return Parent == null; }
            }
            public bool IsLeaf
            {
                get
                {
                    return Children.Count == 0;
                }
            }
            public int Level
            {
                get { return IsRoot ? 0 : Parent.Level + 1; }
            }
        }

        public class MyNode : Tree<MyNode>
        {
            public MyNode(MyNode parent) : base(parent)
            {
            }
        }

        public static void Main(String[] args)
        {
            MyNode root = new MyNode(null);
            MyNode node1 = new MyNode(root);
            MyNode node2 = new MyNode(node1);
            Console.WriteLine(node2.Level);
        }
    }
    
}

```


- T는 자기 자신을 타입으로 받는 제네릭 → Curiously Recurring Template Pattern (CRTP) 스타일
- Parent와 Children을 통해 트리 구조 형성
- IsRoot, IsLeaf, Level은 트리 탐색에 필요한 속성
이 구조는 리플렉션 없이도 객체 간 계층 구조를 표현하고 탐색할 수 있게 해줘요.

## 🧩 C++ 버전
```cpp
#include <iostream>
#include <vector>
#include <memory>

template<typename T>
class Tree {
public:
    Tree(std::shared_ptr<T> parent = nullptr) : parent(parent) {
        if (parent) {
            parent->children.push_back(static_cast<T*>(this));
        }
    }

    std::shared_ptr<T> parent;
    std::vector<T*> children;

    bool isRoot() const { return parent == nullptr; }
    bool isLeaf() const { return children.empty(); }

    int level() const {
        return isRoot() ? 0 : parent->level() + 1;
    }
};

class MyNode : public Tree<MyNode> {
public:
    MyNode(std::shared_ptr<MyNode> parent = nullptr) : Tree(parent) {}
};

int main() {
    auto root = std::make_shared<MyNode>();
    auto node1 = std::make_shared<MyNode>(root);
    auto node2 = std::make_shared<MyNode>(node1);
    std::cout << node2->level() << std::endl; // 출력: 2
}
```


## 🐍 Python 버전
```python
class Tree:
    def __init__(self, parent=None):
        self.parent = parent
        self.children = []
        if parent:
            parent.children.append(self)

    @property
    def is_root(self):
        return self.parent is None

    @property
    def is_leaf(self):
        return len(self.children) == 0

    @property
    def level(self):
        return 0 if self.is_root else self.parent.level + 1

class MyNode(Tree):
    pass

root = MyNode()
node1 = MyNode(root)
node2 = MyNode(node1)
print(node2.level)  # 출력: 2
```



## 📘 언어별 특징 비교
| 언어     | 제네릭/상속 방식                         | 메모리 관리       | 특징                                      |
|----------|------------------------------------------|-------------------|-------------------------------------------|
| C#       | `Tree<T> where T : Tree<T>`              | GC 기반           | CRTP 스타일, 강력한 타입 제약              |
| C++      | `template<typename T>` + 상속            | 스마트 포인터     | 명시적 타입 캐스팅 필요 (`static_cast`)   |
| Python   | 동적 타입 + 클래스 상속                  | 자동 참조 관리    | 가장 간결, 타입 제약 없음                  |



## ✨ 이 구조의 활용 가능성
- ✅ UI 트리 구조 (메뉴, 폴더, 씬 그래프 등)
- ✅ 수식 트리 (JungHwan의 Exp 구조처럼)
- ✅ 기하학적 계층 (OpenNURBS에서 객체 그룹화)
- ✅ 게임 오브젝트 계층 (Unity의 Transform 구조와 유사)


## 🌳 Java 버전: 컴포지트 패턴 트리 구조
```java
import java.util.ArrayList;
import java.util.List;

abstract class Tree<T extends Tree<T>> {
    private T parent;
    private List<T> children = new ArrayList<>();

    public Tree(T parent) {
        this.parent = parent;
        if (parent != null) {
            parent.addChild((T) this);
        }
    }

    public T getParent() {
        return parent;
    }

    public List<T> getChildren() {
        return children;
    }

    public boolean isRoot() {
        return parent == null;
    }

    public boolean isLeaf() {
        return children.isEmpty();
    }

    public int getLevel() {
        return isRoot() ? 0 : parent.getLevel() + 1;
    }

    protected void addChild(T child) {
        children.add(child);
    }
}

class MyNode extends Tree<MyNode> {
    public MyNode(MyNode parent) {
        super(parent);
    }
}

public class Main {
    public static void main(String[] args) {
        MyNode root = new MyNode(null);
        MyNode node1 = new MyNode(root);
        MyNode node2 = new MyNode(node1);

        System.out.println("node2 level: " + node2.getLevel()); // 출력: 2
    }
}
```


## 🧠 주요 차이점 요약
| 항목             | C#                                      | Java                                     | C++                                      |
|------------------|------------------------------------------|-------------------------------------------|-------------------------------------------|
| 제네릭 제약       | `where T : Tree<T>`                     | `T extends Tree<T>`                      | 없음 (템플릿 기반)                        |
| 타입 캐스팅       | `this as T`                             | `(T) this`                               | `static_cast<T*>(this)`                   |
| 컬렉션 타입       | `List<T>`                               | `ArrayList<T>`                           | `std::vector<T*>`                         |
| 메모리 관리       | 자동 (GC)                               | 자동 (GC)                                | 수동 또는 스마트 포인터 (`shared_ptr`)   |
| 접근자 스타일     | 프로퍼티 (`get`, `set`)                | 명시적 `getX()` 메서드                  | 멤버 함수 또는 public 멤버 직접 접근      |
| 상속 구조         | `Tree<T>` 추상 클래스                  | `Tree<T>` 추상 클래스                   | `Tree<T>` 템플릿 클래스 + 상속            |
| 타입 안정성       | 강력한 제약                             | 비교적 유연                              | 가장 유연, 타입 안전성 낮음               |

-----

