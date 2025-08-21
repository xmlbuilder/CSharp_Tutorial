# implicit / explicit 변환 (C# vs C++)

## 1. C#의 의미

### implicit

-   **암시적 형변환**: 타입 변환 연산자를 정의해 두면 캐스트 연산자 없이
    자동 변환.
-   문맥상 안전한 변환(정보 손실 없음, 예외 없음)에 사용.

``` csharp
public static implicit operator ColorImage(BinaryImage im)
{
    return new ColorImage(im);
}
```

사용 예:

``` csharp
BinaryImage b = new BinaryImage();
ColorImage c = b; // 암시적 변환 (캐스트 불필요)
```

### explicit

-   **명시적 형변환**: 반드시 `(타입)` 캐스트를 써야 호출됨.
-   정보 손실 가능, 예외 발생 가능 등 **위험 요소가 있는 변환**에 사용.

``` csharp
public static explicit operator bool[](BinaryImage im)
{
    return im._pixels;
}
```

사용 예:

``` csharp
bool[] pixels = (bool[])binaryImage; // 명시적 캐스트 필요
```

------------------------------------------------------------------------

## 2. C++에서의 대응 개념

C++도 사용자 정의 변환을 지원합니다.\
차이점은 **변환 생성자**와 **변환 연산자**를 통해 제공되며, `explicit`
키워드로 암시적/명시적 여부를 제어합니다.

### (A) 변환 생성자

``` cpp
struct ColorImage {
    ColorImage(const struct BinaryImage& b) { /* 변환 로직 */ }
};
```

-   `BinaryImage → ColorImage` 변환 생성자
-   `explicit`이 없으면 **암시적 변환** 허용 → `ColorImage c = binary;`
-   `explicit`을 붙이면 **명시적 캐스트만 가능** →
    `ColorImage c = (ColorImage)binary;`

### (B) 변환 연산자 (`operator Type()`)

``` cpp
struct BinaryImage {
    bool* pixels;

    // 암시적 변환
    operator ColorImage() const {
        return ColorImage(*this);
    }

    // 명시적 변환
    explicit operator bool*() const {
        return pixels;
    }
};
```

사용 예:

``` cpp
BinaryImage b;

// implicit: 자동 변환 허용
ColorImage c = b;  

// explicit: 반드시 캐스트 필요
bool* px = static_cast<bool*>(b);  
```

------------------------------------------------------------------------

## 3. 비교 정리

  --------------------------------------------------------------------------------------------------------------
  개념                         C#                                             C++
  ---------------------------- ---------------------------------------------- ----------------------------------
  암시적 변환                  `implicit operator TargetType(SourceType s)`   `operator TargetType()` (생성자도
                                                                              암시적 변환 지원)

  명시적 변환                  `explicit operator TargetType(SourceType s)`   `explicit operator TargetType()`
                                                                              또는 `explicit` 생성자

  제어 방식                    `implicit`/`explicit` 키워드                   `explicit` 키워드

  사용성                       `(타입)` 캐스트 필요 여부 결정                   암시적 대입 허용 여부 제어

  안전성                       손실 가능성 있는 경우 `explicit` 강제          동일하게 `explicit`으로 강제
  --------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 4. 요약

-   C#과 C++ 모두 **사용자 정의 타입 변환**을 제공.
-   C#은 `implicit` / `explicit` 키워드로 **변환 메서드 자체에 명시**.\
-   C++은 `explicit` 키워드가 **생성자나 변환 연산자**에 붙어 같은
    역할을 수행.
-   **원칙**:
    -   안전한 변환 → `implicit` 허용\
    -   위험한 변환 → `explicit` 강제
