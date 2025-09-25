# 🧠 매크로 생성 및 실행 구조

💡 “프로그램이 청사진을 가지고 있으면 매우 유연하게 프로그램을 작성할 수 있다”
→ 이 말처럼, Attribute 기반 구조는 확장성과 유연성을 극대화.

## 소스
```csharp
// 프로그램이 청사진을 가지고 있으면 매우 유연하게 프로그램을 작성 할 수 있다.

using System;
using System.Collections.Generic;
using System.Reflection;
using BaseLib;
namespace MacroRunner
{
    class Program
    {
        static void RunMacroMethods(object obj)
        {
            var type = obj.GetType();
            var methods = type.GetMethods(BindingFlags.Instance | BindingFlags.Public);
            foreach (var method in methods)
            {
                if (method.Name.StartsWith("Test"))
                {
                    var attr = method.GetCustomAttribute(typeof(ArgDataAttribute)) as ArgDataAttribute;
                    if (attr != null)
                    {
                        List<object> lists = attr.GetList();
                        method.Invoke(obj, new object[] {lists});
                    }
                    else
                    {
                        method.Invoke(obj, null);
                    }
                }
            }
        }
        
        static void Main(string[] args)
        {
            Assembly user1 = Assembly.LoadFile(@"D:\Development\csharp\UE\MacroRunner\MacroUser1\bin\Debug\net5.0\MacroUser1.dll");
            Module[] modules = user1?.GetModules();
            foreach (var module in modules)
            {
                Type[] types = module.GetTypes();
                foreach (var type in types)
                {
                    var attr = type.GetCustomAttribute(typeof(MacroAttribute)) as MacroAttribute;
                    if (attr != null)
                    {
                        Console.WriteLine(attr.Name);
                        RunMacroMethods(Activator.CreateInstance(type));
                    }
                    else
                    {
                        var obj = Activator.CreateInstance(type);
                        MethodInfo minfo = type.GetMethod("Execute");
                        if (minfo != null)
                        {
                            minfo.Invoke(obj, null);
                        }
                    }
                }
            }
        }
    }
}

```



## 🔧 핵심 구성 요소

| 구성 요소            | 설명 또는 역할                                      |
|---------------------|-----------------------------------------------------|
| `MacroAttribute`    | 클래스에 매크로 이름을 지정하여 식별 가능하게 함       |
| `ArgDataAttribute`  | 메서드에 실행 인자를 Attribute로 전달함               |
| `RunMacroMethods()` | 클래스 내 `Test`로 시작하는 메서드를 찾아 실행함       |
| `Main()`            | DLL을 로드하고 매크로 클래스를 탐색 및 실행함         |


## 📦 실행 흐름 요약
Assembly.LoadFile(...) → DLL 로드  
→ Module.GetTypes() → 타입 탐색  
→ 타입에 MacroAttribute 있는지 확인  
→ 있으면 인스턴스 생성 후 RunMacroMethods() 호출  
→ 메서드 이름이 "Test"로 시작하면 실행  
→ ArgDataAttribute가 있으면 인자 전달 후 실행



## ✅ 주요 기능 정리
### 🧠 메인 프로그램에서 매크로 생성하기 – 기능 요약

| 기능 항목             | 설명 및 역할                                      | 관련 코드 예시                                  |
|----------------------|--------------------------------------------------|-------------------------------------------------|
| DLL 로드             | 외부 매크로 DLL을 로드                           | `Assembly.LoadFile(...)`                        |
| 매크로 클래스 탐색    | `MacroAttribute`로 매크로 클래스 식별             | `type.GetCustomAttribute(typeof(MacroAttribute))` |
| 메서드 실행 조건      | 이름이 "Test"로 시작하는 메서드만 실행            | `if (method.Name.StartsWith("Test"))`           |
| 인자 전달 처리        | `ArgDataAttribute`가 있으면 인자 리스트 전달      | `method.Invoke(obj, new object[] {lists})`      |
| 기본 메서드 실행      | `Execute()` 메서드가 있으면 실행                 | `type.GetMethod("Execute")`                     |



## 🧩 확장 아이디어
- MacroAttribute에 설명(description) 추가 → UI에 도움말 표시
- ArgDataAttribute에 타입 정보 포함 → 인자 타입별 처리 가능
- 매크로 실행 결과를 반환 받아 후처리 가능하게 설계
- 매크로 실행 로그를 파일로 저장하거나 UI에 표시

---

