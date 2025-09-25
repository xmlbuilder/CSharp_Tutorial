# 추상 클래스 오버라이드

C# 코드에서 핵심은 **추상 클래스(Abstract Class)**와 **오버라이드(Override)**의 사용입니다.  
그리고 Java, C++에서는 어떻게 대응되는지까지 깔끔하게 정리해드릴게요.

## 소스 코드
```csharp
class Program
{
    internal abstract class Animal
    {
        public abstract string Name { get; set; }
        public abstract void MakeSound();
    }

    public class Cat : Animal
    {
        public override string Name { get; set; }
        public override void MakeSound()
        {
            Console.WriteLine("Meov meov");
        }
    }
    
    public class Dog : Animal
    {
        public override string Name { get; set; }
        public override void MakeSound()
        {
            Console.WriteLine("Bark Bark");
        }
    }

    public static void Main(String[] args)
    {
        Animal cat = new Cat();
        cat.MakeSound();
        Animal dog = new Dog();
        dog.MakeSound();
        
        //Animal animal = new Animal(); //Compile error!!!
    }
}
```



## 🧾 C# 추상 클래스 & 오버라이드 핵심 요약

| 항목               | 설명                                                                 |
|--------------------|----------------------------------------------------------------------|
| `abstract class`   | 직접 인스턴스화 불가능한 클래스. 자식 클래스가 반드시 구현해야 함     |
| `abstract` 멤버    | 구현 없이 선언만 함. 자식 클래스에서 `override`로 구현 필수             |
| `override`         | 부모 클래스의 추상 멤버를 실제 구현하는 키워드                         |
| `internal` 접근자  | 같은 어셈블리 내에서만 접근 가능 (`Main`이 같은 파일에 있으므로 접근 가능) |
| 다형성             | `Animal` 타입으로 `Cat`, `Dog` 객체를 다룰 수 있음 (동적 바인딩)         |
| 인스턴스화 제한    | `new Animal()`은 컴파일 에러. 추상 클래스는 직접 생성 불가               |
| 실행 결과          | `Cat` → "Meov meov", `Dog` → "Bark Bark" 출력                          |



## ✔️ 동작 흐름
- Animal은 추상 클래스 → 직접 생성 불가 (new Animal()은 컴파일 에러)
- Cat, Dog는 Animal을 상속하고 Name, MakeSound()를 오버라이드
- Main()에서 다형성(polymorphism)으로 Animal 타입 변수로 Cat, Dog 객체를 다룸

## ☕ Java에서의 대응
Java에서는 abstract class와 abstract method 키워드를 사용합니다.  
override는 명시적 키워드가 없지만 @Override 어노테이션을 붙여서 컴파일러가 확인하도록 합니다.
```java
abstract class Animal {
    public abstract String getName();
    public abstract void setName(String name);
    public abstract void makeSound();
}

class Cat extends Animal {
    private String name;

    @Override
    public String getName() { return name; }

    @Override
    public void setName(String name) { this.name = name; }

    @Override
    public void makeSound() {
        System.out.println("Meov meov");
    }
}
```

Java에서는 getName() / setName()으로 프로퍼티를 구현합니다. C#의 Name { get; set; }와 대응됩니다.


## 💻 C++에서의 대응
C++에서는 virtual과 = 0을 사용해 **순수 가상 함수(pure virtual function)** 를 정의합니다.
```cpp
class Animal {
public:
    virtual std::string getName() const = 0;
    virtual void setName(const std::string& name) = 0;
    virtual void makeSound() const = 0;
    virtual ~Animal() {} // 가상 소멸자
};

class Cat : public Animal {
private:
    std::string name;
public:
    std::string getName() const override { return name; }
    void setName(const std::string& name) override { this->name = name; }
    void makeSound() const override {
        std::cout << "Meov meov" << std::endl;
    }
};
```

C++에서는 override 키워드가 선택적이지만, 명시하면 실수 방지에 도움이 됩니다.


## 🔍 추상 클래스 & 오버라이드 비교 요약표

| 언어   | 추상 클래스 선언        | 추상 메서드 정의 방식                | 오버라이드 키워드     | 프로퍼티 처리 방식             |
|--------|--------------------------|-------------------------------------|------------------------|-------------------------------|
| C#     | `abstract class`         | `public abstract void Method();`    | `override`             | `Name { get; set; }`          |
| Java   | `abstract class`         | `public abstract void method();`    | `@Override` (어노테이션) | `getName()`, `setName()` 메서드 |
| C++    | 클래스 + `virtual` 키워드 | `virtual void method() = 0;`        | `override` (선택적)     | `getName()`, `setName()` 메서드 |

---






