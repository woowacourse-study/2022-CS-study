
# 불변과 final/finalize/finally  

## 🔥 불변과 final    

- 불변 객체란  
  - 최초 생성 후 내부 상태값이 변하지 않는 객체  
  - 객체가 살아있는 동안 `public API`가 **항상 똑같이 동작한다는 것을 보장** 
  - `String`도 불변 객체다  

  ```java
  String yellowCat = "yellow-cat";
  String blackCat = yellowCat.replace("yellow", "black");

  assertThat("yellow-cat").isEqualTo(yellowCat);
  assertThat("black-cat").isEqualTo(blackCat);
  ```

- 자바의 `final`  
  - 클래스 선언 시 `final`은 상속을 막음  
  - 메서드 선언 시 `final`은 `override`를 막음  
  - 변수 선언 시 `final`은...
    - 자바의 변수는 기본적으로 가변 == 값을 바꿀 수 있다  
    - `final` 키워드로 재할당을 막을 수 있다  
    - 하지만 객체 내부 상태 변경을 막지는 못한다  


    ```java
    final List<String> names = new ArrayList<>();
    assertThat(names.size()).isEqualTo(0);
    names.add("summer");
    aseertThat(names.size()).isEqualTo(1); // size가 변했다 
    ```

    - `size()`는 `public API`이다  
    - 위 코드에서 항상 똑같이 동작함을 보장하지 못하고 있다  
  
- 자바의 불변  
  - 변수를 `final`로 선언하고, 값을 변경하는 API(ex. setter)를 만들지 않는다  
  - 단, 필드에 `객체`가 있을 경우 해당 객체의 API에 의존하게 된다  

  ```java
  class Team {
    private final String name; // 자체로 불변 
    private final Members members; // Members 객체의 API에 불변 여부를 의존  
  }
  ```

- 외전 : `static final`로 바이트 코드를 최적화 할 수 있다  

```java
static final boolean doX = false;
static final boolean doY = true;
```

```java
Console console = System.console();
if (doX) {
    console.writer().println("x");
} else if (doY) {
    console.writer().println("y");
}
```

- 아래 코드를 컴파일하면
  - `static final`일 시 39줄의 바이트코드가, 
  - 그냥 `static`일 시 76줄의 바이트코드가 생성  
- 컴파일 시 참조가 아닌 *실제 값*으로 대치하기 때문  
- 하지만 이를 의식하고 코드를 짤 일은 딱히 없을 것 같다...


<br>

## 🗑 finalize()  

- `Object`에 정의된 메서드  
- `가비지 콜렉터`에 의해, 객체가 `gc` 대상이 되었을 때 한 번 호출  
  - 원하는 코드를 `@Override`할 수 있다  
    - ex. 다른 스레드에서 다시 참조하도록  
  - 보통 명시적으로 `I/O 커넥션 해제하기` 처럼, 객체가 사라지기 이전에 해야 할 일을 작성  

- **BUT!!**
- Effective Java Item 7. `finalizer`와 `cleaner` 사용을 피하라  
  - 객체에 접근할 수 없게 된 후(=gc 대상이 된 후) 실제 실행 시점까지 얼마나 걸릴 지 알 수 없음  
  - 따라서, 제 때 실행되어야 하는 작업은 **절대** 할 수 없음   
  - 심지어 성능도 나쁨  
    - `AutoCloseable`객체 기준 `try-with-resources`보다 50배 느림  
    - 가비지 컬렉터의 효율을 떨어트리기 때문  
  - `finalize()`는 자바9에 `deprecated` 됨   
  - 대안으로 나온 것이 `cleaner`지만, 역시 좋지 않음  

- 흥미로웠던 사실 : `Enum`에는 `finalize()` 구현이 막혀있음 (유추해보면 당연할지도)

<br>

## ✂️ finally

- `try catch`문에서 무조건 실행되는 블럭 😅  
- 대체 가능하다면 `try-with-resources` 씁시다  

<br>

## 도움받은 곳들  

- [Java의 정석](https://search.shopping.naver.com/book/catalog/32466681076?cat_id=50010920&frm=PBOKPRO&query=java%EC%9D%98+%EC%A0%95%EC%84%9D&NaPm=ct%3Dl8ijh07k%7Cci%3Da6f47a2dc48ab055e282606858cb412c186975a5%7Ctr%3Dboknx%7Csn%3D95694%7Chk%3Dd5926613781aa25a5ca4812425ae8a63ed34e932)  
- [이펙티브 자바](https://search.shopping.naver.com/book/catalog/32436239326?query=%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C%20%EC%9E%90%EB%B0%94&NaPm=ct%3Dl8iji18w%7Cci%3D4ffcec3b0d7488a3d6724af5e017a45e16e9f8b9%7Ctr%3Dboksl%7Csn%3D95694%7Chk%3De8d247c19b5c22fadf52945598b1a6a0de17969f)  
- [Baeldung - java final performance](https://www.baeldung.com/java-final-performance)  
