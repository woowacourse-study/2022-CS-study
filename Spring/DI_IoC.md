![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F31a2f5b0-78a6-441f-920f-e66604565ebf%2FUntitled.png?table=block&id=d3f9494a-3adf-4475-8b69-7cd45f7533b7&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=2000&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F99658458-4788-4af2-a581-f4a9c890f58c%2FUntitled.png?table=block&id=0e9d8682-79b4-4363-8e52-b334d92cfd3c&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=2000&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

# 1. IoC(Inversion of Control) : 제어의 역전

## ✨ 개념

<aside>
📢 Spring에만 적용되는 개념이 아니다!

</aside>

> Michael Mattson의 1996년 논문 [Object-Oriented Frameworks: A survey of methodological issues](https://www.semanticscholar.org/paper/Object-Oriented-Frameworks-%3A-A-Survey-of-Issues-Mattsson/1d13fcb7b9b2bef5e2be3728d3168588a0e55c47)에서 처음 등장한 개념.
>

제어의 역전, 즉 IoC는 개발자가 구현한 프로그램이 외부 라이브러리의 [흐름 제어](https://ko.wikipedia.org/wiki/%ED%9D%90%EB%A6%84_%EC%A0%9C%EC%96%B4)를 받는 [디자인](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EB%94%94%EC%9E%90%EC%9D%B8_%ED%8C%A8%ED%84%B4) 원칙을 말한다.

[- 위키백과](https://ko.wikipedia.org/wiki/%EC%A0%9C%EC%96%B4_%EB%B0%98%EC%A0%84)

### 호출 방향

- 전통적인 프로그래밍 : 개발자의 구현 → 외부 라이브러리 코드
- IoC 구조 : 외부 라이브러리 코드 → 개발자의 구현

## ✨ “할리우드 원칙” 이라고도 불린다!

> "우리에게 전화하지 마세요. 우리가 당신에게 연락할게요.”
>

### Don't call us, we'll call you😎

“GoF의 디자인 패턴”의 **템플릿 메소드 패턴** 챕터에서 등장하는 비유

- 뽑기 어려운 사원에게 나중에 연락할 테니 자꾸 전화하지 말라는 회사 측의 말로 자주 쓰인다.
- 1960년대 미국에서 면접관들이 쓰기 시작한 말인데, 나중에 극장에서 배우들의 오디션을 보고 거절할 때 더 많이 써 유명해졌다.
- 여기서 **배우**는 `구현 모듈`, **영화사**는 `프레임워크`라고 볼 수 있다.

### From. 템플릿 메소드 패턴📥

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2e832a6c-9e9a-4f66-b3d0-2455543ff922%2FUntitled.png?table=block&id=8e076e35-a9cc-4348-bfb8-c20c42159213&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=2000&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

흐름제어의 주체는 부모 클래스인 `AbstractClass`이다.

- **부모 클래스**(= 외부 프로그램, 상위클래스, 추상클래스, `AbstractClass`)는
- **서브 클래스**(= 모듈, 하위클래스, 구체클래스,  `ConcreteClass`) 연산을 호출할 수 있지만,
- 반대방향으로 호출할 수는 없다!

이외에도

전략 패턴, 서비스 로케이터 패턴, 팩토리 패턴, 의존관계 주입(DI) ,의존관계 검색(DI)

등의 방법으로 IoC를 달성할 수 있다!

## ✨ 해결

### 의존성 부패(dependency rot)😱

> 의존성 부패 : 고수준 구성요소가 저수준 구성요소에 의존하고, 그 저수준 구성요소는 다시 고수준 구성요소에 의존하고, 그 고수준 구성요소는 다시 또 다른 구성요소에 의존하고, 그 다른 구성요소는 또 저수준 구성요소에 의존하는 것과 같은 식으로 **의존성이 복잡하게 꼬여있는 것**
> - ***Head First Design Patterns***
>

### 결합도🔗

- 작업의 **구현방식(**모듈**)**과 작업의 **수행(**외부 프로그램**)** 자체를 분리한다. 그로써 **결합도**를 줄인다.
- 따라서 모듈을 **변경**해도 다른 시스템에 부작용을 일으키지 않는다.

# 2. DIP**(Dependency Inversion principle) : 의존성 역전 원칙**

## ✨ 개념

**`Robert Martin`** (Uncle BoB)에 의해 소개된 **`SOLID`** 원칙 중 하나.

하위 레벨 모듈의 변경이 상위 레벨 모듈까지 전파되는 구조적 문제에서 발생하는 위계관계를 끊기 위한 원칙이다.



아래 2가지 원칙을 따른다.

- 상위 모듈은 하위 모듈에 종속되면 안되고 둘 다 **`추상화`**에 의존해야  한다.
- 추상화는 세부사항에 의존하면 안 되고, 세부사항은 추상화에 의존해야 한다.

## ✨ 장점

- 변화하지 않는 추상화에 의존함으로써, 확장에 유연하고 변경에 폐쇄적인 설계가 가능하다.즉, `OCP`를 지킬 수 있다.
- 모듈 설정이 편리해진다.

# 3. DI(Dependency Injection) : 의존관계 주입

## ✨ 개념

> 마틴 파울러가 창시한 개념.
>

하나의 객체가 다른 객체의 의존성을 제공하는 테크닉이다.

- "의존관계"는 예를 들어 **구현방식(**모듈**)**이다. 클라이언트는 모듈을 실행한다.
- "주입"은 의존관계(모듈)을 클라이언트로 전달하는 것을 의미한다.

클라이언트가 서비스(모듈)를 직접 구축하거나 찾는 대신, 클라이언트에게 서비스(모듈)를 전달(주입)하는 것이 패턴의 기본 요건이다.

-[위키백과](https://ko.wikipedia.org/wiki/%EC%9D%98%EC%A1%B4%EC%84%B1_%EC%A3%BC%EC%9E%85)

## ✨ 장점

- 객체의 생성과 사용의 관심을 분리한다!
- 그로써 가독성과 코드 재사용률을 높혀준다.
- 모듈의 테스트가 쉬워진다.

## ✨ 의존관계 주입

> 'Dependency Injection'은 여러 가지 우리말로 번역돼서 사용된다. 그중에서 가장 흔히 사용되는 용어가 의존성 주입이다. 하지만 의존성이라는 말은 DI의 의미가 무엇인지 잘 드러내주지 못한다.
> (…) 엄밀히 말해서 오브젝트는 다른 오브젝트에 주입할 수 있는 게 아니다. 오브젝트의 레퍼런스가 전달될 뿐이다.

> DI는 오브젝트 레퍼런스를 외부로부터 제공(주입)받고 이를 통해 여타 오브젝트와 다이내믹하게 의존관계가 만들어지는 것이 핵심이다. 용어는 동작방식(메커니즘)보다는 의도를 가지고 이름을 짓는 것이 좋다. 그런 면에서 **의존관계 주입**이라는 번역이 적절할 듯싶고, 이 책에서는 이를 사용한다.
> - 토비의 스프링 1.7.2 112쪽
>

# 4. IoC, DI, DIP 적용 예시

```java
public interface Person {
  String study();
  String sleep();
}

public class Summer implements Person {
  @Override
  public String study() {
    return "써머는 선릉에서 공부한다.";
  }
  @Override
  public String sleep() {
    return "써머는 잔다.";
  }
}

public class Philz implements Person {
  @Override
  public String study() {
    return "필즈는 잠실에서 공부한다.";
  }
  @Override
  public String sleep() {
    return "필즈는 잔다.";
  }
}
```

```java
public class PersonService {

  private Person person;

  public PersonService(Person person) {
    this.person = person;
  }

  public String wootecoLife() {
    return person.study() + person.sleep();
  }
}
```

```java
public class PersonServiceFactory {

  public static PersonService summerService() {
    return new PersonService(new Summer())
  }

  public static PersonService philzService() {
    return new PersonService(new Philz())
  }
}
```

```java
public class Application {

  public static void main(String[] args) {
    PersonService summerService = PersonServiceFactory.summerService()
    summerService.wootecoLife();
    // "써머는 선릉에서 공부한다. 써머는 잔다."
  }
}
```

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa9234467-ad02-4ad2-8a72-92517e0ebf3c%2FUntitled.png?table=block&id=c9397c3b-a922-4520-9dde-bfcc08418d66&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=2000&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

👍 **IoC**를 만족하는 설계 구조

- 흐름제어의 주체가 상위 클래스인 `PersonService`이다.
- `PersonService` 는 구현 모듈인 `Person`을 호출해 `wootecoLife()`로직을 상영한다!
- 반면 `Person`은 `PersonService`를 알지 못한다.

👍 **DIP**를 만족하는 설계 구조

- 상위모듈 `PersonService`는 구체 클래스 `Summer`가 아닌, 추상화된 `Person`에 의존하고 있다.
    - 즉, 상위모듈 `PersonService`, 하위모듈 `Summer` 모두 추상화된 `Person`에 의존하고 있다!
- 또한 추상화된 `Person`은 `Summer`나 `Philz`에 의존하지 않으며, `Summer`, `Philz`만 추상화된 `Person`에 의존한다.

👍 **DI**를 만족하는 설계 구조

- 클라이언트 `PersonService`는 의존관계 `Person`의 어떤 객체를 쓸지 직접 new로 정의하지 않는다.
- 객체 생성 책임을 담당하는 `PersonServiceFactory` 에서 **생성자**를 통해 의존관계 `Person`을 **주입**받는다!

# 4. Spring DI/IoC

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa9b5d3e1-84fa-4615-95ef-d1640364b97c%2FUntitled.png?table=block&id=0fb5fa72-666b-4e87-b4a3-3278bf5699bb&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=2000&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

## ✨ **IoC 컨테이너(DI 컨테이너)**

> **IoC**는 매우 느슨하게 정의돼서 폭넓게 사용되는 용어이다. 몇몇 사람의 제안으로 스프링이 제공하는 IoC 방식을 핵심을 짚어주는 **의존관계 주입**(`Dependency Injection`)이라는, 좀 더 의도가 명확히 드러나는 이름을 사용하기 시작했다.
>

> **스프링이 여타 프레임워크와 차별화돼서 제공해주는 기능은 `의존관계 주입`이라는 새로운 용어를 사용할 때 분명하게 드러난다.** 그래서 초기에는 주로 IoC 컨테이너라고 불리던 스프링이 지금은 의존관계 주입 컨테이너 또는 그 영문약자를 써서 `DI 컨테이너`라고 더 많이 불리고 있다.
>

### 정의

자동으로 의존관계를 주입해주는 스프링 프레임워크(컨테이너)!

- `Bean`을 생성하고,
- 생명주기를 관리하고
- 의존성 있는 객체를 다른 객체에게 주입해주는 역할을 담당한다.

보통 `빈 팩토리`를 상속한 `애플리케이션 컨텍스트`(`application context`)를 주로 사용한다.

(빈 팩토리 `=. 애플리케이션 컨텍스트 = DI 컨테이너)

### 역할

- `애플리케이션 컨텍스트`는 애플리케이션에서 `IoC`를 적용해서 관리할 “모든 `bean` 객체에 대한 생성과 관계 설정”을 담당한다.
- 위 예시의 `PersonServiceFactory` 의 역할을 한다고 보면 된다.
- 이 DI 컨테이너에서 `bean` 객체에 대한 자동 DI 가 이뤄진다!

### 장점

- 개발자가 직접 `POJO`를 생성하고 관리할 필요가 없다.
- 개발자는 비즈니스 로직에 집중할 수 있다.
- 객체 생성 코드가 없으므로 `TDD`가 용이하다.

## ✨ Spring DI

`@Bean`이나 `@Configuration` 및 `@Bean` 으로 빈을 등록하면, 등록된 빈들 사이의 의존관계가 자동으로 주입된다.

### 💬 주입 방법 1 : **생성자 주입(Constructor Injection)**

별다른 어노테이션 없이 매개변수 생성자만 열어두면 사용 가능하다. 즉, `@Autowired` 어노테이션을 생략해도 된다

```java
@Component
public class Store {

    private Item item;

    public Store(Item item) {
        this.item = item;
    }
}
```

### 💬 주입 방법 2 : 필드 **주입(Field Injection)**

`@Autowired` 주석으로 의존관계를 주입할 수 있다 .

```java
@Component
public class Store {

    @Autowired
    private Item item;
}
```

### 💬 주입 방법 3 : 세터 **주입(Setter Injection)**

만약 필수적인 의존성을 줘야하는 곳에서 **세터 주입**을 사용하면

null에 대한 검증 로직을 모든 필드에 추가해주어야 한다.

(not-null 체크와 같은 로직)

```java
@Component
public class Store {

    private Item item;

    @Autowired
    public void setItem(Item item) {
        this.item = item;
    }
}
```

### 💬 생성자 주입을 쓰자!

**1. 단일 책임의 원칙**

필드 주입은 의존성 주입이 너무 쉬우므로 무분별한 의존성을 주입할 수도 있다. 그러면 하나의 클래스에서 지나치게 많은 기능을 하게 될 수 있는 여지가 있고, ‘객체는 그에 맞는 동작만 한다’라는 단일 책임의 원칙이 깨지기 쉽다.

생성자 주입을 사용하면 의존성을 주입해야 하는 대상이 많아질수록 생성자의 인자가 늘어난다. 이는 의존관계의 복잡성을 쉽게 파악할 수 있도록 도와주므로 리팩토링의 실마리를 제공한다.

**2. DI 컨테이너와의 낮은 결합도**

필드 주입을 사용하면 의존 클래스를 곧바로 인스턴스화 시킬 수 없다. 만약 IoC 컨테이너 밖의 환경에서 의존성을 주입받는 클래스의 객체를 참조할 때, `Reflection`을 사용하는 방법 외에는 참조할 방법이 없다. 생성자 또는 세터가 존재하지 않는다면 의존 객체 필드를 설정할 수 있는 방법이 없기 때문이다.

생성은 생성자로 의존성을 주입받기 때문에 IoC 컨테이너에 의존하지 않고 사용할 수 있고, 그 덕분에 테스트에서도 더 용이함을 보인다.

**3. 필드의 불변성 보장**

필드 주입은 객체를 생성하고 의존성을 `Reflection`으로 주입받기 때문에 필드 변수를 불변으로 선언할 수 없다.

그러나 생성자 주입은 필드를 `final`로 선언할 수 있다. 객체의 변경에 따른 비용을 절약할 수 있다.

**4. 순환 의존 방지**

생성자 주입에서는 순환 의존성을 가질 경우 `BeanCurrentlyInCreationException`이 발생해서 문제 상황을 알 수 있게 해준다.

## ✨ Spring DL(Dependency Lookup) : 의존관계 검색

```java
public Store() {
	AnnotationConfigApplicationContext context =
 		new AnnotationConfigApplicationContext(ItemFactory.class);

	this.connectionMaker = context.getBean("item", Item.class);
}
```

의존관계를 맺는 방법이 외부로부터의 주입이 아니라 스스로 검색을 이용하기 때문에 의존관계 검색(`dependency lookup`)이라고 불린다.

의존관계 검색은 자신이 필요로 하는 의존 오브젝트를 능동적으로 찾는다.

의존관계 검색은 런타임 시 의존관계를 맺을 오브젝트를 결정하는 것과 오브젝트의 생성 작업은 외부 컨테이너에게 `IoC`로 맡기지만, **이를 가져올 때는 메소드나 생성자를 통한 주입 대신 스스로 컨테이너에게 요청하는 방법을 사용한다.**

## ✨DI vs DL

`의존관계 주입` 쪽이 훨씬 단순하고 깔끔하다.

사용자에 대한 DB 정보를 어떻게 가져올 것인가에 집중해야 하는 UserDao에서 스프링이나 오브젝트 팩토리를 만들고 API를 이용하는 코드가 섞여 있는 것은 어색하다. 대개는 의존관계 주입 방식을 사용하는 편이 낫다.

`의존관계 검색` 방식에서는 검색하는 오브젝트는 자신이 스프링의 빈일 필요가 없으므로, 빈이 아닌 객체의 경우 의존관계 검색을 사용한다.

# 🚀 요약

`IoC` : 제어의 역전, 상위 모듈이 하위 모듈 흐름을 제어하는 GoF 디자인 원칙(할리우드 원칙)

`DIP` : 의존성 역전 원칙, 상위모듈이 하위 모듈의 구체 클래스가 아닌 인터페이스에 의존하는 SOLID 디자인 원칙

`DI` : 의존성 주입, 모듈(객체) 간 의존성을 new로 직접 정의하지 않고 외부에서 주입하는 디자인 패턴. 꼭 인터페이스를 통한 주입일 필요는 없다.

`Spring DI/IoC` : 스프링에서는  어플리케이션 컨텍스트라는 IoC/DI 컨테이너를 통해 bean으로 등록된 객체의 의존성을 자동으로 주입해준다.

이때 DI 방식에는 총 3가지 방법이 있다. 그중 생성자 주입 방식이 제일 권장된다. 의존관계 파악이 쉬워 단일책임원칙을 지키도록 돕고, DI 컨테이너 밖에서도 객체 주입이 가능하고, 필드 불변성이 보장되며, 순환 의존이 방지된다.

스프링에선 DI 대신 `DL`을 통해서도 IoC를 지킬 수 있도록 지원한다. `DL`은 객체 생명주기 관리를 IoC 컨테이너에서 하되, 의존성을 주입받는 대신 직접 컨테이너에서 검색해 가져오는 방법이다. 빈 바깥에서 빈 객체에 접근하고 싶을 때 사용한다.

## 참고자료

[Dependency Injection, IoC, DIP, IoC container정리](https://medium.com/sjk5766/dependency-injection-ioc-dip-ioc-container%EC%A0%95%EB%A6%AC-310885cca412)

[https://johngrib.github.io/wiki/hollywood-principle/#:~:text=객체지향 프레임워크와,때때로 헐리우드 원칙이라고도 합니다](https://johngrib.github.io/wiki/hollywood-principle/#:~:text=%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5%20%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC%EC%99%80,%EB%95%8C%EB%95%8C%EB%A1%9C%20%ED%97%90%EB%A6%AC%EC%9A%B0%EB%93%9C%20%EC%9B%90%EC%B9%99%EC%9D%B4%EB%9D%BC%EA%B3%A0%EB%8F%84%20%ED%95%A9%EB%8B%88%EB%8B%A4).

[Core Technologies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-introduction)

[https://black-jin0427.tistory.com/194](https://black-jin0427.tistory.com/194)

[Dependency Injection, IoC, DIP, IoC container정리](https://medium.com/sjk5766/dependency-injection-ioc-dip-ioc-container%EC%A0%95%EB%A6%AC-310885cca412)

[왜 Constructor Injection을 사용해야 하는가?](https://tecoble.techcourse.co.kr/post/2020-07-18-di-constuctor-injection/)
