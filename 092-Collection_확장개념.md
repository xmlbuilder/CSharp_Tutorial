# 🧠 목적: Dictionary를 Collection 기반으로 확장하기
기존의 Dictionary<TKey, TValue>는 하나의 키에 하나의 값만 저장할 수 있습니다.
하지만 이 코드는 하나의 키에 여러 값을 저장할 수 있도록 Dictionary<TKey, ICollection<TValue>>를 확장합니다.

## 🔧 핵심 구성 요소
### 1. Add 확장 메서드
```csharp
public static void Add<TKey, TValue, TCollection>(
    this Dictionary<TKey, TCollection> dict, TKey key, TValue value)
    where TCollection : ICollection<TValue>, new()
```

- 기능: 키가 존재하지 않으면 new TCollection()을 생성해서 추가하고, 그 컬렉션에 값을 넣습니다.
- 제약 조건: TCollection은 ICollection<TValue>를 구현하고, new() 생성자가 있어야 합니다.
- 예시 동작:
dictList.Add("example", 5); // "example" 키가 없으면 List<int> 생성 후 5 추가


### 2. Remove 확장 메서드
```csharp
public static bool Remove<TKey, TValue, TCollection>(
    this Dictionary<TKey, TCollection> dict, TKey key, TValue value)
    where TCollection : ICollection<TValue>
```

- 기능: 해당 키의 컬렉션에서 값을 제거하고, 컬렉션이 비면 키 자체도 제거합니다.
- 예시 동작:
dictList.Remove("example", 5); // "example" 키의 List<int>에서 5 제거



## 🧪 실행 흐름 요약
```csharp
var dictList = new Dictionary<string, List<int>>();
dictList.Add("example", 5);     // List<int> 생성 후 5 추가
dictList.Add("example", 10);    // 기존 List<int>에 10 추가
dictList.Add("example", 15);    // 기존 List<int>에 15 추가
Console.WriteLine(...);         // 출력: 5, 10, 15

dictList.Remove("example", 5);  // 5 제거 → 남은 값: 10, 15
dictList.Remove("example", 10); // 10 제거 → 남은 값: 15
Console.WriteLine(...);         // 출력: 15

dictList.Remove("example", 15); // 15 제거 → List 비어짐 → 키 "example" 제거
Console.WriteLine(...);         // 출력: False (키 없음)
```
## 전체코드

```csharp
namespace GrammarTest
{
    public static class DicListExtensions
    {
        public static void Add<TKey, TValue, TCollection>(this Dictionary<TKey, TCollection> dict, TKey key,
            TValue value) where TCollection : ICollection<TValue>, new()
        {
            TCollection list;
            if (!dict.TryGetValue(key, out list))
            {
                list = new TCollection();
                dict.Add(key, list);
            }
            list.Add(value);
        }

        public static bool Remove<TKey, TValue, TCollection>(this Dictionary<TKey, TCollection> dict,
            TKey key, TValue value) where TCollection : ICollection<TValue>
        {
            TCollection list;
            if (!dict.TryGetValue(key, out list))
            {
                return false;
            }
            var ret = list.Remove(value);
            if (list.Count == 0)
            {
                dict.Remove(key);
            }
            return ret;
        }
    }
    
    class Program
    {
        public static void Main(String[] args)
        {
            var dictList = new Dictionary<string, List<int>>();
            dictList.Add("example", 5);
            dictList.Add("example", 10);
            dictList.Add("example", 15);
            Console.WriteLine(String.Join(", ", dictList["example"]));

            dictList.Remove("example", 5);
            dictList.Remove("example", 10);
            Console.WriteLine(String.Join(", ", dictList["example"]));
            dictList.Remove("example", 15);
            
            Console.WriteLine(dictList.ContainsKey("example"));
            
        }
    }
}


```


## 📘 Dictionary<string, List<int>> 구조의 장점 요약
| 항목                  | 설명                                                                 |
|-----------------------|----------------------------------------------------------------------|
| Dictionary<string, List<int>> | ✅ 하나의 키에 여러 값 저장 가능<br>✅ 빠른 조회 및 그룹화 처리에 유리 |
| new List<int>()       | ✅ 값 초기화가 간편<br>✅ 유연한 값 추가 및 수정 가능                     |
| Add / Remove          | ✅ 값 추가 및 제거가 직관적<br>✅ 값이 모두 제거되면 키도 삭제 가능         |



## 💡 확장 아이디어
- HashSet<T>를 사용하면 중복 없는 값 저장도 가능
- Dictionary<TKey, Queue<TValue>>로 FIFO 구조 구현 가능
- TryGetValues() 같은 조회 확장 메서드도 추가 가능


---


## 🧵 1. ConcurrentDictionary 기반 확장
### 🔧 목적
- 멀티스레드 환경에서 안전하게 키-컬렉션 구조를 사용하고 싶을 때
- Dictionary는 스레드 안전하지 않기 때문에 ConcurrentDictionary로 교체
### ✅ 확장 메서드 예시
```csharp
public static class ConcurrentDicListExtensions
{
    public static void Add<TKey, TValue>(
        this ConcurrentDictionary<TKey, ConcurrentBag<TValue>> dict,
        TKey key, TValue value)
    {
        dict.AddOrUpdate(
            key,
            _ => new ConcurrentBag<TValue> { value },
            (_, bag) => { bag.Add(value); return bag; });
    }
}
```

### 💡 특징
- ConcurrentBag<T>는 스레드 안전한 컬렉션
- AddOrUpdate()는 키가 없으면 생성, 있으면 업데이트
- ConcurrentQueue<T>나 ConcurrentStack<T>로도 확장 가능

## 🧊 2. ImmutableDictionary 기반 확장
### 🔧 목적
- 불변성 유지: 상태 변경 없이 새로운 사본을 반환
- 함수형 프로그래밍 스타일에 적합
### ✅ 예시 코드
```csharp
public static class ImmutableDicListExtensions
{
    public static ImmutableDictionary<TKey, ImmutableList<TValue>> Add<TKey, TValue>(
        this ImmutableDictionary<TKey, ImmutableList<TValue>> dict,
        TKey key, TValue value)
    {
        if (dict.TryGetValue(key, out var list))
        {
            return dict.SetItem(key, list.Add(value));
        }
        else
        {
            return dict.Add(key, ImmutableList.Create(value));
        }
    }
}
```

### 💡 특징
- 기존 dict는 변경되지 않고, 새로운 사본이 반환됨
- ImmutableList<T>는 불변 리스트
- Remove()도 마찬가지로 새로운 사본을 반환해야 함

## 🔄 비교 요약
| 컬렉션 타입           | 특징                         | 사용 시점                      |
|----------------------|------------------------------|--------------------------------|
| Dictionary           | 일반 컬렉션, 스레드 안전 X   | 단일 스레드, 간단한 구조      |
| ConcurrentDictionary | 스레드 안전, 병렬 처리 가능  | 멀티스레드 환경               |
| ImmutableDictionary  | 불변성 유지, 함수형 스타일   | 상태 변경 없이 안전한 처리    |



## 🧠 마무리 팁
- ConcurrentDictionary는 성능과 안정성을 동시에 고려할 때 유용
- ImmutableDictionary는 불변성과 예측 가능한 상태 관리가 중요할 때 적합
- 확장 메서드를 통해 기존 구조를 재사용하면서도 안전하게 확장할 수 있음

---

