# ORM에서 자주 발생하는 이슈

쿼리 하나에 대하여 추가적으로 N개가 발생할 수 있다.

# [원인] 발생 시나리오

1. `JPQL` 등을 통해서 N개의 row를 불러들인다.

```java
List<Team> teams = em.createQuery("select t from Team t", Team.class);
```

2. 각 row를 돌면서 추가적으로 data를 가져온다. (`LAZY-LOADING`)

```java

List<ResponseDto> responseDtos = ...;
for(Team team : team.getMembers()) {
    List<Member> members = team.getMembers();
    for(Member member: members) {
        // member에 대한 LazyLoading
        responseDtos.add(team.getName(), member.getName(), ...);
    }
}
```

# [해결법] - NOT GOOD

## 글로벌 설정

TEAM 클래스의 MEMBER 객체를 즉시로딩으로 바꾼다

```java
class Team {

    ...

    @OneToMany(fetch = EAGER)
    Member member;

    ...
}
```

# [해결법] - GOOD

## 필요한 부분에서만 처리 (`JPQL`)

### FETCH JOIN

일반 JOIN문과 다른 부분은 select절에 join된 객체의 엔티티를 모두 가져온다

```java
List<Team> teams = em.createQuery("select t Team t join fetch t.mebers m", Team.class);
```

이때 JPQL의 실행문에 MEMBER 객체도 같이 가져온다!

```sql
select
  t.id, t.name,
  m.id, m.name, m.team_id
from Team t
join Member m
  on t.id = m.team_id;
```

### 일반 JOIN + DTO

FETCH로도 해결하지 못하는 부분은 이 방법으로 해결

```java
List<ResponseDto> responseDtos = em.createQuery("select new 패--키지_경로명.ResponseDto(t.name, m.name) from Team t join t.members m", ResponseDto.class);
```
