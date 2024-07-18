# 자바8, 11, 17에서의 인터페이스 변경점

## 자바 8:

인터페이스에 중요한 변화가 있었습니다. 주요 변경 사항은 다음과 같습니다:

1. 디폴트 메소드 (Default Methods):
   - 인터페이스에 구현부가 있는 메소드를 정의할 수 있게 되었습니다.
   - 이를 통해 인터페이스를 구현하는 클래스에서 메서드를 오버라이드하지 않고도 기본 구현을 사용할 수 있습니다.
   - 이를 통해 하위 호환성을 유지할 수 있습니다.

```java
public interface MyInterface {
    default void defaultMethod() {
        System.out.println("This is a default method");
    }
}

public class MyClass implements MyInterface {
    // defaultMethod를 오버라이드하지 않으면 인터페이스의 기본 구현이 사용됩니다.
}
```

2. 정적 메소드 (Static Methods):

   - 인터페이스 내에 static 메소드를 정의할 수 있게 되었습니다.

   - 이는 클래스와 유사하게 인터페이스에서도 유틸리티 메서드를 제공할 수 있게 합니다.

```java
public interface MyInterface {
    static void staticMethod() {
        System.out.println("This is a static method");
    }
}

public class MyClass {
    public void someMethod() {
        MyInterface.staticMethod(); // 정적 메서드 호출
    }
}
```



3. 함수형 인터페이스 (Functional Interfaces)와 람다 표현식 (Lambda Expressions):

   - 자바 8에서는 함수형 프로그래밍을 지원하기 위해 인터페이스에 단 하나의 추상 메서드만 있는 경우 이를 함수형 인터페이스로 간주합니다. 이러한 인터페이스는 `@FunctionalInterface` 어노테이션을 사용할 수 있습니다.

   - 함수형 인터페이스는 람다 표현식과 함께 사용할 수 있어, 코드를 더 간결하고 읽기 쉽게 만듭니다.

```java
@FunctionalInterface
public interface MyFunctionalInterface {
    void singleAbstractMethod();
}

public class MyClass {
    public void someMethod() {
        MyFunctionalInterface func = () -> System.out.println("Lambda expression");
        func.singleAbstractMethod(); // 람다 표현식 실행
    }
}
```

## 자바 11:

1. 인터페이스 내 private 메소드:
   - 인터페이스 내에 private 메소드와 private static 메소드를 정의할 수 있습니다.
   - 이 메소드들은 인터페이스 내부에서만 사용 가능합니다.
   - 주로 default 메소드나 static 메소드의 내부 로직을 재사용하거나 복잡한 로직을 분리할 때 사용합니다.
   - 인터페이스의 public API를 깔끔하게 유지하면서 내부 구현을 숨길 수 있습니다.

```java
public interface MyInterface {
    default void publicMethod() {
        System.out.println("Public method");
        privateMethod();
    }

    private void privateMethod() {
        System.out.println("Private method");
    }

    static void staticPublicMethod() {
        System.out.println("Static public method");
        staticPrivateMethod();
    }

    private static void staticPrivateMethod() {
        System.out.println("Static private method");
    }
}
```



## 자바 17:

1. Sealed Classes and Interfaces:
   - Sealed Classes and Interfaces 이 기능은 자바 15에서 preview로 도입되어 자바 17에서 정식 기능이 되었습니다.
   - 'sealed' 키워드를 사용하여 인터페이스나 클래스를 선언할 수 있습니다.
   - 'permits' 키워드를 사용하여 해당 인터페이스를 구현하거나 클래스를 상속할 수 있는 클래스를 명시적으로 지정합니다.

```java
public sealed interface Vehicle permits Car, Truck, Motorcycle {
    void drive();
}

// final 클래스 - 더 이상 상속할 수 없음
public final class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Car is driving");
    }
}

// non-sealed 클래스 - 누구나 상속 가능
public non-sealed class Truck implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Truck is driving");
    }
}

// sealed 클래스 - 지정된 클래스만 상속 가능
public sealed class Motorcycle implements Vehicle 
    permits SportBike, CruiserBike {
    @Override
    public void drive() {
        System.out.println("Motorcycle is driving");
    }
}

// Motorcycle의 하위 클래스들
public final class SportBike extends Motorcycle {
    // 구현...
}

public final class CruiserBike extends Motorcycle {
    // 구현...
}
```

