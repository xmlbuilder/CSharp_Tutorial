# 🔁 C# vs C++ 튜플 정렬 비교
| 항목             | C# (`Tuple`)                                      | C++ (`std::tuple`)                                 |
|------------------|---------------------------------------------------|----------------------------------------------------|
| 튜플 타입 정의     | `Tuple<int, string>`                              | `tuple<string, string, int>`                       |
| 컨테이너         | `List<Tuple<...>>`                                | `vector<tuple<...>>`                               |
| 정렬 방식         | `list.Sort((a, b) => a.Item2.CompareTo(b.Item2))` | `sort(v.begin(), v.end(), CmpName)` 또는 람다 사용 |
| 비교 기준 설정    | `Item1`, `Item2` 등 속성 접근                     | `get<index>(tuple)`으로 요소 접근                  |
| 람다 표현식 지원  | ✅ 매우 간결                                       | ✅ C++11 이상에서 가능, C++17부터 구조 분해 가능   |
| 출력 방식         | `String.Join(",", list)`                         | `cout << get<0>(v[i]) << ...`                      |
| 가독성 및 유연성  | 명명된 요소는 불가 (단순 `ItemX`)                | 구조 분해 할당으로 가독성 향상 가능                |



## 전체 소스
```csharp
List<Tuple<int,string>> list = new List<Tuple<int,string>>()
{
    new Tuple<int,string>(2, "foo"),
    new Tuple<int,string>(1, "bar"),
    new Tuple<int,string>(3, "qux"),
};

list.Sort( (a,b) => a.Item2.CompareTo(b.Item2));
string strData = String.Join(",",list);
Console.WriteLine(strData);
//(1,bar),(2,foo),(3,qux)


C++에서 Tuple sorting을 구현하는 예이다.

static bool CmpName(tuple<string, string, int> &v1, tuple<string, string, int> &v2)
{
    return get<2>(v1) < get<2>(v2);//tuple 3번째 값으로 비교
}

int main(void)
{
    std::vector<tuple <string, string, int>> v;

    v.push_back(make_tuple("workid 1", "2021-10-27 08:20:18", 3));
    v.push_back(make_tuple("workid 2", "2021-10-28 09:22:18", 5));
    v.push_back(make_tuple("workid 3", "2021-10-29 10:18:18", 7));
    v.push_back(make_tuple("workid 5", "2021-10-27 14:32:18", 1));
    v.push_back(make_tuple("workid 8", "2021-10-27 14:47:18", 2));
    v.push_back(make_tuple("workid 6", "2021-10-30 14:36:18", 6));
    v.push_back(make_tuple("workid 4", "2021-10-29 11:12:18", 4));
    v.push_back(make_tuple("workid 9", "2021-10-26 08:20:18", 8));
    v.push_back(make_tuple("workid 7", "2021-10-27 14:37:18", 5));

    sort(v.begin(), v.end(), CmpName);

    for (int i = 0; i < v.size(); i++)
        cout << get<0>(v[i]) << "\t" << get<1>(v[i]) << "\t" << get<2>(v[i]) << endl;
        return 0;
}

```

## ✅ C++에서 람다로 정렬도 가능!
지금은 CmpName() 함수로 정렬했지만,
C++11 이상이라면 람다를 써서 더 간결하게 표현할 수도 있어요:
std::sort(v.begin(), v.end(), [](const auto& a, const auto& b) {
    return std::get<2>(a) < std::get<2>(b);
});


이렇게 하면 별도의 함수 선언 없이 바로 정렬 조건을 명시할 수 있어요.
C#의 Sort((a,b) => ...)와 거의 비슷한 느낌이죠.

## 🧠 튜플 정렬의 장점
- 간단한 구조로 여러 필드를 묶어 처리 가능
- 정렬 기준을 자유롭게 설정할 수 있어 유연함
- 불필요한 클래스 정의 없이 빠르게 구현 가능

## ✨ 출력 예시 (C++)
정렬 후 출력 결과는 get<2>() 기준으로 오름차순:
workid 5    2021-10-27 14:32:18    1  
workid 8    2021-10-27 14:47:18    2  
workid 1    2021-10-27 08:20:18    3  
...




