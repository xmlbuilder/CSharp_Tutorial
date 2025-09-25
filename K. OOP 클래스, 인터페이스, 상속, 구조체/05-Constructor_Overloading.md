# Constructor Overloaing

Constructor Overloading is a technique to define multiple constructors within a class 
with different sets of parameters to achieve polymorphism.

## 🧱 Constructor Overloading 비교: C#, C++, Java

| 언어   | 오버로딩 지원 | 호출 방식                  | 다른 생성자 호출 방식     | 특징 및 주의사항                          |
|--------|----------------|-----------------------------|----------------------------|-------------------------------------------|
| C#     | ✅ 지원         | `new ClassName(...)`        | `: this(...)`              | `this` 키워드로 다른 생성자 호출 가능     |
| Java   | ✅ 지원         | `new ClassName(...)`        | `this(...)`                | 반드시 생성자 첫 줄에서만 호출 가능       |
| C++    | ✅ 지원         | `ClassName(...)`            | `: ClassName(...)`         | 클래스 이름으로 직접 다른 생성자 호출     |



## ✨ 핵심 차이점 요약
- C#: this(...)를 통해 다른 생성자를 호출하며, : 구문으로 연결
- Java: this(...)는 반드시 생성자의 첫 줄에서만 호출 가능
- C++: ClassName(...)으로 다른 생성자를 호출하며, : 초기화 리스트에서 사용

## 🔍 왜 중요한가요?
Constructor Overloading은 다음과 같은 상황에서 유용합니다:
- 기본값을 자동으로 설정하고 싶을 때
- 다양한 초기화 옵션을 제공하고 싶을 때
- 코드 중복을 줄이고 싶을 때
예를 들어 AppVersion("1", "0")처럼 간단한 버전 정보를 넘겨도 내부적으로 "0.0"을 채워주는 방식은 사용자 친화적이고 유지보수에도 좋죠.

## 샘플 코드

### 1. C#
```csharp
using System;
namespace  GrammarTest
{
    public class GrammarTest
    {
        class AppVersion
        {
            public AppVersion(string major, string minor, string build, string revision)
            {
                Major = major;
                Minor = minor;
                Build = build;
                Revision = revision;
            }

            public AppVersion(string major, string minor, string build)
                : this(major, minor, build, "0.0")
            {
                
            }

            public AppVersion(string major, string minor)
                : this(major, minor, "0.0", "0.0")
            {
                
            }
            
            
            public string Major { get; set; }
            public string Minor { get; set; }
            public string Build { get; set; }
            public string Revision { get; set; }
        }
        
        public static void Main(String[] args)
        {
           
        }
    }
}


```
### 2. C++
```cpp
class AppVersion{
public:
    AppVersion(const std::string& major, const std::string& minor, 
        const std::string& build, const std::string& revision){
        
    }
    
    AppVersion(const std::string& major, const std::string& minor, const std::string& build) :
            AppVersion(major, minor, build, "0.0"){
    }
    
    AppVersion(const std::string& major, const std::string& minor) :
            AppVersion(major, minor, "0.0", "0.0"){
        
    }
    
private:
    
    std::string Major;
    std::string Minor;
    std::string Build;
    std::string Revision;
};

```

---
