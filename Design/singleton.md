## Singleton Pattern이란?

싱글톤 패턴은 어떠한 하나의 클래스를 어플리케이션 내에서 하나의 인스턴스로만 생성해서 사용하도록 하는 디자인 패턴을 말합니다. 즉, **생성자가 여러번 호출되더라도 실제로 생성되는 객체는 하나이며, 최초로 생성된 이후에 호출된 생성자는 이미 생성한 객체를 반환**하도록 만듭니다.

Java 언어에서는 두가자 방법으로 싱글톤 패턴을 구현할 수 있습니다.

### Private 생성자 + Public Static Final 필드

```java
public class Singleton { 
    private static final Singleton INSTANCE = new Singleton();

    private Singleton() { }
}
```

### Private 생성자 + 정적 팩터리 메서드

```java
public class Singleton {
    private static Singleton INSTANCE;

    private Singleton() { }
		
    // 지연 초기화 방식을 사용
    public static Singleton getInstance() {
        if (INSTANCE == null) {
            INSANCE = new Singleton();
        }
        return INSTANCE;
    }
}
```

## 싱글톤 패턴의 장점

객체는 생성할 때마다 메모리 영역을 할당받아야 합니다. 하지만 싱글톤 패턴을 사용하게 되면, 한번 객체를 생성한 이후에는 이를 재사용하기 때문에, 메모리 낭비를 줄일 수가 있습니다.

또한, 싱글톤으로 구현한 인스턴스에는 전역적으로 접근을 할 수 있으므로, 다른 클래스의 인스턴스들이 데이터를 공유하는 것이 가능합니다. (이는 장점이자 단점이라고 생각함)

## 싱글톤 패턴의 문제점

### 객체지향과 어울리지 않는다.

private 생성자를 사용해서 싱글톤 패턴을 구현하기 때문에, 상속을 이용할 수 없고 다형성도 이용이 불가능합니다. 또한, 인스턴스를 반환해주는 구현 클래스를 직접 참조해야 하기 때문에 DIP를 위반한다고 할 수 있습니다.

### 지연 초기화 적용의 싱글톤 패턴 적용시, 멀티 스레드 환경에서 인스턴스가 1개 이상 생성될 수 있다.

지연 초기화 방법을 이용해 싱글톤 패턴을 구현하는 경우, 인스턴스 null 여부를 확인하는 동안 여러 스레드에서 동시에 접근한다면 인스턴스가 여러개 생성될 수 있는 문제가 있습니다.

이를 해결하기 위해서 `synchronized`를 사용할 수 있습니다. 하지만 `synchronized`는 클래스 자체에 락을 걸기 때문에 `getInstance()`를 사용하고 있는 동안 다른 스레드들은 해당 클래스를 사용할 수 없어 성능상의 문제가 발생합니다.

```java
public class Singleton {
    private static Singleton INSTANCE;
		
    private Singleton() {}
		
    public static synchronized Singleton getInstance() {
        if (INSTANCE == null) {
            INSTANCE = new Singleton();
        }
        return INSTANCE;
    }
}
```

두번째 방법으로 `Double Checked Locking`를 사용할 수 있습니다. 이는 초기화 되지 않은 경우에만 동기화를 수행하므로써 `synchronized` 의 성능 문제를 해결할 수 있습니다. 하지만 이 방식은 스레드 별 CPU 캐시와 메인 메모리 간의 동기화를 보장하지 않습니다.

이를 조금 더 자세히 설명한다면, **스레드1 에서 인스턴스를 생성한 후, 스레드2가 `synchronized` 블록 안에서 null 체크를 하는 경우, 스레드1 에서 생성한 인스턴스가 스레드2의 CPU 캐시에는 적용되지 않아서 인스턴스를 다시 생성**할 수 있습니다.

이를 해결하기 위해서 `volatile` 키워드를 추가적으로 사용해줘야 합니다. `volatile` 키워드를 사용하면 인스턴스 변수를 캐시가 아닌 메인 메모리의 데이터를 직접 사용하게 되어 해당 문제를 방지할 수 있습니다.

```java
public class Singleton {
    private volatile static Singleton INSTANCE;

    private Singleton() {}

    public static Singleton getInstance() {
        if (INSTANCE == null) {
            synchronized (Singleton.class) {
                INSTANCE = new Singleton();
            }
        }
        return INSTANCE;
    }
}
```

마지막 방법으로 `LazyHolder`를 사용할 수 있습니다. **싱글톤으로 만들려는 클래스의 내부에 static 내부클래스를 선언**하고, **여기서 싱글톤으로 만들려는 클래스를 클래스 로딩 시점에 생성**합니다.

```java
public class Singleton {
    private Singleton() {}

    private static class InnerInstanceClazz {
        // 클래스 로딩 시점에서 생성
        private static final Singleton UNIQUE_INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return InnerInstanceClazz.UNIQUE_INSTANCE;
    }
}
```

조금 더 자세히 설명하면, 싱글턴 클래스에는 `InnerInstanceClazz` 클래스의 변수가 없기 때문에, `static` 멤버 클래스더라도, 클래스 로더가 초기화 과정을 진행할때 `InnerInstanceClazz` 메서드를 초기화 하지 않고, `getInstance()` 메서드를 호출할때 초기화 됩니다.

이를 사용해서, `synchronized`와 `volatile`을 이용하지 않고도 `Thread-safe`하면서 동적으로 싱글톤을 만들 수 있습니다.

### Java 기반의 서버 환경에서는 싱글톤이 깨질 수 있다

싱글톤 패턴을 만들기 위해서 `private` 생성자를 사용하더라도, 리플렉션을 통해 새로운 객체를 생성할 수 있기 때문입니다. 또한 서버가 여러 개의 `JVM`에 분산되어 설치되는 경우, 각 `JVM` 마다 독립적으로 객체를 생성하기 때문에 싱글톤이 깨지게 됩니다.

## Spring에서의 Singleton

스프링은 기본적으로 내부에서 생성하는 빈을 모두 **싱글톤**으로 만들어 제공합니다. 이때 **싱글톤 패턴을 이용하지 않고, 싱글톤 레지스트리를 이용해 싱글톤을 구현**합니다.

자바와 스프링의 싱글톤의 차이점은 객체의 생명주기가 다르다는 점입니다. **스프링에서는 Bean Scope를 통해 싱글톤 패턴을 다양한 생명주기**로 가져갈 수가 있습니다.

~~또한, 자바에서의 공유 범위는 클래스 로더가 기준이 되지만, **스프링에서는 어플리케이션 컨텍스트가 기준**이 됩니다. 클래스 로더가 기준일 경우, 톰캣이 JAR 파일을 만들게 되면, WAR 파일 하나당 클래스 로더 하나가 1 : 1 로 배치가 되기 때문에 다른 WAR 파일은 참조가 불가능합니다. 하지만 어플리케이션 컨텍스트가 기준일 경우, `Root Context` 하나와 `Servlet Context`를 여러개 등록하여, 각각의 `Context` 들이 싱글톤 범위에 속하도록 형성을 할 수 있습니다.~~

또한 `Static Method`와 `private` 생성자를 이용해서 싱글톤을 만드는 것이 아니라, 평범한 자바 클래스를 싱글톤으로 활용할 수 있게 해줍니다. 이 덕분에 `public` 생성자를 구현할 수 있어서, 생성자 파라미터로 의존관계 주입이 가능하고, 테스트 시 `mock` 객체도 생성이 가능합니다. (오브젝트의 생성권한이 모두 어플리케이션 컨텍스트에 있기 때문! → IoC)
