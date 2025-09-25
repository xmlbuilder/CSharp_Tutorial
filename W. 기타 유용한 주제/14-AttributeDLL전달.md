# Attribute를 활용해 DLL 간 정보를 전달

## 🧠 핵심 개념: Attribute를 이용한 매크로 정보 전달

| Attribute 이름        | 적용 대상       | 주요 목적                          | 예시 사용 방식                          |
|----------------------|----------------|------------------------------------|----------------------------------------|
| `MacroAttribute`     | 클래스          | 매크로 이름 지정 및 식별            | `[Macro("ImageProcessor")]`            |
| `ArgDataAttribute`   | 메서드          | 실행 시 전달할 인자 정보 설정       | `[ArgData("filename.jpg", 80)]`        |


## 소스
```csharp
// BaseLib를 만들어서 여러 DLL을 사용할 Attribute를 생성한다.


using System;
using System.Collections.Generic;

namespace BaseLib
{
    [AttributeUsage(AttributeTargets.Method)]
    public class ArgDataAttribute : Attribute
    {
        private readonly List<object> _objects = new List<object>();
        public ArgDataAttribute(params object[] args)
        {
            foreach (var arg in args)
            {
                _objects.Add(arg);
            }
        }
        public List<object> GetList()
        {
            return _objects;
        }
    }
}

using System;
namespace BaseLib
{
    [AttributeUsage(AttributeTargets.Class)]
    public class MacroAttribute : Attribute
    {
        public string Name { get; set; }
        public MacroAttribute(string name)
        {
            this.Name = name;
        }
        
    }
}


```

## 📦 BaseLib 구조 요약
### 1️⃣ MacroAttribute – 클래스 단위 매크로 식별
[AttributeUsage(AttributeTargets.Class)]
public class MacroAttribute : Attribute
{
    public string Name { get; set; }

    public MacroAttribute(string name)
    {
        this.Name = name;
    }
}


- 클래스에 부여되어 해당 매크로의 이름을 지정
- 예: [Macro("ImageProcessor")]

### 2️⃣ ArgDataAttribute – 메서드에 인자 정보 전달
[AttributeUsage(AttributeTargets.Method)]
public class ArgDataAttribute : Attribute
{
    private readonly List<object> _objects = new List<object>();

    public ArgDataAttribute(params object[] args)
    {
        foreach (var arg in args)
        {
            _objects.Add(arg);
        }
    }

    public List<object> GetList()
    {
        return _objects;
    }
}


- 메서드에 부여되어 실행 시 필요한 인자 정보를 Attribute로 전달
- 예: [ArgData("filename.jpg", 80)]

## 🔍 활용 예시: DLL에서 매크로 탐색 및 실행
foreach (var type in assembly.GetTypes())
{
    var macroAttr = type.GetCustomAttribute<MacroAttribute>();
    if (macroAttr != null)
    {
        Console.WriteLine($"매크로 이름: {macroAttr.Name}");

        foreach (var method in type.GetMethods())
        {
            var argAttr = method.GetCustomAttribute<ArgDataAttribute>();
            if (argAttr != null)
            {
                var args = argAttr.GetList().ToArray();
                var instance = Activator.CreateInstance(type);
                method.Invoke(instance, args);
            }
        }
    }
}



## ✅ 장점
- 확장성: 새로운 매크로 DLL을 추가해도 Attribute만 있으면 자동 탐색 가능
- 유연성: 메서드 인자도 Attribute로 전달 가능해 하드코딩 없이 처리
- 모듈화: BaseLib만 공유하면 다양한 매크로 DLL을 독립적으로 개발 가능

---
