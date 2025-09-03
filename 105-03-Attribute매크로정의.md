# 매크로 클래스 정의

MacroAttribute와 ArgDataAttribute를 활용해 매크로 클래스를 정의하고 메서드에 인자 정보를 전달하는 구조.

## 🧠 매크로 시스템 활용 예시 정리

| 항목                  | 관련 Attribute 또는 코드           | 예시 또는 설명                                      |
|-----------------------|------------------------------------|-----------------------------------------------------|
| 매크로 클래스 식별     | `MacroAttribute`                   | `[Macro("Code1")]`, `[Macro("Code2")]`              |
| 메서드 인자 전달       | `ArgDataAttribute`                 | `[ArgData(1, 2, 3, 4, 5, 6)]`, `[ArgData(1, 3)]`     |
| 매크로 이름 접근       | `MacroAttribute.Name`              | `macroAttr.Name`                                    |
| 조건 기반 실행         |                                    | `if (macroAttr.Name == "Code1")`                    |
| 실행 로그 출력         |                                    | `Console.WriteLine($"실행: {macroAttr.Name}, 인자 수: {args.Count}")` |
| DLL 로드 및 탐색       |                                    | `Assembly.LoadFile()`                               |

## 소스
```csharp
using System;
using System.Collections.Generic;
using BaseLib;

namespace MacroUser1
{
    [Macro("Code1")]
    public class MacroCode1
    {
        public void TestCode1()
        {
            Console.WriteLine("Code1 - TestCode1");
        }
        public void TestCode2()
        {
            Console.WriteLine("Code1 - TestCode2");
        }
        [ArgData(1, 2, 3, 4, 5, 6)]
        public void TestWithArgs(List<object> args)
        {
            Console.WriteLine(args.Count);
        }
    }
}

using System;
using System.Collections.Generic;
using BaseLib;
namespace MacroUser1
{
    [Macro("Code2")]
    public class MacroCode2
    {
        public void TestCode1()
        {
            Console.WriteLine("Code2 - TestCode1");
        }
        public void TestCode2()
        {
            Console.WriteLine("Code2 - TestCode2");
        }
        [ArgData(1, 3)]
        public void TestWithArgs(List<object> args)
        {
            Console.WriteLine(args.Count);
        }
    }
}

```


## 🔍 실행 흐름 예시 (간략 요약)
```csharp
foreach (var type in assembly.GetTypes())
{
    var macroAttr = type.GetCustomAttribute<MacroAttribute>();
    if (macroAttr != null)
    {
        Console.WriteLine($"[매크로] {macroAttr.Name}");

        foreach (var method in type.GetMethods())
        {
            var argAttr = method.GetCustomAttribute<ArgDataAttribute>();
            var instance = Activator.CreateInstance(type);

            if (argAttr != null)
            {
                method.Invoke(instance, new object[] { argAttr.GetList() });
            }
            else
            {
                method.Invoke(instance, null);
            }
        }
    }
}
```


## ✅ 확장 아이디어
- ArgDataAttribute에 타입 정보 추가 → 인자 타입별 처리 가능
- MacroAttribute에 설명(description) 추가 → UI에 도움말 표시
- 매크로 실행 결과를 반환 받아 후처리 가능하게 설계

---

