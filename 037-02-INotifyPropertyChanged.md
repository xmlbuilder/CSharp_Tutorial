# 🧭 INotifyPropertyChanged 도입 근거

## ✅ 목적
- 속성 값이 변경되었을 때 알림을 발생시킴
- 데이터 바인딩을 가능하게 함 (특히 WPF, MAUI, Xamarin 등에서)
- 상태 변화에 따른 동작을 자동화할 수 있음

## ❓ 왜 필요한가?

| 목적 | 설명 | 효과 |
|------|------|------|
| 상태 변화 감지 | 속성 값이 변경될 때 외부에서 이를 감지할 수 있도록 이벤트 발생 | 로직 간 동기화 및 반응형 설계 가능 |
| UI 자동 갱신 | GUI 프레임워크에서 속성 변경 시 UI가 자동으로 업데이트됨 | 사용자 경험 향상 및 코드 간결화 |
| 느슨한 결합 구조 | View와 ViewModel 간의 의존도를 낮춤 | 유지보수성과 테스트 용이성 향상 |
| 테스트 가능성 | GUI 없이도 속성 변경에 따른 동작을 검증 가능 | 단위 테스트 및 로직 검증에 유리 |


INotifyPropertyChanged는 이 문제를 해결하면서 **느슨한 결합(loose coupling)**을 가능하게 해줍니다.

## 🖥️ GUI와 연결될 때의 장점
- 자동 UI 업데이트: 속성이 바뀌면 UI가 자동으로 반응
- MVVM 패턴의 핵심 구성 요소: ViewModel에서 상태를 관리하고 View는 바인딩만 함
- 테스트 용이성: UI 없이 ViewModel만 테스트 가능
예: WPF에서 TextBox.Text가 Offset에 바인딩되어 있다면,
Offset = 10만 해도 UI가 자동으로 갱신됩니다.

## 🧪 GUI 없이도 유용한 이유
INotifyPropertyChanged는 UI 없이도 상태 변화 감지와 반응형 설계에 매우 유용해요.
### 💡 활용 예시

- 로직 간의 상태 동기화: 여러 컴포넌트가 ViewModel의 상태를 구독하고 반응
- 테스트 자동화: 속성 변경에 따른 동작을 테스트 코드에서 검증 가능
- 비동기 작업 상태 추적: 예: IsLoading, Progress, ErrorMessage 등
```csharp
viewModel.PropertyChanged += (s, e) => {
    if (e.PropertyName == "IsLoading") {
        Console.WriteLine("로딩 상태 변경됨");
    }
};
```

이처럼 GUI 없이도 상태 중심의 설계를 가능하게 해주며,
관찰자 패턴의 구현체로서 독립적인 로직 설계에 매우 적합합니다.

## 전체 소스

```csharp
using System.ComponentModel;

namespace GrammarTest
{
    public class Program
    {
        public class ViewModel : INotifyPropertyChanged
        {
            private int offset;
            public int Offset
            {
                get
                {
                    return offset;
                }
                set
                {
                    if (offset == value) return;
                    offset = value;
                    OnPropertyChanged("Offset");
                }
            }
            
            protected virtual void OnPropertyChanged(string propertyName = null)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }

            public event PropertyChangedEventHandler? PropertyChanged;
        }

        public class SomeListener
        {
            public SomeListener(ViewModel viewModel)
            {
                _viewModel = viewModel;
                _viewModel.PropertyChanged += OnViewModelPropChanged;
            }

            private void OnViewModelPropChanged(object? sender, PropertyChangedEventArgs e)
            {
                Console.WriteLine($"{sender?.GetType()}");
                Console.WriteLine($"{e.PropertyName}");
            }

            public void MakeEvent()
            {
                _viewModel.Offset = 10;
            }
            private readonly ViewModel _viewModel;
        }
        
        public static void Main(String[] args)
        {
            ViewModel vm = new ViewModel();
            SomeListener someListener = new SomeListener(vm);
            for (int i = 0; i < 2; i++)
            {
                vm.Offset = i;
            }
        }
    }
}
```


## 요약
| 항목 | 설명 |
|------|------|
| 상태 변화 감지 | 속성 값이 변경될 때 외부에서 이를 감지할 수 있도록 이벤트 발생 |
| UI 자동 갱신 | GUI 프레임워크(WPF, MAUI 등)에서 데이터 바인딩을 통해 UI가 자동으로 업데이트됨 |
| 느슨한 결합 구조 | View와 ViewModel 간의 의존도를 낮춰 유지보수성과 테스트 용이성 향상 |
| 테스트 가능성 | GUI 없이도 ViewModel 단위로 상태 변화와 반응을 테스트할 수 있음 |
| 반응형 설계 | 속성 변경에 따라 다양한 컴포넌트가 동적으로 반응할 수 있는 구조 구현 가능 |
| 관찰자 패턴 구현 | 객체 간의 상태 변화 통지를 위한 대표적인 디자인 패턴 적용 사례 |


## 🌱 INotifyPropertyChanged는 단순한 인터페이스가 아니라,
UI와 로직을 분리하고, 상태 중심의 사고를 가능하게 하는 철학적 도구입니다.


