# 📋 BindingListSample

.NET의 `BindingList<T>`를 활용하여 GUI 없이도 데이터 변경 이벤트를 감지하고 처리하는 콘솔 샘플입니다.  
보통 WPF나 WinForms의 XAML과 함께 사용되지만, 이 예제는 **독립적으로도 강력한 기능을 발휘할 수 있다는 점**을 보여줍니다.

## 🎯 목적

- `BindingList<T>`의 이벤트 기반 구조를 이해
- `RaiseListChangedEvents`와 `ResetBindings()`의 역할 확인
- GUI 없이도 데이터 변경을 감지하고 처리하는 방법 학습

## 📌 주요 기능

| 기능 | 설명 |
|------|------|
| `RaiseListChangedEvents` | 이벤트 발생 여부를 제어하여 대량 추가 시 성능 최적화 가능 |
| `ResetBindings()` | 바인딩된 UI 또는 핸들러에 전체 변경 사항을 알림 |
| `ListChanged` 이벤트 | 항목 추가, 삭제, 변경 시 자동으로 발생하는 이벤트 |
| 람다 핸들러 | 이벤트를 간단하게 처리하기 위한 람다식 사용 |

## 전체 소스
```csharp
using System.ComponentModel;

class Program
{
    static int Main(string[] args)
    {
        var nameList = new BindingList<string>();

        nameList.RaiseListChangedEvents = false;
        for (long i = 0; i < 10000; i++)
        {
            nameList.Add("Alice");
        }
        nameList.RaiseListChangedEvents = true;
        nameList.ResetBindings();
        
        
        void listOfParts_ListChanged(object sender, ListChangedEventArgs e)
        {
            Console.WriteLine(e.ListChangedType.ToString());
        }
        //nameList.ListChanged += listOfParts_ListChanged;

        
        nameList.ListChanged += (s, e) =>
        {
            Console.WriteLine(e.ListChangedType.ToString() + ", " + e.NewIndex);
        };
        
        nameList.Add("Alice");
        //ItemAdded, 10000
        return 0;

    }
}

```


## 🧪 실행 예시

```bash
dotnet run
```

## 💡 활용 예시
- WPF, WinForms의 ViewModel에서 데이터 바인딩 처리
- 테스트 환경에서 데이터 변경 감지 로직 검증
- GUI 없이도 이벤트 기반 구조를 학습하고 활용

## 🛠️ 개발 환경
- .NET 6 이상
- C# 10
