# ✅ C# 이벤트 핸들링 요약
- event 키워드로 멀티캐스트 델리게이트 선언
- EventHandler와 EventArgs를 활용해 타입 안전한 이벤트 전달
- 구독자는 += 연산자로 이벤트 핸들러 등록
- 이벤트는 게시자 클래스 내부에서만 호출 가능
server.DataChangeEvent += data_changed;

## 전체 코드

```csharp
namespace GrammarTest
{
    class Program
    {
        public class MyEventArg : EventArgs
        {
            public String Message { get; set; }
        }

        public class Server
        {
            internal event EventHandler DataChangeEvent;
            void RaiseEvent(String message)
            {
                var ev = DataChangeEvent;
                if (ev != null)
                {
                    MyEventArg arg = new MyEventArg { Message = message };
                    ev(this, arg);
                }
            }
            public void DataChanged()
            {
                RaiseEvent("Data Changed");
            }
        }

        public class Client
        {
            public Client(Server server)
            {
                server.DataChangeEvent += data_changed;
            }
            private void data_changed(object sender, EventArgs args)
            {
                Console.WriteLine(sender.GetType());
                MyEventArg arg = (MyEventArg)args;
                Console.WriteLine(arg.Message);
                
            } 
        }
          
        public static void Main(String[] args)
        {
            Server server = new Server();
            Client client = new Client(server);
            server.DataChanged();
            //GrammarTest.Program+Server
            //Data Changed
        }
    }
}


```



# ✅ C++ 이벤트 핸들링 요약
- std::function으로 콜백을 저장
- 람다 또는 함수 포인터로 핸들러 등록
- 이벤트 발생 시 raiseEvent()로 콜백 실행
- GUI 프레임워크 없이도 직접 구현 가능
server->setDataChanged([&](int a){ ... });

```cpp

//C++로 비슷하게 구현해 보면 다음과 같다.

#include <iostream>
#include <functional>

class Server {
private:

    std::function<void(int)> dataChanged = nullptr;

    void raiseEvent(int data){

        if(dataChanged != nullptr){
            dataChanged(data);
        }
    }

public:
    void setDataChanged(std::function<void(int)> dataChanged)
    {
        this->dataChanged = dataChanged;
    }

    void updateChanged(int data){
        raiseEvent(data);
    }

};

class Client {
public:
    Client(Server* server)
    {
        std::cout << this << std::endl;

        //std::function<void(int)> func = data_changed;
        //server->setDataChanged(func);

        server->setDataChanged([&](int a){
            Client* client = this;
            std::cout << client->id << std::endl;
            std::cout << "message : " << a << std::endl;
        });
    }

private:
    static void data_changed(int a){
        std::cout << a << std::endl;
    }

    int id {30};
};

int main()
{
    Server server;
    Client client(&server);
    server.updateChanged(3);
    /*
     000000592711F664
     30
     message : 3
     */
}
```



##  ✅ Java에서의 유사한 이벤트 구현
Java는 Observer 패턴을 직접 구현하거나, PropertyChangeSupport 같은 내장 클래스를 활용할 수 있어요.
아래는 C# 구조에 가까운 Java 예시입니다:

```java
import java.beans.PropertyChangeListener;
import java.beans.PropertyChangeSupport;

class Server {
    private PropertyChangeSupport support = new PropertyChangeSupport(this);

    public void addDataChangeListener(PropertyChangeListener listener) {
        support.addPropertyChangeListener("data", listener);
    }

    public void dataChanged() {
        support.firePropertyChange("data", null, "Data Changed");
    }
}

class Client {
    public Client(Server server) {
        server.addDataChangeListener(evt -> {
            System.out.println(evt.getSource().getClass());
            System.out.println(evt.getNewValue());
        });
    }
}

public class Main {
    public static void main(String[] args) {
        Server server = new Server();
        Client client = new Client(server);
        server.dataChanged();
        // class Server
        // Data Changed
    }
}
```


## 🔄 이벤트 처리 구조: C# vs C++ vs Java

| 언어 | 이벤트 등록 방식 | 이벤트 발생 방식 | 특징 |
|------|------------------|------------------|------|
| **C#** | `+=` 연산자로 델리게이트 등록<br>예: `server.DataChangeEvent += handler;` | `event` 키워드로 선언된 멀티캐스트 델리게이트 호출<br>예: `ev(this, arg);` | .NET 이벤트 모델<br>타입 안전<br>구독자-게시자 구조 명확 |
| **C++** | `std::function`으로 콜백 저장<br>람다 또는 함수 포인터 사용 | 직접 호출 (`raiseEvent`)로 콜백 실행 | 자유도 높음<br>수동 구현<br>프레임워크에 따라 구조 다양 |
| **Java** | `PropertyChangeListener` 또는 사용자 정의 인터페이스 등록<br>예: `addPropertyChangeListener(listener)` | `firePropertyChange()` 또는 사용자 정의 메서드 호출 | 관찰자 패턴 기반<br>내장 이벤트 지원<br>객체 지향적 구조 |

---




