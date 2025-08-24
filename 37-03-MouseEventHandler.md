# 🖱️ MouseEventSimulator

GUI 없이 콘솔 환경에서 마우스 이벤트를 가상으로 발생시키는 C# 샘플입니다.  
`EventHandlerList`를 활용하여 `MouseDown`, `MouseUp` 이벤트를 등록하고, 직접 호출하여 테스트할 수 있도록 구성되어 있습니다.

## 📌 주요 기능

- `MouseEventArgs`: 마우스 좌표 정보를 담는 사용자 정의 클래스
- `MouseEventHandler`: 마우스 이벤트를 처리하는 델리게이트
- `SampleControl`: 이벤트 등록 및 호출을 담당하는 샘플 컨트롤 클래스
- `OnMouseDown()`: 마우스 다운 이벤트를 가상으로 발생시킴

## 전체 소스
```csharp
/*
 * Sample로 MouseEvent Handler를 구현해 본다.
 */

using System.ComponentModel;

namespace GrammarTest
{
    public class Program
    {
        public class MouseEventArgs : EventArgs
        {
            public int X { get; set; } = 0;
            public int Y { get; set; } = 0;
        }
        
        public delegate void MouseEventHandler(object sender, MouseEventArgs e);
        
        class SampleControl
        {
            protected EventHandlerList _eventHandlerList = new EventHandlerList();
            
            private static readonly object mouseDownEventKey = new object();
            private static readonly object mouseUpEventKey = new object();
            
            public event MouseEventHandler MouseDown
            {
                add
                {
                    _eventHandlerList.AddHandler(mouseDownEventKey, value);
                }
                remove
                {
                    _eventHandlerList.RemoveHandler(mouseDownEventKey, value);
                }
            }
            
            private void OnMouseDown(MouseEventArgs e)
            {
                MouseEventHandler mouseEventHandler = (MouseEventHandler) _eventHandlerList[mouseDownEventKey];
                mouseEventHandler(this, e);
                
            }
            
            public event MouseEventHandler MouseUp
            {
                add
                {
                    _eventHandlerList.AddHandler(mouseUpEventKey, value);
                }
                remove
                {
                    _eventHandlerList.RemoveHandler(mouseUpEventKey, value);
                }
            }

            private void OnMouseUp(MouseEventArgs e)
            {
                MouseEventHandler mouseEventHandler = (MouseEventHandler) _eventHandlerList[mouseUpEventKey];
                mouseEventHandler(this, e);
            }

            public void OnMouseDown()
            {
                MouseEventArgs e = new MouseEventArgs()
                {
                    X = 10,
                    Y = 10
                };
                
                OnMouseDown(e);
            }
        }
        
        public static void Main(String[] args)
        {
            SampleControl sampleControl = new SampleControl();
            sampleControl.MouseDown += MouseDown;
            sampleControl.MouseUp += MouseUp;
            sampleControl.OnMouseDown();
        }

        public static void MouseDown(object sender, MouseEventArgs e)
        {
            Console.WriteLine($"{e.X} - {e.Y}");
        }

        public static void MouseUp(object sender, MouseEventArgs e)
        {
            Console.WriteLine($"{e.X} - {e.Y}");
        }
    }
}
```

## 🧪 실행 예시

```bash
dotnet run
```

## 출력 결과:
10 - 10


## 💡 활용 예시
- 테스트 자동화 환경에서 GUI 없이 이벤트 시뮬레이션
- 이벤트 핸들링 구조 학습
- ViewModel 또는 로직 단위 테스트

## 🛠️ 개발 환경
- .NET 6 이상
- C# 10
