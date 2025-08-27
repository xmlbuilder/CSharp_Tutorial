# 언어별 타입 별칭(alias) 비교

| 언어     | 키워드 / 방식         | 타입 별칭 지원 | 예시 코드 | 설명 |
|----------|------------------------|----------------|-----------|------|
| C++      | `using`, `typedef`     | ✅ 지원        | `using NewType = std::map<int, std::string>;` | C++11 이후 `using`이 `typedef`보다 선호됨. 템플릿 타입에도 잘 작동함 |
| C#       | `using` + `=`          | ✅ 지원        | `using NewType = Dictionary<string, Dictionary<int, string>>;` | `using`은 네임스페이스 import와 타입 별칭 정의 둘 다 가능 |
| Java     | 없음 (직접 지원 안 함) | ❌ 미지원      | `Map<String, Map<Integer, String>> dict = new HashMap<>();` | 타입 별칭 기능은 없고, 클래스 import만 가능. 래퍼 클래스로 우회 가능 |


## 🔍 C++에서의 using
using NewType = std::map<int, std::string>;
NewType myMap;


- typedef와 유사하지만 using은 템플릿 타입에도 잘 작동함
- C++11 이후 using이 더 선호됨

## 🔍 C#에서의 using
using NewType = Dictionary<string, Dictionary<int, string>>;
NewType dict = new NewType();


- using은 두 가지 역할:
- 네임스페이스 import (using System;)
- 타입 별칭 정의 (using NewType = ...)
- 긴 제네릭 타입을 간결하게 표현할 수 있어 가독성 향상

## 🔍 Java에서는?
Java는 타입 별칭 기능이 없습니다.
import는 단지 클래스나 패키지를 불러오는 용도이고,
타입 이름을 줄이려면 직접 클래스를 감싸거나 제네릭을 활용한 래퍼 클래스를 만들어야 해요.
예를 들어:
```java
import java.util.Map;

public class NewType extends HashMap<String, Map<Integer, String>> {
    // 커스텀 메서드 추가 가능
}


//혹은 그냥 변수 선언 시 타입을 그대로 써야 합니다:
Map<String, Map<Integer, String>> dict = new HashMap<>();
```


## ✨ 요약
- C++과 C#은 using으로 타입 별칭을 직접 정의 가능
- Java는 타입 별칭을 직접 지원하지 않음 → 클래스 래핑 또는 IDE의 자동완성에 의존
