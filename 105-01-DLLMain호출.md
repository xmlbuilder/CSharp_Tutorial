# DLL Main 호출

DLLL에 정의된 메서드(예: Execute)를 메인 프로그램에서 동적으로 호출하는 방법.

## 🧠 핵심 개념 요약: DLL의 메서드에 접근하는 방법
| 단계 | 설명 |
|------|------|
| 1️⃣ Assembly.LoadFile() | 외부 DLL 파일을 로드하여 Assembly 객체 생성 |
| 2️⃣ Module.GetTypes() | DLL 내의 모듈에서 정의된 모든 타입(Type)을 가져옴 |
| 3️⃣ Activator.CreateInstance() | 해당 타입의 인스턴스를 동적으로 생성 |
| 4️⃣ MethodInfo.Invoke() | 생성된 객체에서 특정 메서드(예: Execute)를 실행 |


## 📦 DLL 내부 클래스 예시 (MacroUser1.dll)
namespace MacroUser1
{
    public class UserClass1
    {
        public bool Execute()
        {
            Console.WriteLine("DLL Macro User1");
            return true;
        }
    }
}


- Execute() 메서드는 콘솔에 메시지를 출력하고 true를 반환합니다.
- 이 메서드는 외부에서 호출 가능하도록 public으로 선언되어 있어야 합니다.

## 🚀 메인 프로그램에서 DLL 메서드 호출 (Reflection 사용)
```csharp
using System;
using System.Reflection;

namespace MacroRunner
{
    class Program
    {
        static void Main(string[] args)
        {
            // DLL 로드
            Assembly userAssembly = Assembly.LoadFile(
                @"D:\Development\csharp\UE\MacroRunner\MacroUser1\bin\Debug\net5.0\MacroUser1.dll");

            // 모든 모듈 탐색
            foreach (var module in userAssembly.GetModules())
            {
                // 모든 타입 탐색
                foreach (var type in module.GetTypes())
                {
                    // 인스턴스 생성
                    object instance = Activator.CreateInstance(type);

                    // Execute 메서드 찾기
                    MethodInfo method = type.GetMethod("Execute");
                    if (method != null)
                    {
                        // 메서드 실행
                        method.Invoke(instance, null);
                    }
                }
            }
        }
    }
}

```

## ✅ 주의사항
- DLL 경로는 정확해야 하며, 실행 중인 앱과 호환되는 .NET 버전이어야 합니다.
- Execute()는 public이어야 하며, 매개변수가 없어야 Invoke(obj, null)로 호출 가능해요.
- 예외 처리를 추가하면 안정성이 높아집니다 (try-catch).

---

