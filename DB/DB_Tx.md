# Isolation Level

![image](https://user-images.githubusercontent.com/66164361/196096466-d252dd02-c417-4d80-912c-8e8f6f636145.png)

- 각 항목 정의(열)
  - Dirty Read: 다른 Tx가 커밋하지 않은 데이터를 읽음
  - Non-Repeatable Read: 반복해서 읽을 때 동일한 데이터를 읽지 못함
  - Phantom Read: 다시 읽었을 때 데이터가 추가되거나 삭제 됨

결국, 동일 조건에서 n번 반복해서 읽었을 때 조회 결과 값이 다른 것!

- 세 가지 이상 현상을 정의하고 어떤 현상을 허용하는지에 따라서 각각의 `isolation level`이 구분된다
- App 설계자는 (우리와 같은 BE) isolation level을 통해 전체 처리량(`thoroughput`)과 데이터 일관성 사이에서 어느 정도 거래를 할 수 있다
- 참고로 Serializable은 위 세 가지 현상 뿐만 아니라 아예 이상한 현상 자체가 발생하지 않는 level을 의미한다.

![image](https://user-images.githubusercontent.com/66164361/196097119-925ef04d-cd7d-447f-bd41-fdad0d404da0.png)

- 위 isolation 을 발표한 논문

![image](https://user-images.githubusercontent.com/66164361/196097180-efc1a4c0-3f5a-4d54-806b-fc521a391b3a.png)

- 95년도에 나온 위 논문을 비판하는 논문

- 1. 세가지 이상 현상의 정의가 모호
- 2. 이상 현상은 세 가지 이외에도 더 있다
- 3. 상업적인 DBMS에서 사용하는 방법을 반영해서 isolation level을 구분하지 않았음
  - snapshot isolation 
    - MVCC를 어떻게 구현할 것인지에 따라 isolation level을 정의
      - tx 시작 전 commit된 데이터만 보임
      - first-commiter win

# Lock

### Shared Lock, Exclusive Lock

- Shared Lock
  - S-Lock, 읽기락, 공유락
  - 자신의 Tx가 획득시 다른 Tx의 쓰기는 막고 읽기는 허용한다
  - 다른 Tx의 S-Lock 허용, X-Lock 막음
- Exclusive Lock
  - X-Lock, 쓰기락
  - 자신의 Tx가 획득시 다른 Tx의 읽기, 쓰기 모두 막는다
  - 다른 Tx의 S, X-Lock 모두 막음

| . | S-Lock | X-Lock |
|--------|----|----|
| S-Lock |  O  | X |
| X-Lock |  X  | X |

### S-Lock, X-Lock 문제?

- X-Lock을 사용하고 있을 경우 read 조차 허용하지 않기 때문에 동시에 처리할 수 있는 처리량(throughough)이 낮다

# MVCC 

- `M`ulti`V`erseion `C`oncurency `C`ontrol
- write를 할 때 read를 허용하겠다 !
- 다만 write를 할 때 write 락을 사용한다
- MySQL에서는 Read Commited 와 Repeatable Read 에서 MVCC를 사용한다


```
- MVCC(Multi Version Concurrency Control) - 잠금을 사용하지 않는 일관된 읽기를 제공

MVCC는 다중 버전 병행수행 제어의 약자이다.
write 세션이 read 세션을 블로킹하지 않고, read 세션이 write 세션을 블로킹하지 않게,
서로 다른 세션이 동일한 데이터에 접근했을 때 
각 세션마다 스냅샷 이미지를 보장하는 메커니즘

이는 RDBMS에서 동시성을 높이기 위해 등장한 기술로, 
소수의 전산실 운영자들이 서버 컴퓨터를 사용하던 시절에는 MVCC가 선택사항이었지만 
인터넷이 보편화되고 온라인으로 업무를 처리하는 시대에서는 DBMS를 선택하는데 있어 MVCC가 가장 중요한 요소가 됐다. 

출처 : 데이터넷(http://www.datanet.co.kr)
```

### MVC에 의한 read-write 관계 표

| . | read | write |
|--------|----|----|
| read |  O  | O |
| write |  O  | X |


# 예시로 알아보는 격리수준

![image](https://user-images.githubusercontent.com/66164361/196092387-fc9a3d8d-c47c-4498-9b2b-f5ffa22870d0.png)

- MVCC는 커밋된 데이터만 읽는 것이 기준
  - 따라서 Read UnCommited 는 MVCC를 사용하지 않는다
- Tx2가 50으로 쓰기만 하고 Tx1 입장에서는 commit하지 않은 데이터를 읽지 않고, 10이란 데이터를 읽는다

![image](https://user-images.githubusercontent.com/66164361/196092623-48b7cf04-501b-4488-8ca5-a4c6939e08cc.png)

### Tx2 가 데이터를 업데이트한 뒤에는 상황이 다르다
  - 이 경우 Tx1의 격리 레벨에 따라 다른 데이터를 읽는다

# 1. Read Commited 인 경우

- Read 하는 시간을 기준으로 그 전에 Commit된 데이터를 읽는다
- 따라서 Tx2에서 커밋한 50이란 숫자를 읽어간다
- 정확히는 UnDo 영역의 백업된 레코드를 읽어온다

  ![image](https://user-images.githubusercontent.com/66164361/196095074-fa1994ee-88f9-4555-856f-c3fe457259cc.png)

### ★ 언두(Undo) 로그 ★

```
언두 영역은 UPDATE 문장이나 DELETE와 같은 문장으로 데이터를 변경했을 때 변경되기 전의 데이터(이전 데이터)를 보관하는 곳입니다.

언두의 데이터는 크게 두 가지 용도로 사용이 됩니다.

1) 트랜잭션의 롤백 대비용

2) 트랜잭션의 격리 수준을 유지하면서 높은 동시성을 제공

참고: https://zzang9ha.tistory.com/m/381 

```

# 2. Repeatable Read인 경우

- Tx 시작 시간 기준으로 그 전에 commit 된 데이터를 읽는다
- 따라서 시작할 때 읽었던 10이란 값을 가져온다
- MySQL InnoDB에서 작동하는 개념은, 자기 자신의 Tx ID보다 크지 않은 값 중에서 가장 큰 커밋 값을 읽어간다.

![image](https://user-images.githubusercontent.com/66164361/196093071-7dc00fda-f0ce-4218-98a1-1b449802ddd1.png)

- 정확히는 자신에게 부여된 Tx번호보다 낮은 번호 중에서 가장 최신의 Commit 을 읽어간다

### (표준) Reaptable Read에서는 Phantom Read가 발생할 수 있다

![image](https://user-images.githubusercontent.com/66164361/196093608-12c786f5-7fb2-4061-8455-09021b9a8fed.png)

- 본래는 위처럼 데이터가 삽입이 된 경우, 동일 조건에서 읽음에도 불구하고 데이터가 추가된 것 만큼 읽는 것을 발견할 수 있다
- 그러나 MySQ InnoDB는 레코드 락과 갭락을 사용하기 때문에, 조건절에 해당하는 레코드에 락이 걸리게 되어 데이터가 삽입되는 경우를 막을 수 있다

### (MySQL) Reaptable Read에서는 Lost Update가 발생할 수 있다

![image](https://user-images.githubusercontent.com/66164361/196098145-1c12ca38-cb27-47f6-b5e2-b7d9facf1546.png)

- Tx2가 먼저한 commit의 결과가 Tx1가 나중에 한 Commit 결과로 덮어 씌워진다
- 해결은 locking read를 통해서 한다

![image](https://user-images.githubusercontent.com/66164361/196099835-54dc6905-b40b-4424-b067-1541389e30cf.png)

- 처음 읽어가는 시점(Tx2)에서 `SELECT ... FOR UPDATE` 를 통해서 읽기, 쓰기 모두에 락을 건다
- 그 이후 Tx1이 읽는 시점에서도 locking read를 한다
- commit된 시점에서 락이 해제가 되고 Tx1이 락을 취득한다
- 이때 읽는 것은 최초의 50이 아닌, 가장 최근에 커밋된 80을 읽어간다
  - locking read는 
    - Tx 시작 시간 기준으로 그 전에 commit 된 데이터가 아닌 (repeatable read 정의)
    - 가장 최근의 commit된 데이터를 읽는다


### Read UnCommited 에서는 Dirty Read가 발생한다

![image](https://user-images.githubusercontent.com/66164361/196094188-3a20c4c8-c506-4ffe-a4ed-10ae264542f1.png)

- 사용자 B는 아직 완전히 커밋되지 않은 50,000번의 데이터를 읽고 있다
  - 사용자 A가 작업 도중 문제가 생겨 롤백이 생겨서 없어질 수도 있는 데이터를 읽고 있는 것 !
- RDBMS 표준에서는 트랜잭션의 격리 수준으로 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준

### Serializable

- 동일 조건에서 n번 조회시 `이상 현상`(데이터 부정합: Dirty Read, Non-Repeatable Read , Phatom Read)이 `나오지 않는다`
  - 그러나 제약 사항이 많아져서 동시 처리 가능한 트랜잭션 수가 줄어들어 결국 DB의 전체 처리량(`throughput`)이 `하락`하게 된다
- MySQL에서는 `MVCC로 동작`하기 보다는 `lock으로 동작`한다


# 참고

- 각 격리 수준에 대한 설명
  - https://zzang9ha.tistory.com/m/381
- 락에 대한 설명
  - https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/
- repeatable read 실험
  - https://jyeonth.tistory.com/m/32
- 케이의 ACID
  - https://kth990303.tistory.com/m/384
- 쉬운 코드
  - https://youtu.be/wiVvVanI3p4
  - https://youtu.be/-kJ3fxqFmqA
  - https://youtu.be/bLLarZTrebU
- 최범균님
  - https://www.youtube.com/watch?v=urpF7jwVNWs
  - https://www.youtube.com/watch?v=poyjLx-LOEU