# 📊 Nullable vs Optional 비교표

| 언어       | 기능 이름           | 값 형식 지원 | null 표현 방식         | 주요 메서드 / 속성               | 예외 처리 방식                     |
|------------|---------------------|--------------|------------------------|----------------------------------|------------------------------------|
| **C#**     | `Nullable<T>`       | ✅ (값 형식)  | `T?` 또는 `Nullable<T>`| `.HasValue`, `.Value`            | `InvalidOperationException` 발생   |
| **Java**   | `Optional<T>`       | ✅ (참조형)   | `Optional.empty()`     | `.isPresent()`, `.get()`, `.orElse()` | `NoSuchElementException` 발생     |
| **C++17**  | `std::optional<T>`  | ✅ (값 형식)  | `std::nullopt`         | `.has_value()`, `.value()`, `.value_or()` | `bad_optional_access` 예외 발생   |



## 🧠 C# Nullable 핵심 요약
- int?, bool?, Vector?처럼 값 형식에 null을 허용
- .HasValue로 null 여부 확인, .Value로 값 접근
- 명시적 캐스팅 없이 값 접근 시 예외 발생 가능
```csharp
int? n = null;
int n2 = (int)n; // 예외 발생 가능
```


## ☕ Java Optional 예시
```java
Optional<Integer> opt = Optional.ofNullable(null);
if (opt.isPresent()) {
    System.out.println("Value: " + opt.get());
} else {
    System.out.println("No value");
}
```

- Optional<T>는 참조형에만 적용 (값 형식은 오토박싱됨)
- Optional.empty()로 null 표현
- .get() 호출 시 값 없으면 NoSuchElementException 발생

## 🧊 C++17 Optional 예시
```cpp
std::optional<int> opt = std::nullopt;
if (opt.has_value()) {
    std::cout << "Value: " << opt.value() << "\n";
} else {
    std::cout << "No value\n";
}
```

- std::optional<T>는 값 형식에도 적용 가능
- std::nullopt로 null 표현
- .value() 호출 시 값 없으면 bad_optional_access 예외 발생

## 🔍 C# 구조체 Nullable 예시
```csharp
public struct Vector
{
    public int x, y, z;
}

Vector? v2 = null;
Nullable<Vector> v3 = null;
```

- 구조체도 Nullable<T>로 null 허용 가능
- Vector v1 = null;은 컴파일 오류 → 값 형식은 기본적으로 null 불가

💡 요점 정리
- C#은 값 형식에 직접 null을 허용하는 유일한 언어
- Java와 C++은 Optional 객체를 통해 null을 우회적으로 표현
- 세 언어 모두 명시적 null 체크 없이 값 접근하면 예외 발생


## 🧊 C++ 예시 정리 (std::optional<double>)
```cpp
std::optional<double> CalcInjuryBase::getDoubleValue(const std::string& strKey, double dScale)
{
    if (m_pKeyValuePool->hasValue(strKey)) {
        return strtof(m_pKeyValuePool->getValue(strKey).c_str(), nullptr) * dScale;
    }
    return std::nullopt;
}

// 사용 예
std::optional<double> optPointHead3MS = calInjNCAP->getDoubleValue("P4Head3MS");
if (optPointHead3MS.has_value()) {
    double dPointHead3MS = optPointHead3MS.value();
    QTableWidgetItem* item = new QTableWidgetItem(QString::number(dPointHead3MS, 'f', 3));
    item->setTextAlignment(Qt::AlignCenter);
    ui->tableWidget->setItem(startIndex + 1, 2, item);
}
```

### ✅ 의미: 0.0이라는 값이 실제로 존재하는 것인지, 아니면 값이 없어서 디폴트로 처리된 것인지 명확히 구분됨.

### ☕ Java 예시 정리 (Optional<Double>)
```java
public Optional<Double> getDoubleValue(String key, double scale) {
    if (keyValuePool.containsKey(key)) {
        String raw = keyValuePool.get(key);
        double value = Double.parseDouble(raw) * scale;
        return Optional.of(value);
    }
    return Optional.empty();
}

// 사용 예
Optional<Double> optPointHead3MS = getDoubleValue("P4Head3MS", 1.0);
if (optPointHead3MS.isPresent()) {
    double dPointHead3MS = optPointHead3MS.get();
    tableModel.setValueAt(String.format("%.3f", dPointHead3MS), rowIndex, columnIndex);
}
```

### ✅ 의미: null을 직접 다루지 않고, 명시적으로 값의 존재 여부를 표현함.

## 📊 Nullable/Optional 비교표 (C# vs C++ vs Java)

| 언어       | 표현 방식               | 값 존재 여부 확인       | 값 접근 방식         | 예외 발생 조건                  |
|------------|--------------------------|--------------------------|----------------------|----------------------------------|
| C#         | `Nullable<T>` 또는 `T?`  | `.HasValue`              | `.Value` 또는 캐스팅 | `InvalidOperationException`     |
| C++        | `std::optional<T>`       | `.has_value()`           | `.value()`           | `bad_optional_access`           |
| Java       | `Optional<T>`            | `.isPresent()`           | `.get()`             | `NoSuchElementException`        |






## ☕ Java Optional<Double> 구현 코드
```java
import java.util.Optional;
import java.util.HashMap;
import java.util.Map;

public class InjuryCalculator {

    private Map<String, String> keyValuePool = new HashMap<>();

    public InjuryCalculator() {
        keyValuePool.put("P4Head3MS", "12.345");
        // 다른 키는 생략하거나 존재하지 않게 설정 가능
    }

    public Optional<Double> getDoubleValue(String key, double scale) {
        if (keyValuePool.containsKey(key)) {
            try {
                double value = Double.parseDouble(keyValuePool.get(key)) * scale;
                return Optional.of(value);
            } catch (NumberFormatException e) {
                // 파싱 실패 시 Optional.empty() 반환
            }
        }
        return Optional.empty();
    }

    public void displayValue(String key) {
        Optional<Double> optValue = getDoubleValue(key, 1.0);
        if (optValue.isPresent()) {
            double value = optValue.get();
            System.out.printf("✔ %s = %.3f%n", key, value);
        } else {
            System.out.printf("✘ %s 값 없음 또는 파싱 실패%n", key);
        }
    }

    public static void main(String[] args) {
        InjuryCalculator calc = new InjuryCalculator();
        calc.displayValue("P4Head3MS");   // 값 있음
        calc.displayValue("P4Chest3MS");  // 값 없음
    }
}

```

## 🔍 핵심 포인트
- Optional.of(value) → 값이 존재할 때
- Optional.empty() → 값이 없거나 파싱 실패
- .isPresent() 또는 .ifPresent()로 존재 여부 확인
- .get()은 값이 없을 경우 NoSuchElementException 발생 가능
