# 🧠 Named Argument란?
- 인자 이름을 명시해서 메서드 호출 시 전달
- 인자 순서와 상관없이 명확하게 값 전달 가능
- 선택적 인자(optional parameter)와 함께 쓰면 더욱 강력함
```csharp
Sample(left: "A", right: "B");
Sample(right: "A", left: "B"); // 순서 바꿔도 OK
```


## 🔍 언어별 비교
### ✅ C#
- 지원: 완전 지원
- 장점: 순서 무관, 선택적 인자와 조합 가능
- 예시:
```csharp
public void Log(string message, int level = 1)
{
    Console.WriteLine($"[{level}] {message}");
}

Log(message: "Hello", level: 2);
Log(level: 3, message: "World");
Log(message: "Default"); // level 생략 가능
```


## 🐍 Python
- 지원: 완전 지원
- 예시:
```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet(name="Alice", greeting="Hi")
greet(greeting="Hey", name="Bob")
greet(name="Charlie")  # greeting 생략
```


## 🧱 Java
- 직접 지원은 없음
- 대안:
| 대안 방식         | 설명                                                           | 예시 또는 특징                            |
|------------------|----------------------------------------------------|-------------------------------------------------|
| Builder 패턴      | 메서드 호출 대신 메서드 체이닝으로 인자 설정             | `new Sample().setLeft("A").setRight("B").build()`   |
| 메서드 오버로딩   | 다양한 인자 조합을 지원하는 여러 메서드 정의               | `sample("A")`, `sample("A", "B")` 등                 |
| Map 전달          | 인자 이름을 키로 사용하여 유연하게 값 전달               | `Map<String, Object> args = Map.of("left", "A")`    |
| Record / DTO      | 인자들을 하나의 객체로 묶어 전달                       | `new SampleDTO("A", "B")`                           |
| Varargs + 인덱스  | 가변 인자와 인덱스를 조합해 유사한 효과 구현              | `sample("A", "B")` → 내부에서 위치 기반 처리         |
| Annotation 기반   | 커스텀 어노테이션을 활용해 메타데이터로 인자 의미 부여     | 복잡하지만 프레임워크에서 활용 가능 (예: Spring)     |


예시 (Builder 패턴):
```java
public class Sample {
    private String left;
    private String right;

    public Sample setLeft(String left) {
        this.left = left;
        return this;
    }

    public Sample setRight(String right) {
        this.right = right;
        return this;
    }

    public String build() {
        return left + "-" + right;
    }
}

// 사용
String result = new Sample().setRight("B").setLeft("A").build();
```


## 🧠 C++
- 직접 지원은 없음
- 대안:
| 대안 방식               | 설명                                              | 예시 또는 특징                                         |
|------------------------|---------------------------------------------------|------------------------------------------------------|
| 구조체 초기화           | C++11 이상에서 멤버 이름 기반 초기화 가능                    | `Sample s = { .left = "A", .right = "B" };` (C++20 이상)   |
| 함수 오버로딩           | 다양한 인자 조합을 지원하는 여러 함수 정의                    | `sample("A")`, `sample("A", "B")` 등                |
| default parameter       | 기본값을 지정하여 선택적 인자처럼 사용 가능                 | `void sample(string left, string right = "default")`   |
| Named parameter idiom   | 구조체나 클래스를 인자로 전달하여 이름 기반 인자처럼 사용     | `sample(SampleArgs{ .left = "A", .right = "B" })`        |
| 템플릿 + Tag dispatching| 템플릿과 태그를 활용해 인자 조합을 유연하게 처리             | 고급 기법, 라이브러리에서 사용됨 (예: Boost.Parameter)      |
| std::map 또는 unordered_map | 키-값 쌍으로 인자 전달, 유연하지만 타입 안정성 부족      | `std::map<string, string> args = { {"left", "A"}, {"right", "B"} }` |


예시 (구조체 초기화):
```cpp
struct Sample {
    std::string left;
    std::string right;
};

Sample s = { .right = "B", .left = "A" }; // C++20부터 지원
std::cout << s.left << "-" << s.right << std::endl;
```


## ✨ 요약: Named Argument 유사 기능 비교
| 언어   | Named Argument 지원 | 대안 또는 유사 기능                      |
|--------|----------------------|------------------------------------------|
| C#     | ✅ 완전 지원           | 선택적 인자, 순서 무관 호출 가능         |
| Python | ✅ 완전 지원           | 기본 인자, 키워드 인자                   |
| Java   | ❌ 직접 지원 없음      | Builder 패턴, DTO, Map 전달              |
| C++    | ❌ 직접 지원 없음      | 구조체 초기화(C++20), default parameter |

---



