# shallow copy
**얇은 복사(shallow copy)**의 개념을 아주 잘 보여주는 코드입니다. 

## 🧠 얇은 복사란?
- 얇은 복사는 객체의 참조형 필드나 속성까지 복사하지 않고, 해당 필드의 참조만 복사하는 방식입니다.
- 즉, 복사된 객체와 원본 객체가 같은 메모리 주소의 데이터를 공유하게 됩니다.
- 배열, 리스트, 클래스 같은 참조형 데이터는 얕은 복사 시 서로 영향을 주게 됩니다.

## 🔍 코드 분석
```csharp

namespace  GrammarTest
{
    public class Program
    {
        class MyObject
        {
            public int[] ar1 = new[] {1, 2, 3};
            public MyObject make_copy()
            {
                MyObject myObject = new MyObject();
                myObject.ar1 = (int[])this.ar1.Clone();
                return myObject;
            }
        }
        
        public static void Main(String[] args)
        {
            MyObject obj1 = new MyObject();
            obj1.ar1[2] = 10;
            
            MyObject obj2 = obj1.make_copy();
            obj1.ar1[2] = 3;
            
            Console.WriteLine(obj1.ar1[2]);
            //3

            Console.WriteLine(obj2.ar1[2]);
            //10
        }
    }
}
```

```csharp
public MyObject make_copy()
{
    MyObject myObject = new MyObject();
    myObject.ar1 = (int[])this.ar1.Clone(); // 얕은 복사
    return myObject;
}
```

- this.ar1.Clone()은 배열의 내용을 복사하지만, 배열 자체는 여전히 참조형입니다.
- 따라서 make_copy()는 MyObject의 새 인스턴스를 만들지만, ar1 배열은 복사된 배열이지만 여전히 얕은 복사로 처리됩니다.

## 🧪 실행 흐름
obj1.ar1[2] = 10;       // obj1의 배열 값 변경
MyObject obj2 = obj1.make_copy();  // obj2는 obj1의 배열을 복사받음
obj1.ar1[2] = 3;        // obj1의 배열 값 다시 변경


- obj2는 obj1의 배열을 복사받았지만, Clone()을 통해 배열 자체는 분리됨
- 따라서 obj2.ar1[2]는 여전히 10을 유지하고, obj1.ar1[2]는 3으로 변경됨


## ⚠️ 얕은 복사 vs 깊은 복사

| 복사 방식   | 참조형 필드 처리 방식         | 복사 방법 예시                          | 특징 및 주의점                          |
|-------------|-------------------------------|-----------------------------------------|------------------------------------------|
| 얕은 복사   | 참조만 복사                   | `Clone()`, `Array.Clone()`, `MemberwiseClone()` | 내부 객체는 공유됨 → 원본 변경 시 영향 |
| 깊은 복사   | 참조형도 새로 생성하여 복사   | 직접 구현 필요 (재귀적 복사 등)         | 완전히 독립된 객체 생성 → 안전한 복사   |


----

# Deep Copy
C#에서 ICloneable 인터페이스를 활용해 **깊은 복사(deep copy)**를 구현한 예제입니다. 
 예제는 참조형 필드(배열)를 포함한 객체를 복사하면서 원본과 완전히 독립적인 복사본을 만드는 방식이에요.

## 🧪 깊은 복사 예제: ICloneable 구현
```csharp
using System;

namespace GrammarTest
{
    class MyObject : ICloneable
    {
        public int[] ar1;

        public MyObject()
        {
            ar1 = new[] { 1, 2, 3 };
        }

        // 깊은 복사 구현
        public object Clone()
        {
            MyObject copy = new MyObject();
            copy.ar1 = (int[])this.ar1.Clone(); // 배열 내용 복사
            return copy;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            MyObject obj1 = new MyObject();
            obj1.ar1[2] = 10;

            MyObject obj2 = (MyObject)obj1.Clone(); // 깊은 복사
            obj1.ar1[2] = 3;

            Console.WriteLine(obj1.ar1[2]); // 3
            Console.WriteLine(obj2.ar1[2]); // 10
        }
    }
}
```


## ✅ 핵심 포인트
- ICloneable 인터페이스는 Clone() 메서드를 통해 복사 기능을 제공합니다.
- Clone() 내부에서 배열을 this.ar1.Clone()으로 복사하면 새로운 배열 인스턴스가 생성되어 원본과 분리됩니다.
- 결과적으로 obj1과 obj2는 서로 다른 배열을 참조하게 되어, 값 변경이 서로에게 영향을 주지 않아요.

## ⚠️ 주의사항
- ICloneable은 .NET에서 권장되지 않는 경우도 있음: 이유는 Clone()의 복사 방식이 깊은 복사인지 얕은 복사인지 명확하지 않기 때문이에요.
- 따라서 실무에서는 ICloneable 대신 명시적인 복사 메서드를 만드는 방식도 많이 사용됩니다:

```csharp
public MyObject DeepCopy()
{
    return new MyObject { ar1 = (int[])this.ar1.Clone() };
}
```

---




