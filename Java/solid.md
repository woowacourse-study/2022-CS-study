# SOLID

## 정의

SOLID라고 하는 좋은 객체 지향 프로그래밍을 작성하기 위한 5가지 원칙을 많이 들어보셨을거라 생각합니다. SOLID 원칙을 지켜서 설계를 하면, 시간이 지나도 변경이 용이하고, 유지보수와 확장이 쉬운 소프트웨어를 개발하는데 많은 도움이 됩니다.


## 단일 책임 원칙 (SPR)

단일 책임 원칙은 하나의 모듈은 한 가지의 책임을 가져야 한다는 것으로, 이것은 **모듈이 변경되는 이유가 한가지어야 한다는 의미**를 가집니다. 여기서 변경의 이유가 한가지라는 것은 해당 모듈이 여러 활동에 대해서 책임을 가지는 것이 아니라, 오직 하나의 활동에 대해서면 책임을 저야 한다는 것을 의미합니다.

만약 어떤 기능이 여러 활동에 대해 책임을 가지고 있다면 여러 활동들로부터 변경에 대한 요구가 올 수 있으므로, 해당 기능을 수정해야 하는 이유 역시 여러 개가 될 수 있다. 반면에 어떤 클래스가 단 하나의 책임 만을 갖고 있다면, 특정 활동로부터 변경을 특정할 수 있으므로 해당 클래스를 변경해야 하는 이유와 시점이 명확해지는 장점이 있습니다.


코드를 통해서도 한번 살펴보겠습니다.

``` java
@Service
@RequiredArgsConstructor
public class UserService {

	private final UserRepository userRepository;

	public void addUser(final String email, final String pw) {
		final StringBuilder sb = new StringBuilder();

		for(byte b : pw.getBytes(StandardCharsets.UTF_8)) {
			sb.append(Integer.toString((b & 0xff) + 0x100, 16).substring(1));
		}

		final String encryptedPassword = sb.toString();
		final User user = User.builder()
				.email(email)
				.pw(encryptedPassword).build();

		userRepository.save(user);
	}
}
```

해당 코드는 다양한 변화 요소가 발생할 수 있는 요소들을 가지고 있습니다.

* 사용자에 대해서 다른 역할들이 추가된다.
* 사용자의 비밀번호 암호화 방식이 다른 방식으로 변경된다.
* 추가적인 기능


이런 문제들은 앞에서 언급했듯이 하나의 기능에서 여러 활동들을 가지고 있기 때문입니다. 결국 문제를 해결하기 위해서는 책임을 분리하도록 구현해야 합니다. 여기서는 비밀번호를 암호화 하는 기능을 분리해서 SPR 원칙에 맞도록 구현할 수 있습니다.

``` java
@Component
public class SimplePasswordEncoder {

	public void encryptPassword(final String pw) {
		final StringBuilder sb = new StringBuilder();

		for(byte b : pw.getBytes(StandardCharsets.UTF_8)) {
			sb.append(Integer.toString((b & 0xff) + 0x100, 16).substring(1));
		}

		return sb.toString();
	}
}

@Service
@RequiredArgsConstructor
public class UserService {

	private final UserRepository userRepository;
	private final SimplePasswordEncoder passwordEncoder;

	public void addUser(final String email, final String pw) {
		final String encryptedPassword = passwordEncoder.encryptPassword(pw);

		final User user = User.builder()
				.email(email)
				.pw(encryptedPassword).build();

		userRepository.save(user);
	}
}

```


정리하자면, 단일 책임 원칙은 기능마다 하나의 역할만 가지도록 하는 것을 말합니다. 이로 인해, 변경이 필요할 때 수정할 대상이 명확해지도록 하는 효과를 가질 수가 있어 애플리케이션 변화시 다른 코드에 영향을 주지 않도록 하는 효과를 가질 수 있습니다.

<br>

## 개방 폐쇄 원칙 (OCP)

개방 폐쇄 원칙은 **변화에는 닫혀있고, 확장에는 열려있어야 한다는 원칙**을 말합니다.

여기서 말하는 **확장에 열려있다**는 말은 **요구사항 변경 시, 새로운 기능을 추가하여서 애플리케이션 기능을 확장**하는 것을 말하며, **변화에 닫혀있다**는 말은 **기존의 코드를 수정하지 않고, 애플리케이션 동작을 추가해야 한다는 것**을 의미합니다.

코드를 통해서도 한번 살펴보겠습니다.

``` java
@Component
public class SHA256PasswordEncoder {

	private final static String SHA_256 = "SHA-256";

	public String encryptPassword(final String pw)  {
		final MessageDigest digest;

		// 중간 로직 생략...
		
		return hexString.toString();
	}
}

@Service
@RequiredArgsConstructor
public class UserService {

	private final UserRepository userRepository;
	private final SHA256PasswordEncoder passwordEncoder;
	
	// 중간 로직 생략...
}
```

현재 SHA256 방식으로 수행하는 비밀번호 암호화 방식을 SHA512로 변경한다면 반드시 UserService의 코드를 수정해야만 합니다. 이는 `OCP` 원칙에 대해서 정면으로 반박하는 내용이라고 할 수 있습니다.

이러한 문제를 우리는 추상화 의존으로 해결할 수 있습니다. 추상화를 통해서 변하지 않는 부분만 고정적으로 제공하고, 변화는 부분을 외부에서 구현함으로써 `OCP` 원칙을 지키도록 설계를 할 수 있습니다. 이를 예시에 대한 내용으로 변경한다면, 변하지 않는 부분인 암호화를 제공하는 부분은 추상화를 하고, 변하는 부분인 구체적으로 어떤 암호화 방식으로 제공할지는 외부에서 제공하도록 설계한다고 할 수 있습니다.

``` java
public interface PasswordEncoder {
    String encryptPassword(final String pw);
}

@Component
public class SHA256PasswordEncoder implements PasswordEncoder {

	@Override
	public String encryptPassword(final String pw)  {
      ...
	}
}

@Service
@RequiredArgsConstructor
public class UserService {

	private final UserRepository userRepository;
	private final PasswordEncoder passwordEncoder;

	public void addUser(final String email, final String pw) {
		final String encryptedPassword = passwordEncoder.encryptPassword(pw);

		final User user = User.builder()
				.email(email)
				.pw(encryptedPassword).build();

		userRepository.save(user);
	}
    
}
```

정리하자면, OCP를 지키기 위해서는 추상화와 다형성을 사용해야 한다고 할 수 있습니다. 이를 좀 더 구체적으로 말하면, 컴파일 시점에는 추상화된 인터페이스에 의존하고 있지만, 런타임 시점에는 구체 클래스에 의존되도록 설계해야 한다고 할 수 있습니다. 이로 인해, 기존의 코드 및 클래스들을 수정하지 않은 채로 애플리케이션을 확장할 수 있습니다.

<br>

## 인터페이스 분리 원칙 (ISP)

SRP 원칙을 통해서 객체가 충분히 높은 응집도로 설계되었다고 하더라도, 목적과 관심이 각기 다른 클라이언트가 있다면 이를 적절하기 분리해야 하는데, 이를 `인터페이스 분리 원칙` 이라고 합니다. 만약, 파일 읽기/쓰기 기능을 제공하는 구현체가 있는데 어떤 클라이언트는 읽기 작업만을 제공받고 싶어한다면, 별도의 읽기 작업만을 제공하는 인터페이스를 제공해야 하는것이 `ISP`라고 할 수 있습니다.


역시 코드를 통해서 한번 살펴보겠습니다.

``` java 
public interface PasswordEncoder {
    String encryptPassword(final String pw);
}

@Component
public class SHA256PasswordEncoder implements PasswordEncoder {

	@Override
	public String encryptPassword(final String pw)  {
		...
	}

	@Override
	public String isCorrectPassword(final String rawPw, final String pw) {
		final String encryptedPw = encryptPassword(rawPw);
		return encryptedPw.equals(pw);
	}
}

@Component
public class SHA512PasswordEncoder implements PasswordEncoder {

	@Override
	public String encryptPassword(final String pw)  {
		...
	}

	@Override
	public String isCorrectPassword(final String rawPw, final String pw) {
		throw new IllegalArgumentException("지원하지 않는 기능입니다.")
	}
}

```


SHA256 라는 객체는 `encryptPassword`와 `isCorrectPassword`라는 기능을 제공하고 있습니다. 여기서 시간이 지나서 SHA512로 암호화하는 인터페이스가 업그레이드 되었다고 가정하겠습니다. SHA512는 256에서 제공하고 있는 `isCorrectPassword` 기능을 보안상의 이유로 제공하지 않는다고 합니다. 결국 위의 코드처럼 지원하지 않는 기능이라고 예외를 던져주어야 합니다.



``` java
public interface PasswordEncoder {
    String encryptPassword(final String pw);
}

public interface PasswordChecker {
    String sCorrectPassword(final String rawPw, final String pw);
}


public class SHA256PasswordEncoder implements PasswordEncoder, PasswordChecker {
	@Override
	public String encryptPassword(final String pw)  {
		...
	}

	@Override
	public String isCorrectPassword(final String rawPw, final String pw) {
		final String encryptedPw = encryptPassword(rawPw);
		return encryptedPw.equals(pw);
	}
}

public class SHA512PasswordEncoder implements PasswordEncoder {
	@Override
	public String encryptPassword(final String pw)  {
		...
	}
}
```


이러한 문제들을 ISP 원칙을 사용한다면 해결할 수 있습니다. 각각의 기능들은 인터페이스로 분리하고, 구현체에서 각 인터페이스들을 가져와서 구현을 진행한다면, 필요한 기능만 구현을 할 수 있게 됩니다.



<br>


## 리스코프 치환 원칙

리스코프 치환 원칙은 각 프로그램의 객체들이 프로그램의 정확성을 깨뜨리지 않으면서, 하위 타입의 인스턴스로 바꿀 수 있어야 한다는 점을 말합니다. 이때까지 앞의 3가지 원칙은 코드에 기준으로 하는 설계관점에서의 원칙들이지만, 리스코프 치환 원칙은 논리적인 기준에 조금 더 초점을 맞춘다고 할 수 있습니다.

앞의 예제처럼 비밀번호를 암호화하는 인터페이스와 구현체가 있다고 가정하겠습니다. 해당 인터페이스는 다음과 같은 규약을 정의합니다.

``` java
public interface PasswordEncoder {
    String encryptPassword(final String pw);
}
```

즉, 비밀번호를 암호화 한다는 규악을 인터페이스로 정의했다고 할 수 있습니다. 그런데 만약 이를 구현하는 **하위 타입에서 비밀번호를 암호화 하지 않고 비밀번호를 축약하는 알고리즘을 구현**하면 어떻게 될까요?? 실제 코드는 컴파일 되겠지만, 논리적으로는 구현이 되지 않았다고 할 수 있습니다.

정리자하면, **다형성에서 하위 클래슨느 인터페이스의 규약을 반드시 지켜**야 다른 구현체들 역시 믿고 사용할 수 있다는 것이 리스코프 치환 원칙이라고 할 수 있습니다.

<br>

## 의존관계 역전 원칙

의존 역전 원칙이란 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안 되며, 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야 한다는 것을 말합니다. 여기서 고수준 모듈과 저수준 모듈은 다음을 의미합니다.

* 고수준 모듈 : 변경이 없는 추상화된 인터페이스(추상 클래스)
* 저수준 모듈 : 변하기 쉬운 구체 클래스

의존 역전 원칙이란 결국 추상화에 의존하며 구체화에는 의존하지 않는 설계 원칙을 의미합니다. SRP에서 살펴보았던 `SimplePasswordEncoder`는 변하기 쉬운 구체 클래스인데, `UserService`가 `SimplePasswordEncoder`에 직접 의존하는 것은 **의존성 역전 원칙**에 위배된다고 할 수 있습니다.

그러므로 `UserService`가 변하지 않는 추상화에 의존하도록 설계하기 위해 `PasswordEncoder` 라는 인터페이스를 만들어 이에 의존하도록 변경하였습니다. 이로인해, `UserService`가 추상화된 `PasswordEncoder`에 의존하므로 비밀번호 암호화 정책이 변경되어도 다른 곳들로 변경이 전파되지 않으며 유연한 애플리케이션이 된다는 것을 확인할 수 있습니다.


<img width="647" alt="Pasted image 20220905011847" src="https://user-images.githubusercontent.com/48710213/188359773-76f12c40-7700-4e1a-9a9f-0b075a303404.png">


이렇게 의존관계 역전 원칙을 살펴보면, 개방 폐쇄 원칙과 밀접한 관련이 있다는 것을 확인할 수 있습니다. 의존 역전 원칙이 위배되면 개방 폐쇄 원칙 역시 위배될 가능성이 매우 높습니다.

추가적으로 의존 역전 원칙에서 의존성이 역전되는 시점은 컴파일 시점이라는 것을 인지해야 합니다. 런타임 시점에는 `UserService`가 구체 클래스에 의존하게 됩니다. 즉, 의존 역전 원칙은 컴파일 시점 또는 소스 코드 단계에서의 의존성이 역전되는 것을 의미하며, 코드에서는 `UserService`가 `PasswordEncoder`라는 인터페이스에 의존한다는 것을 확인할 수 있습니다.

<br>

> 참고한 자료

* https://mangkyu.tistory.com/194
* https://blog.itcode.dev/posts/2021/08/16/interface-segregation-principle
