# C# `explicit` 강제 변환 예제 정리 (JsExpression)

## 요약

-   **의도**: 숫자 타입(`int`, `double`)을 **JavaScript 표현식
    문자열**로 감싸는 래퍼 `JsExpression`를 만들고,\
    숫자 → `JsExpression` 변환을 **명시적(explicit)** 또는
    **암시적(implicit)** 으로 제어.
-   **핵심 차이**
    -   `explicit`: **반드시 캐스트** 필요 → `(JsExpression) -1`
    -   `implicit`: **캐스트 없이** 대입 가능 → `JsExpression e = -1`

------------------------------------------------------------------------

## 현재 코드 핵심

``` csharp
public class JsExpression
{
    private readonly string expression;

    public JsExpression(string rawExpression) => expression = rawExpression;

    public override string ToString() => expression;

    public JsExpression IsEqualTo(JsExpression other)
        => new JsExpression("(" + this + " == " + other + ")");

    // (주석 처리됨) 명시적 변환
    // public static explicit operator JsExpression(int value)   => new JsExpression(value.ToString());
    // public static explicit operator JsExpression(double value)=> new JsExpression(value.ToString());

    // (활성) 암시적 변환
    public static implicit operator JsExpression(int value)    => new JsExpression(value.ToString());
    public static implicit operator JsExpression(double value) => new JsExpression(value.ToString());
}
```

### 사용 예 (암시적 변환 활성 상태)

``` csharp
JsExpression intExpression = new JsExpression("-1");
JsExpression doubleExpression = new JsExpression("-1.0");
Console.WriteLine(intExpression.IsEqualTo(doubleExpression)); // (-1 == -1.0)

JsExpression e1 = -1;     // implicit
JsExpression e2 = -1.0;   // implicit
Console.WriteLine(e1);    // -1
Console.WriteLine(e2);    // -1
```

------------------------------------------------------------------------

## `explicit`로 강제했을 때 어떻게 달라지나?

아래처럼 **explicit** 연산자를 활성화(주석 해제)하고, **implicit을
제거**한다고 가정하면:

``` csharp
public static explicit operator JsExpression(int value)    => new JsExpression(value.ToString());
public static explicit operator JsExpression(double value) => new JsExpression(value.ToString());

// implicit 연산자는 제거
```

### 결과

-   **반드시 캐스트 필요**:

    ``` csharp
    JsExpression e1 = (JsExpression)(-1);
    JsExpression e2 = (JsExpression)(-1.0);
    ```

-   실수로부터의 **무의식적인 변환을 차단**하여, API 경계에서 의도를
    분명히 함.

-   특히 오버로드/제네릭 문맥에서 **모호성/예상치 못한 변환**을 줄임.

> 가이드라인:\
> - **의미 손실 가능성**(예: 소수점 포맷, 문화권별 `ToString()`)
> - **비용 큰 변환**(무거운 래핑/검증)
> - **의도적 설계 제약**(JS 표현식과 일반 숫자의 혼동 방지)
> 이 포함되면 `explicit`을 선호하세요.

------------------------------------------------------------------------

## 출력/동작 비교 (Markdown 표)

| 코드                           | 변환자   | 가능 여부   | 예시                          |
|--------------------------------|----------|-------------|-------------------------------|
| `JsExpression e = -1;`         | implicit | ✅ 자동 변환 | 편리하지만 의도가 흐려질 수 있음 |
| `JsExpression e = -1;`         | explicit | ❌ 오류     | 캐스트 강제됨                  |
| `JsExpression e = (JsExpression)(-1);` | explicit | ✅ 명시적 변환 | 의도가 명확                     |

------------------------------------------------------------------------

## 추가 팁

1.  **`ToString()` 포맷 주의**
    -   `double.ToString()`은 문화권(CultureInfo)에 따라 `,`/`.` 등
        포맷이 달라질 수 있습니다.\

    -   JS 리터럴용이면 **InvariantCulture**를 권장:

        ``` csharp
        new JsExpression(value.ToString(CultureInfo.InvariantCulture))
        ```
2.  **오버로드 확장**
    -   필요하면 `decimal`, `long`, `bool`, `string`(따옴표 처리) 등도
        변환자/팩토리로 제공:

        ``` csharp
        public static explicit operator JsExpression(string s) => new JsExpression($"\"{s}\"");
        ```
3.  **팩토리 메서드 대안**
    -   변환자를 과하게 늘리기보단, 의도 명확한 팩토리도 좋습니다:

        ``` csharp
        public static JsExpression FromNumber(double v) => new JsExpression(v.ToString(CultureInfo.InvariantCulture));
        ```

------------------------------------------------------------------------

## 결론

-   **explicit 변환**은 "이 타입은 **함부로 변환되면 안 된다**"는 설계를
    **컴파일 타임**에 강제하는 장치입니다.
-   `JsExpression`처럼 **도메인 의미가 분명한 래퍼**에서는
    **explicit**을 통해 의도를 드러내고, 필요한 곳에서만 **캐스트로
    명시적 전환**을 사용하세요.
