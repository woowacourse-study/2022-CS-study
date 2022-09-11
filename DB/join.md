# JOIN의 개념과 MySQL에서의 알고리즘 살펴보기

`JOIN`은 `SQL`을 사용하게 되면 반드시 활용하는 기능 중 하나입니다. 프로젝트를 하면서 `INNER JOIN`, `OUTER JOIN` 을 통해서 대부분 원하는 로직들을 구현할 수 있었습니다. 하지만 내부적으로는 `JOIN`이 어떻게 알고리즘 적으로 동작하는지 잘 모르고 사용을 해왔었습니다. 그래서 이번 시간에 `JOIN`의 개념 복습과 함께, `JOIN`이 어떤식으로 동작하는지 한번 살펴보고자 합니다.

<br>

## 조인(JOIN)이란?

`JOIN`은 둘 이상의 테이블을 결합하여서 하나의 테이블처럼 사용하기 위함이라고 할 수 있습니다. A 테이블에서 FK를 지정하고 B 테이블에서 이와 관련된 PK를 통해 **일반적으로** `JOIN`을 사용할 수 있습니다.

`JOIN`의 종류로 크게 `INNER JOIN`, `OUTER JOIN`, `CROSS JOIN` 3가지로 분류를 할 수 있습니다.

![image](https://user-images.githubusercontent.com/48710213/189535651-a6a280ca-acfd-4cda-8020-b249a4a723ef.png)

<br>

## 조인(JOIN) 종류

[예시 테이블]

```sql
SELECT * FROM BROWN_LV1;
id | name 
1     헌치
2     로마
3     루키
4     써머
    
SELECT * FROM BROWN_LV2;
id | name 
1     루키
2     써머
3     호호
4     포키
```

### INNER JOIN

`INNER JOIN`은 두 개의 테이블이 모두 조인 조건을 만족하는 데이터를 조회하는 방법입니다. 즉, 두 테이블의 교집합이라고 말할 수 있습니다.

```sql
SELECT *
FROM BROWN_LV1 LV1
INNER JOIN BROWN_LV2 LV2 ON LV1.name = LV2.name;

// 결과
3 루키 1 루키
4 써머 2 써머
```

### OUTER JOIN

`OUTER JOIN`은 두 개의 테이블 중, 기준 테이블에 공통되지 않은 행도 조회하는 방법입니다. `OUTER JOIN`은 크게 2가지로 분류할 수 있는데 왼쪽 테이블을 기준으로 할 경우 `LEFT OUTER JOIN`, 오른쪽 테이블 기준으로 할 경우 `RIGHT OUTER JOIN`을 사용하면 됩니다. (추가적으로 Oracle에는 FULL OUTER JOIN이 존재하지만 MySQL에는 존재하지 않습니다.)

```sql
SELECT * 
FROM BROWN_LV1 LV1
LEFT OUTER JOIN BROWN_LV2 LV2 ON LV1.name = LV2.name;

// 결과
1 헌치 null null
2 로마 null null
3 루키 1    루키
4 써머 2    써머
```

```sql
SELECT * 
FROM BROWN_LV1 LV1
RIGHT OUTER JOIN BROWN_LV2 LV2 ON LV1.name = LV2.name;

// 결과
1 루키 3    루키
2 써머 4    써머
3 호호 null null
4 포키 null null
```

### CROSS JOIN

`CROSS JOIN`은 한쪽 테이블의 모든 행들과 다른 쪽 테이블의 모든 행을 `JOIN`시키는 것을 말합니다. 즉, **두 테이블의 교차곱을 의미**하므로 결과 개수는 두 테이블의 개수를 곱한 값이 됩니다.

이러한 `CROSS JOIN`은 실제 프로덕션 상에서는 사용되지 않고, 테스트용 데이터를 만들 필요가 있을 때, 주로 사용됩니다.

```sql
SELECT *
FROM BROWN_LV1 LV1
CROSS JOIN BROWN_LV2 LV2;

// 결과
1 헌치 1 루키
1 헌치 2 써머
1 헌치 3 호호
1 헌치 4 포키
2 로마 1 루키
2 로마 2 써머
2 로마 3 호호
... 계속 반복
```

<br>

## 조인(JOIN) 알고리즘과 성능

`JOIN`을 수행할 때 내부적으로 선택되는 알고리즘의 종류는 `Nested Loops Join`, `Hash Join`, `Sort Merge Join`으로 분류할 수 있습니다.

이러한 `JOIN` 알고리즘을 선택하는 기준으로는 데이터의 크기, 결합키(Key), 인덱스(Index)와 같은 요인에 따라 **옵티마이저**가 결정하게 됩니다.

우리가 자주 사용하는 `MySQL`은 3개의 알고리즘 중 `Nested Loops Join`만 지원을 합니다. (오라클은 3개 전부 지원) 따라서 이번 시간에는 `Nested Loops Join`에 대해서만 살펴보도록 하겠습니다.

> 옵티마이저란?
>

SQL 쿼리를 작성해서 실행을 시키면, 해당 쿼리는 **옵티마이저**로 전송됩니다. 옵티마이저는 **‘최적화’**라는 의미인데, ‘데이터에 어떻게 접근하여 결과를 도출할지'가 최적화의 대상이 됩니다. 이때, 데이터의 크기, 인덱스 유무 등의 여러 조건을 고려해서 선택 가능한 많은 **‘실행계획**'을 작성하고, 이를 연산하여 가장 낮은 비용을 가진 실행계획을 선택해줍니다.

### Nested Loops Join

`Nested Loops Join`은 다른 `JOIN` 알고리즘의 기본이 되는 알고리즘 입니다. 이름이 의미하는 것 처럼 **중첩 반복을 사용하는 알고리즘**이라고 할 수 있습니다.

그림으로 살펴보면 다음과 같습니다.

![image](https://user-images.githubusercontent.com/48710213/189535712-08e02bad-f854-4050-b3d3-dc7310866c88.png)



```java
1. Table A와 Table B가 어떤 Key를 기준으로 결합(JOIN)을 진행합니다.
2. Table A(Driving Table)의 첫 번째 행에서 출발해, Table B(Driven Table)의 모든 행을 스캔합니다.
3. Table A의 첫 번째 행의 스캔이 끝나면 두 번째 행이 Table B의 모든 행을 스캔합니다.
4. 2 ~ 3번 과정을 반복하여 Table A의 마지막 행이 Table B의 모든 행을 스캔하면 결합(JOIN)이 완료됩니다.
```

즉, 이와 같이 알고리즘을 수행하게 된다면, `R(A) * R(B)` 만큼 레코드를 접근하게 되며 실행시간은 이에 비례한다고 할 수 있습니다. 이를 다르게 말한다면, `R(A) * R(B)` 의 레코드 접근이 적을수록 실행시간이 빨라진다고 할 수 있습니다. 어떻게 하면 성능을 향상시킬 수 있을까요??

결론부터 말하자면, `**Driving Table`이 작고, `Driven Table`의 결합키(FK) 필드에 인덱스가 존재할 경우 성능을 향상시킬 수 있다**고 할 수 있습니다.

![image](https://user-images.githubusercontent.com/48710213/189535751-7b1fcc36-c344-4d06-bfcf-98cd197373bb.png)

이렇게 `Driven Table` 의 결합키에 인덱스가 존재하게 된다면, `Driving Table`에서 `Driven Table`을 스캔할 때 모든 행에 대해 스캔을 할 필요가 없기 때문에 실행시간을 단축시킬 수가 있습니다.

### Driving Table은 어떻게 결정할까??

`Nested Loops Join`을 설명할 때, `Driving Table`에 대해서 얘기가 나왔었습니다. `Driving Table`이란, `JOIN`이 진행될 때 다른 테이블의 결합키에 다가가서 주도적으로 매칭을 시도하는 테이블입니다.

이런 `Driving Table`을 잘 선택해야 앞서 말한 것 처럼 성능향상을 얻을수가 있습니다. 하지만 놀랍게도 `Driving Table`은 **옵티마이저가 최적의 실행계획에 따라 결정**을 해주게 됩니다. 그래서 우리는 옵티마이저를 믿고 `JOIN` 연산을 수행하면 됩니다.

하지만 하나의 예외 상황이 있는데, `OUTER JOIN`을 실행하는 경우 입니다. 그 이유는 `INNER JOIN`의 경우 어느 테이블을 먼저 읽어도 결과가 달라지지 않기 때문에, 옵티마이저가 `JOIN`의 순서를 조절해 다양한 방법으로 최적화를 수행하지만, `**OUTER JOIN`의 경우 반드시 `OUTER`가 되는 테이블을 먼저 읽어야하기 때문에 옵티마이저가 `JOIN`의 순서를 선택할 수 없습니다**.

이점을 유의해서 `JOIN`을 작성해야 겠습니다 ㅎㅎ
