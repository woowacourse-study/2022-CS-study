# 객체지향 쿼리

- 기존 쿼리 문법을 자바의 객체 기준으로 쿼리 !

```sql
select m, t.name from Member m join m.team t
```

예를 들어 위의 경우 `Member`의 경우 테이블 명이 아닌 `클래스명`이 들어간다!

(`member`, `MEMBER` 등의 표기를 할 경우 런타임 예외가 발생! - QUERYDSL 등을 사용하면 미연에 방지할 수 있다)

`t.name`의 경우도 테이블의 컬럼명이 아닌 클래스의 필드명이 들어가야한다!

# DB벤더 추상화 - 여러가지 편의 메서드 지원

FETCH JOIN, 페이징 API 등 각 DB 벤더사 마다 다른 문법을 하나로 추상화하였다

특히 오라클 페이징 API의 경우 다중 서브쿼리문을 작성해서 구현해야 하는데 JPQL을 이용하면 정말 간편하다!

```java
em.creaetQuery("select m from Member m", Member.class)
 .setFirstResult(10)
 .setMaxResult(20)
 .getResultList();
```

# QueryDSL

- JPQL을 자바로 작성할 수 있다

- 작성된 자바 문법은 실행시 JPQL이 되고, 이것은 다시 적용 대상의 DB의 쿼리가 되어 실행한다

- 런타임도, 로딩 시점도 아닌 컴파일 타임에 오류를 잡을 수 있다

- 퇴근할 때 연락와서 "장애 났어요"하고 집에 돌아갈 일이 적어짐...
