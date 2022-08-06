```java
//게시판의 게시글을 삭제하는 메서드 
@Transactional
public void removeBoard(Long id)throws Exception{
    replyDAO.removeAll(id); //삭제할 게시글의 답글 삭제
    boardDAO.deleteBoard(id); //게시글 삭제 
}
```

# 트랜잭션?

## 1. 정의

> 데이터베이스 관리 시스템 또는 유사한 시스템에서 상호작용의 단위이다. 여기서 유사한 시스템이란 트랜잭션이 성공과 실패가 분명하고 상호 독립적이며, 일관되고 믿을 수 있는 시스템을 의미한다.
>

## 2. ACID

> 이론적으로 데이터베이스 시스템은 각각의 트랜잭션에 대해 원자성(`Atomicity`), 일관성(`Consistency`), 독립성(`Isolation`), 영구성(`Durability`)을 보장한다. 이 성질을 첫글자를 따 [ACID](https://ko.wikipedia.org/wiki/ACID)라 부른다.
>

- [`원자성(Atomicity)`](https://ko.wikipedia.org/wiki/%EC%9B%90%EC%9E%90%EC%84%B1_(%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4_%EC%8B%9C%EC%8A%A4%ED%85%9C)) :
  트랜잭션과 관련된 작업들이 부분적으로 실행되다가 중단되지 않는 것을 보장하는 능력이다. 중간 단계까지 실행되고 실패하는 일이 없도록 하는 것이다.
- `일관성(Consistency)`: 트랜잭션이 실행을 성공적으로 완료하면 언제나 일관성 있는 데이터베이스 상태로 유지하는 것을 의미한다.
- `독립성(Isolation)`: 트랜잭션을 수행 시 다른 트랜잭션의 연산 작업이 끼어들지 못하도록 보장하는 것을 의미한다. 이것은 트랜잭션 밖에 있는 어떤 연산도 중간 단계의 데이터를 볼 수 없음을 의미한다.
- `지속성(Durability)` : 성공적으로 수행된 트랜잭션은 영원히 반영되어야 함을 의미한다. 트랜잭션은 로그에 모든 것이 저장된 후에만 commit 상태로 간주될 수 있다.

[ACID - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/ACID)

## 3. 트랜잭션 과정

1. 트랜잭션 시작
2. 비즈니스 로직 실행(여러 쿼리들이 실행) (DB내 갱신이 아직 적용되지 않는다)
3. 트랜잭션 커밋 (트랜잭션이 성공적이며, 갱신이 실제 적용됨)

만약 쿼리 하나가 실패하면, 데이터베이스 시스템은 전체 트랜잭션 또는 실패한 쿼리를 롤백한다.

## 4. **트랜잭셔널 데이터베이스**

트랜잭션을 지원하는 데이터베이스를 트랜잭셔널 데이터베이스(`transactional database`)라고 부른다.

현재 대부분의 관계형 데이터베이스 관리시스템은 트랜잭션 데이터베이스이다.

[데이터베이스 트랜잭션 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4_%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98)

## 5. 요약

특정 실행단위에서 오류 발생시 전체 실행 내용을 `롤백`해주는 기능

# 순수 JDBCTemplete

```java
Connection connection=dataSource.getConnection()
    try(connection){
        connection.setAutoCommit(false);
    // 비즈니스 로직 수행
        connection.commit();
    }catch(SQLException e){
        connection.rollback();
    }finally{
        connection.close()
}
```

## 1. 개념

1. `setAutoCommit(false)` 를 통해 트랜잭션을 직접 관리할 수 있게 되었다.
2. 이에 따라 기능들이 수행된 뒤  `커밋`을 하고, 예외 발생시 `롤백`을 하고 있다.

## 2. 문제점

1. `Connection` 오브젝트를 한번 생성 후 계속 메소드의 파라미터로 전달하다가 `DAO`를 호출할 때 사용한다
    1. → Spring 트랜잭션 동기화로 해결!
2. 데이터 접근 기술에 의존적인 코드
    1. → Spring 트랜잭션 추상화로 해결!
3. 순수 비즈니스 로직과는 다른 관심사의 일(DB 접근 관련)을 수행한다. 코드가 깔끔하지 않다.
    1. → Spring 선언적 트랜잭션로 해결!

# Spring Transaction

## 1. 트랜잭션 동기화

### 1-1. 개념

1. `Connection` 오브젝트를 `특별한 저장소`에 보관해두고,
2. 이후에 `DAO`의 메소드에서 `트랜잭션`이 필요할 때
3. 저장된 `Connection`을 가져다가 사용한다.

### 1-2. `TransactionSynchronizationManager`

트랜잭션 동기화 저장소

- 작업 `스레드`마다 독립적으로 `Connection` 오브젝트를 저장하고 관리
- → 다중 사용자를 처리하는 서버의 `멀티스레드` 환경에서도 `충돌 x`

이제 여러 쿼리를 한 로컬 트랜잭션에 관리 가능하다.

파라미터도 제거할 수 있다.

```java
private DeataSource dataSource;

// Connection을 생성할 때 사용할 DataSource를 DI 받는다
public void setDataSource(DataSource dataSource){
    this.dataSource=dataSource;
}

public void method()throws Exception{
    // 1. 동기화 작업 초기화
    TransactionSynchronizationManager.initSynchronization();
    // 2. DB 커넥션을 생성하고 트랜잭션을 시작
    Connection c=DataSourceUtils.getConnection(dataSource);
    c.setAuthCommit(false);

    try{
        // 3. 비즈니스 로직 수행
        c.commit();
    }catch(Exception e){
        c.rollback();
        throw e;
    }finally{
        DataSourceUtils.releaseConnection(c,dataSource);
    // 4. 동기화 작업 종료 및 정리
        TransactionSynchronizationManager.unbindResource(this.dataSource);
        TransactionSynchronizationManager.clearSynchronization();
    }
}
```

### 1-3. 문제점

`DataSourceUtils` 을 사용해 `Connection` 오브젝트를 가져온다. 즉, 아직 `Data 접근 기술`에 의존적이다.

## 2. 트랜잭션 추상화

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ee7b626f-d027-4343-a3f0-d4e8ddd4e77e/Untitled.png)

### 2-1. 개념

스프링은 `Data 접근 기술`에 `독립적인` 트랜잭션을 사용 할 수 있다.

기술 별로 가져오는 트랜잭션 형태만 다를 뿐, 트랜잭션 기능은 같기 때문에 추상화가 가능하다.

### 2-2. `PlatformTransactionManager`

`JPA`, `JDBC` 등 다양한 기술의 트랜잭션을 추상화

- 기술에 독립적이다
- `트랜잭션 경계설정`이 가능해졌다

```java
public void method()throws Exception{
    // 1.사용할 DB(JDBC)의 DataSource를 생성자에 넣어 트랜잭션 추상 오브젝트 생성
    PlatformTransactionManager transactionManager=new DataSourceTransactionManager(dataSource);
    TransactionStatus status=transactionManager.getTransaction(new DefaultTransactionDefinition());  //트랜잭션 시작 

    try{
        // 2. 비즈니스 로직 수행
        transactionManager.commit(status);
    }catch(Exception e){
        transactionManager.rollback(status);
        throw e;
    }
}
```

### 2-3. `JDBC` 대신 `JPA`를 이용하는 법

```java
PlatformTransactionManager transactionManager=new DataSourceTransactionManager(dataSource);
```

에서

```java
PlatformTransactionManager transactionManager=new JpaTransactionManager();
```

이렇게 바꿔주기만 하면 된다.

### 2-4. 문제점

여전히 순수 비즈니스 로직과는 다른 관심사의 일(DB 접근 관련)을 수행한다. 코드가 깔끔하지 않다.

## 3. 선언적 트랜잭션 (`@Transactional`)

### 3-1. 개념

소스코드에 직접 트랜잭션 관련 로직을 넣어두지 않고 비지니스 로직에서 완전히 분리하는 방식이다.

이 방식을 사용하면 프로그래밍에 의한 트랜잭션에서 나온 단점인 트랜잭션 코드 중복 문제, 소스코드 유지보수의 문제를 모두 해결할 수 있다.

트랜잭션이라는 횡단 관심사를 비지니스 로직에서 완전히 분리하기 때문에 SRP 관점에서 봤을때도 적합하고 많은 양의 트랜잭션 로직을 적용하기에도 합리적이다.

### 3-2. 구현방식

`Spring`에서는 `AOP`를 사용해서 `선언적 트랜잭션`을 구현하고 있다.

1. `AbstractPlatformTransactionManager`에서 팩토리 메서드 패턴을 사용해서 여러 `PlatformTransactionManager`을 확장한다.
2. `TransactionInterceptor` 를 통해 트랜잭션 경계를 설정하고 여기에서 주입된 `PlatformTransactionManager` 를 사용한다.
3. `SpringTransactionAnnotationParser` 를 통해 `@Transactional` 관련 속성을 파싱한다.
4. `AbstractAutoProxyCreator` 에 의해 `Proxy`로 생성되고 실제 클라이언트가 타깃에 접근할때는 `Proxy`를 거쳐 `TransactionInterceptor` 를 사용해 트랜잭션을 열고
   타깃의 메서드를 호출하고 커밋, 롤백을 수행한다….

중요한건, 이제 `어노테이션`으로 트랜잭션 관리가 가능해졌다는 것이다!!

# 4. `@Transactional`

- 클래스 주석 문서 번역

    ```java
    /*
    개별 메서드 또는 클래스의 트랜잭션 특성을 설명합니다.
    
    이 주석이 클래스 수준에서 선언되면 선언하는 클래스 및 해당 하위 클래스의 모든 메서드에 기본값으로 적용됩니다. 클래스 계층의 상위 클래스에는 적용되지 않습니다. 상속된 메서드는 하위 클래스 수준 주석에 참여하기 위해 로컬에서 다시 선언해야 합니다. 메소드 가시성 제약에 대한 자세한 내용은 참조 매뉴얼의 트랜잭션 관리 섹션을 참조하십시오.
    
    이 주석은 일반적으로 Spring의 org.springframework.transaction.interceptor.RuleBasedTransactionAttribute 클래스와 직접 비교할 수 있으며 실제로 AnnotationTransactionAttributeSource는 이 주석의 속성을 RuleBasedTransactionAttribute의 속성으로 직접 변환하므로 Spring의 트랜잭션 지원 코드는 주석에 대해 알 필요가 없습니다.
    
    Attribute Semantics
    
    이 주석에 사용자 지정 롤백 규칙이 구성되지 않은 경우 트랜잭션은 RuntimeException 및 Error 시 롤백되지만 확인된 예외에서는 롤백되지 않습니다.
    
    롤백 규칙은 지정된 예외가 throw될 때 트랜잭션을 롤백해야 하는지 여부를 결정하며 규칙은 패턴을 기반으로 합니다. 패턴은 현재 와일드카드 지원이 없는 정규화된 클래스 이름 또는 예외 유형(Throwable의 하위 클래스여야 함)에 대한 정규화된 클래스 이름의 하위 문자열일 수 있습니다. 예를 들어 "javax.servlet.ServletException" 또는 "ServletException" 값은 javax.servlet.ServletException 및 해당 하위 클래스와 일치합니다.
    
    롤백 규칙은 각각 패턴을 클래스 참조 또는 문자열로 지정할 수 있도록 하는 rollbackFor/noRollbackFor 및 rollbackForClassName/noRollbackForClassName을 통해 구성할 수 있습니다. 예외 유형이 클래스 참조로 지정되면 정규화된 이름이 패턴으로 사용됩니다. 결과적으로 @Transactional(rollbackFor = example.CustomException.class)은 @Transactional(rollbackForClassName = "example.CustomException")과 동일합니다.
    
    경고: 패턴이 얼마나 구체적인지, 패키지 정보를 포함할지 여부를 신중하게 고려해야 합니다
    (필수 사항은 아님). 
    예를 들어 "예외"는 거의 모든 항목과 일치하며 다른 규칙을 숨길 수 있습니다. "Exception"이 검사된 모든 예외에 대한 규칙을 정의하려는 경우 "java.lang.Exception"이 정확합니다. "BaseBusinessException"과 같은 보다 고유한 예외 이름을 사용하면 예외 패턴에 대해 완전한 클래스 이름을 사용할 필요가 없을 것입니다. 
    또한 롤백 규칙으로 인해 유사한 이름의 예외 및 중첩 클래스에 대해 의도하지 않은 일치가 발생할 수 있습니다. 이는 throw된 예외의 이름에 롤백 규칙에 대해 구성된 예외 패턴이 포함된 경우 throw된 예외가 지정된 롤백 규칙과 일치하는 것으로 간주되기 때문입니다. 
    예를 들어, com.example.CustomException에서 일치하도록 구성된 규칙이 주어지면 해당 규칙은 com.example.CustomExceptionV2라는 예외(CustomException과 동일한 패키지에 있지만 추가 접미사가 있는 예외) 또는 com이라는 예외와 일치합니다. example.CustomException$AnotherException(CustomException에서 중첩 클래스로 선언된 예외).
    
    이 주석에 있는 다른 속성의 의미에 대한 특정 정보는 TransactionDefinition 및 org.springframework.transaction.interceptor.TransactionAttribute javadoc을 참조하십시오.
    
    **트랜잭션 관리**
    이 주석은 일반적으로 org.springframework.transaction.PlatformTransactionManager에 의해 관리되는 스레드 바운드 트랜잭션과 함께 작동하여 현재 실행 스레드 내의 모든 데이터 액세스 작업에 트랜잭션을 노출합니다. 참고: 이것은 메서드 내에서 새로 시작된 스레드로 전파되지 않습니다.
    또는 이 주석은 스레드 로컬 변수 대신 Reactor 컨텍스트를 사용하는 org.springframework.transaction.ReactiveTransactionManager에 의해 관리되는 반응 트랜잭션을 구분할 수 있습니다. 결과적으로 모든 참여 데이터 액세스 작업은 동일한 리액티브 파이프라인의 동일한 Reactor 컨텍스트 내에서 실행되어야 합니다.
    
    Since:1.2
    See Also :org.springframework.transaction.interceptor.TransactionAttribute, org.springframework.transaction.interceptor.DefaultTransactionAttribute, org.springframework.transaction.interceptor.RuleBasedTransactionAttribute
    작가:콜린 삼팔레아누, 유르겐 홀러, 샘 브래넌, 마크 팔루치
    */
    
    ```

```java

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {

    // 사용할 트랜잭션 관리자
    @AliasFor("transactionManager")
    String value() default "";

    @AliasFor("value")
    String transactionManager() default "";

    String[] label() default {};

    // 선택적 전파 설정
    Propagation propagation() default Propagation.REQUIRED;

    // 선택적 격리 수준
    Isolation isolation() default Isolation.DEFAULT;

    // 트랜잭션 타임 아웃
    int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;

    String timeoutString() default "";

    // 읽기/쓰기 vs 읽기 전용 트랜잭션
    boolean readOnly() default false;

    // 롤백이 수행되어야 하는, 선택적인 예외 클래스의 배열
    Class<? extends Throwable>[] rollbackFor() default {};

    // 롤백이 수행되어야 하는, 선택적인 예외 클래스 이름의 배열
    String[] rollbackForClassName() default {};

    // 롤백이 수행되지 않아야 하는, 선택적인 예외 클래스의 배열
    Class<? extends Throwable>[] noRollbackFor() default {};

    // 롤백이 수행되지 않아야 하는, 선택적인 예외 클래스 이름의 배열
    String[] noRollbackForClassName() default {};

}
```

## 4-1. **트랜잭션의 격리 수준 (`Isolation Level`)**

1.  `DEFAULT` :데이터 베이스에서 설정된 기본 격리 수준을 따릅니다.

2. `READ_UNCOMMITED`: 트랜잭션이 아직 커밋되지 않은 데이터를 읽을 수 있습니다.

3. `READ_COMMITED`: Dirty Read 를 방지하기 위해 Commit 된 데이터만 읽을 수 있습니다.

4. `REPEATABLE READ`: 트랜잭션이 완료될 때까지 조회한 모든 데이터에 shared lock이 걸리므로 트랜잭션이 종료될 때까지 다른 트랜잭션은 그 영역에 해당하는 데이터를 수정할 수 없습니다.

5. `SERIALIZABLE`: 가장 엄격한 트랜잭션 격리수준으로, 완벽한 읽기 일관성 모드를 제공합니다. 이 격리 수준에서는 PHANTOM READ 상태가 발생하지 않지만 동시성 처리 성능이 급격히 떨어질 수
   있습니다.

## 4-2. **트랜잭션 전파 옵션 (`Propagation`)**

1. `REQUIRED` : 이미 시작된 트랜잭션이 있으면 참여하고, 없으면 새로운 트랜잭션을 시작합니다. (디폴트 속성)
2. `SUPPORTS` : 이미 시작된 트랜잭션이 있으면 참여하고, 없으면 트랜잭션 없이 처리합니다.
3. `REQUIRED_NEW` : 항상 새로운 트랜잭션을 시작합니다. 이미 진행중인 트랜잭션이 있다면 잠시 보류시킵니다.
4. `MANDATORY` : 이미 시작된 트랜잭션이 있으면 참여하고, 없으면 새로운 트랜색션을 시작하는 대신 예외를 발생시킵니다. 혼자서는 독립적으로 수행되면 안되는 경우에 사용됩니다.
5. `NOT_SUPPORTED` : 트랜잭션을 사용하지 않고 처리하도록 합니다. 이미 진행중인 트랜잭션이 있다면 잠시 보류시킵니다.
6. `NEVER` : 트랜잭션을 사용하지 않도록 강제시킵니다. 이미 진행중인 트랜잭션 또한 허용하지 않으며, 있다면 예외를 발생시킵니다.
7. `NESTED` : 이미 실행중인 트랜잭션이 있다면 중첩하여 트랜잭션을 진행합니다. 부모 트랜잭션은 중첩 트랜잭션에 영향을 주지만 중첩 트랜잭션은 부모 트랜잭션에 영향을 주지 않습니다.

## 4-3. **`readOnly` 옵션**

- `readOnly` 속성을 통해 트랜잭션을 읽기 전용으로 설정할 수 있다.
- `JPA`의 경우, 해당 옵션을 `true` 로 설정하게 되면 트랜잭션이 커밋되어도 영속성 컨텍스트를 플러시하지 않는다. 플러시할 때 수행되는 엔티티의 스냅샷 비교 로직이 수행되지 않으므로 성능을 향상 시킬 수
  있다.

# 참고자료

[https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html](https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html)

[https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative](https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative)

[https://jongmin92.github.io/2018/04/08/Spring/toby-5/#:~:text=트랜잭션 동기화란 UserService에서,가 사용하게 하는 것이다](https://jongmin92.github.io/2018/04/08/Spring/toby-5/#:~:text=%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%20%EB%8F%99%EA%B8%B0%ED%99%94%EB%9E%80%20UserService%EC%97%90%EC%84%9C,%EA%B0%80%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B2%8C%20%ED%95%98%EB%8A%94%20%EA%B2%83%EC%9D%B4%EB%8B%A4)
.

[https://programmer-study.tistory.com/19](https://programmer-study.tistory.com/19)

[https://sup2is.github.io/2021/11/11/about-spring-transaction.html](https://sup2is.github.io/2021/11/11/about-spring-transaction.html)

# 예상 질문/답변

### 1. **선언전 트랜잭션 방식을 사용하는 이유는?**

비즈니스 로직이 트랜잭션 처리를 필요로 할 때, 트랜잭션 처리 코드와 비즈니스 로직이 공존한다면 코드 중복이 발생하고 비즈니스 로직에 집중하기 어렵다. 따라서 트랜잭션 처리와 비즈니스 로직을 분리할 수 있는 선언적
트랜잭션 방식을 자주 사용한다.

### 2. **`@Transactional`의 동작 원리는?**

`@Transactional`을 메소드 또는 클래스에 명시하면

`AOP`를 통해

`Target`이 상속하고 있는 인터페이스 또는 `Target` 객체를 상속한 `Proxy` 객체가 생성되며,

`Proxy` 객체의 메소드를 호출하면

`Target` 메소드 전 후로 `트랜잭션` 처리를 수행한다.

### 3. **`@Transactional`을 사용할 때 주의할 점은?**

```java
could not initialize proxy[com.wooteco.sokdak.hashtag.domain.Hashtag#3]-no Session
        org.hibernate.LazyInitializationException:could not initialize proxy[com.wooteco.sokdak.hashtag.domain.Hashtag#3]-no Session
        ...
```

`Proxy` 객체의 `Target Method`가 내부 메소드를 호출하면 실제 메소드가 호출되기 때문에

`Inner Method`에서 `@Transactional` 어노테이션이 적용되지 않는 것을 주의해야 한다.

`@Transactional` 어노테이션을 붙이면 트랜잭션 처리를 위해 `Proxy` 객체를 생성하는데,

`Proxy`는 `Target Class`를 상속하여 생성된다.

따라서 상속이 불가능한 `Private 메소드`의 경우 `@Transactional` 어노테이션을 적용할 수 없다.

- 관련 트러블슈팅

  **ServiceTest**에서 **@Transactional** 어노테이션을 빼니 **LazyInitializationException** 에러가 터졌습니다.

    ```
    could not initialize proxy [com.wooteco.sokdak.hashtag.domain.Hashtag#3] - no Session
    org.hibernate.LazyInitializationException: could not initialize proxy [com.wooteco.sokdak.hashtag.domain.Hashtag#3] - no Session
    	...
    
    ```

  원인은 **postHashtag** 연결 엔티티에서 **Hashtag**, **Post**를 저장할 때

  **Lazy** 로딩으로 가져온다는 데에 있었습니다.

  서비스 메소드를 호출하고, 트랜잭션이 닫힌 후, 다시 **Lazy** 로딩을 걸어 값을 가져오려고 하니

  **DB**와 연결된 **Connection**이 없어서 생기는 오류였습니다.

  [[Hibernate / JPA ] LazyLoading | No Session 에러에 대해서](https://sticky32.tistory.com/entry/Hibernate-JPA-LazyLoading-No-Session-%EC%97%90%EB%9F%AC%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C)

  [우당탕탕 테스트 격리 트러블 슈팅기](https://sokdak-sokdak.tistory.com/5)

### 4. **Service 객체의 A라는 메소드 내부에서 로컬 트랜잭션 3개가 존재한다고 할 때, A 메소드에 `@Transactional`을 적용하면 어떤 요청 흐름이 발생하는지 설명하라.**

트랜잭션 전파 수준에 따라 달라진다.

만약 기본 옵션인 `REQUIRED`를 가져간다면 로컬 트랜잭션 3개가 모두 부모 트랜잭션인 A에 합류하여 수행된다.

그래서 부모 트랜잭션이나 로컬 트랜잭션 3개나 모두 같은 트랜잭션이므로 어느 하나의 로직에서든 문제가 발생하면 `전부 롤백`이 된다.
