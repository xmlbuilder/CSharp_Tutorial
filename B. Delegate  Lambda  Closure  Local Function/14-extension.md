## 📘 확장 메서드란?
- 기존 클래스(예: string, List<T>, DateTime)에 새 메서드를 추가하는 것처럼 보이지만, 실제로는 정적 메서드입니다.
- this 키워드를 통해 인스턴스 메서드처럼 호출할 수 있습니다.
- 정의는 별도의 정적 클래스에 있어야 하며, using 지시문을 통해 해당 네임스페이스를 포함해야 호출 가능.

```csharp
public static class StringExtensions {
    public static string Left(this string str, int count) {
        return str.Substring(0, count);
    }
}

string result = "HelloWorld".Left(5); // "Hello"
```

### 📌 전체 샘플
```csharp
using System;
using GrammarTest;

namespace GrammarTest
{
    public static class StringExtensions
    {
        public static string Left(this string strParam, int number)
        {
            strParam += String.Empty;
            number = Math.Abs(number);
            if (number > strParam.Length) number = strParam.Length;
            return strParam.Substring(0, number);
        }

        public static string Right(this string strParam, int number)
        {
            strParam += String.Empty;
            number = Math.Abs(number);
            if (number > strParam.Length) number = strParam.Length;
            return strParam.Substring(strParam.Length - number);         
        }

        public static string Mid(this string strParam, int startIndex)
        {
            strParam += String.Empty;
            startIndex = Math.Abs(startIndex);
            if (startIndex > strParam.Length) startIndex = strParam.Length;
            return strParam.Substring(startIndex - 1);
        }
        
        public static string Mid(this string strParam, int startIndex, int number)
        {
            strParam += String.Empty;
            startIndex = Math.Abs(startIndex);
            if (startIndex > strParam.Length) startIndex = strParam.Length;
            return strParam.Substring(startIndex - 1, number);
        }
    }
}

class Program
{
    static int Main(string[] args)
    {
        
        string strSub="0123456789".Left(7);
        Console.WriteLine(strSub);
        //0123456

        strSub="0123456789".Right(7);
        Console.WriteLine(strSub);
        //3456789

        strSub="0123456789".Mid(2);
        Console.WriteLine(strSub);
        //123456789

        strSub="0123456789".Mid(2,3);
        Console.WriteLine(strSub);
        //123

        string myLongString="HelloWorld!";
        string myShortString=myLongString.Right(6);
        Console.WriteLine(myShortString);
        //"World!"

        string myLeftString=myLongString.Left(5);
        Console.WriteLine(myLeftString);
        //"Hello"
        
        string myMidString1=myLongString.Mid(4);
        Console.WriteLine(myMidString1);
        //"loWorld"

        string myMidString2=myLongString.Mid(2,3);
        Console.WriteLine(myMidString2);
        //"ell"

        return 0;
    }
}
```

### 📌 C++에는 extension 기능이 없다.
```cpp
static std::string Left(const std::string& strParam, int number){
    number = abs(number);
    if(number > strParam.size()) number = (int)strParam.size();
    return strParam.substr(0, number);
}

static std::string Right(const std::string& strParam, int number){
    number = abs(number);
    if(number > strParam.size()) number = (int)strParam.size();
    return strParam.substr(strParam.size() - number);
}

static std::string Mid(const std::string& strParam, int startIndex){
    startIndex = abs(startIndex);
    if(startIndex > strParam.size()) startIndex = (int)strParam.size();
    return strParam.substr(startIndex - 1);
}

static std::string Mid(const std::string& strParam, int startIndex, int number){
    startIndex = abs(startIndex);
    if(startIndex > strParam.size()) startIndex = (int)strParam.size();
    return strParam.substr(startIndex - 1, number);
}

std::string str = "0123456789";
std::string strSub = LIB_STRING::Left(str, 7);
std::cout << strSub << std::endl; 
//0123456
strSub = LIB_STRING::Right(str, 7);
std::cout << strSub << std::endl; 
//3456789

strSub = LIB_STRING::Mid(str, 2);
std::cout << strSub << std::endl; 
//123456789

strSub = LIB_STRING::Mid(str, 2, 3);
std::cout << strSub << std::endl; 
//123
```

### 📌 개발자가 헤매는 이유
- 메서드가 원래 클래스에 없으니 정의 위치를 찾기 어려움
- 특히 여러 프로젝트/라이브러리에서 확장 메서드가 중복되면 혼란 가중
- 디버깅 시 정적 메서드처럼 보이지 않음 → 인텔리센스가 힌트를 주지 않으면 추적 어려움

### 📌 C++에는 확장 메서드가 없다
- C++에서는 클래스 외부에 함수를 정의할 수는 있지만,  
그 함수를 마치 클래스의 인스턴스 메서드처럼 호출하는 기능은 없습니다.

```cpp
std::string Left(const std::string& str, int count) {
    return str.substr(0, count);
}

std::string result = Left("HelloWorld", 5); // "Hello"
```

- str.Left(5) 같은 문법은 불가능
- 대신 네임스페이스나 유틸리티 클래스로 묶어 관리

### 📌 Java도 확장 메서드는 없다
- Java 역시 클래스 외부에서 메서드를 추가할 수 없고,  
기존 클래스에 기능을 확장하려면 상속하거나 유틸리티 클래스를 사용해야 합니다.
```java
public class StringUtils {
    public static String left(String str, int count) {
        return str.substring(0, count);
    }
}
String result = StringUtils.left("HelloWorld", 5); // "Hello"
```

### 📌 비교 요약

| 기능 | C# | C++ | Java |
|-----|-----|-----|------| 
| 확장 메서드 | ✅ this 키워드로 구현 | ❌ 없음 | ❌ 없음 | 
| 호출 방식 | "abc".Left(3) | Left("abc", 3) | StringUtils.left("abc", 3) | 
| 정의 위치 추적 | 어려움 (정적 클래스 + using 필요) | 명확함 | 명확함 | 


---


