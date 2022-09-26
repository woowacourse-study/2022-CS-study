# 1. 개념 정리

### 🤔 **Database**

컴퓨터 시스템에 전자 방식으로 저장된 구조화된 정보 또는 데이터의 체계적인 집합

### 🤔 **DBMS**(DataBase Management System)

데이터베이스를 정의, 생성, 유지하는 프로그램들의 집합

### 🤔 RDB(Relational DataBase)

테이블, 행, 열의 정보를 **2차원 테이블 형태**로 구조화하는 방식.

- 테이블을 `Join`해 정보 간 관계 또는 링크를 설정할 수 있는 기능이 있다. 여러 데이터 포인트 간의 관계를 쉽게 이해하고 정보를 얻을 수 있다.
- 비즈니스에서 데이터를 구성, 관리, 연결하는 데 도움이 되는 스프레드시트 파일 모음이라고 생각하면 된다.

### 🤔 **SQL**(Strucured Query Language)

`RDBMS`의 데이터를 관리하기 위해 설계된 특수 목적 프로그래밍 언어.

`RDBMS`에서

- 자료의 검색과 관리,
- 데이터베이스 스키마 생성과 수정,
- 데이터베이스 객체 접근 조정 관리

를 위해 고안되었다.

### 🤔 관계(R)(Relational)

테이블 간의 상호작용을 기반으로 설정되는 여러 테이블 간의 논리적 연결

# 2. RDBMS **💾**

![RDBMS](https://prepinsta.com/wp-content/uploads/2021/04/RDBMS.webp)

`R`+ `DBMS` : **관계형** 데이터베이스 관리 시스템

`SQL`에 의해 저장되고 있으며 정해진 스키마에 따라 데이터를 저장해야 한다.

- 테이블이 다른 테이블들과 관계를 맺고 모여있는 집합체로 이해할 수 있다.
- 이러한 관계를 나타내기 위해 외래 키(`foreign key`)라는 것을 사용한다.
- 외래 키를 이용한 테이블 간 `Join`이 가능하다.

### 예시

`MySQL`, `PostgreSQL` 등

## 👍 장점

데이터 일관성을 지킬 수 있다!

- 정해진 스키마에 따라 데이터를 저장하여야 하므로 명확한 데이터 구조를 보장하고 있다.
- 각 데이터를 중복없이 한 번만 저장할 수 있다.

## 👎 단점

- 시스템이 커질 경우 `JOIN`문이 많은 복잡한 쿼리가 만들어질 수 있다.
- 성능을 향상시키기 위해서는 장비가 좋아야 한다. (`Scale-Up` : 서버 그 자체를 업그레이드 함) **비용이 기하급수적으로 증가**한다.
- **스키마** 때문에 유연한 데이터 관리가 어렵다. 스키마가 변경될 시 번거롭다.

# 3. **NoSQL**(Not Only SQL) **💾**

![NoSQL](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTGmuWz3mdlTb605Vh2Sy2fHHE_yZ9DQ5cvaw&usqp=CAU)

위에서 설명한 `RDBMS`가 아닌, 다른 형태의 데이터 저장 기술을 의미한다.

- `RDBMS`와는 달리 **테이블 간 관계를 정의하지 않는다!**
- 데이터 테이블은 그냥 **하나의 테이블**일 뿐이다.
- 테이블 간의 관계를 정의하지 않아 일반적으로 **테이블 간 `Join`도 불가능**하다.

### 예시

`MongoDB`, `Redis` 등

## 🚀 사용 목적

**빅데이터**가 등장하면서, 점점 **데이터와 트래픽이 기하급수적으로 증가하는 상황**에서 고안되었다.

## 👍 장점

유연성

- 스키마가 없어 유연하고 자유로운 데이터 구조를 가질 수 있다. 언제든 저장된 데이터를 조정하고 새로운 필드를 추가할 수 있다.
- 데이터 분산이 용이하다. 수평적 확장(`Scale-Out` : 서버 개수를 늘림)을 쉽게 할 수 있다. 따라서 비용이 적게 든다.

## 👎 단점

데이터 일관성이 지켜지지 않는다.

- 데이터 중복이 발생하고, 해당 데이터가 변경될시 모든 컬렉션에서 수정해야 한다.
- 스키마가 없으니 명확한 데이터 구조를 보장하지 않는다. 데이터 구조 결정 자체가 어렵다.

## 🧷 종류

![종류](https://learn.microsoft.com/en-us/dotnet/architecture/cloud-native/media/types-of-nosql-datastores.png)

### `Key-Value Database`

- `Redis`, `Riak`, `Amazon Dynamo DB` 등이 있다.

데이터가 `Key`와 `Value`의 쌍으로 저장된다.

- `Key`는 `Value`에 접근하기 위한 용도로 사용되며, 값은 어떠한 형태의 데이터라도 담을 수 있다. 심지어는 이미지나 비디오도 가능하다.
- 검색에 최적화되어 있다. 질의의 속도가 매우 빠르다.

### `Document Database`

- `MongoDB`, `CouthDB` 등이 있다.

데이터가 `Key`와 `Document`의 형태로 저장된다.

- `Key-Value` 모델과 달리 `Value`가 계층적인 형태인 `Document`로 저장된다. **객체지향에서의 객체와 유사하며, 이들은 하나의 단위로 취급되어 저장된다.** 즉, 하나의 객체를 여러 개의
  테이블에 나눠 저장할 필요가 없어진다.
- 객체-관계 매핑이 필요하지 않다. 객체를 `Document`의 형태로 바로 저장 가능하기 때문이다.
- 검색에 최적화되어 있다. `Key-Value` 모델의 특징과 동일하다.

**단점**

- 사용이 번거롭고 쿼리가 SQL과는 다르다.
- 도큐먼트 모델에서는 질의의 결과가 `JSON`이나 `xml` 형태로 출력되기 때문에 그 사용 방법이 `RDBMS`에서의 질의 결과를 사용하는 방법과 다르다.

### `Wide Column Database`

- `HBase`, `Hypertable` 등이 있다.

`Column-family Model` 기반의 `Database`.

- 특이하게도 이 모델은 키에서 필드를 결정한다.
- 키는 `Row`(키 값)와 `Column-family`, `Column-name`을 가진다. 연관된 데이터들은 같은 `Column-family` 안에 속해 있으며, 각자의 `Column-name`을 가진다.
  관계형 모델로 설명하자면 속성이 계층적인 구조를 가지고 있는 셈이다.
- 저장된 데이터는 하나의 커다란 테이블로 표현이 가능하며, 질의는 `Row`, `Column-family`, `Column-name`을 통해 수행된다.

### `Graph Database`

- `Neo4J` 등이 있다.

데이터를 `Node`와 `Edge`, `Property`와 함께 그래프 구조를 사용하여 데이터를 표현하고 저장하는 `Database`.

- 개체와 관계를 그래프 형태로 표현한 것이므로 **관계형 모델이라고 할 수 있다.**
- 데이터 간의 **관계가 탐색의 키일 경우에 적합**하다.
- 페이스북이나 트위터 같은 소셜 네트워크에서(내 친구의 친구를 찾는 질의 등) 적합하고, 연관된 데이터를 추천해주는 추천 엔진이나 패턴 인식 등의 데이터베이스로도 적합하다.

# 4. 언제 쓰지? 🧐

![when to use](https://le-cdn.website-editor.net/s/192387ccd8824fdcac0b5ed96f855ec3/dms3rep/multi/opt/nosql-vs-sql-overview-1-640w.png?Expires=1664949840&Signature=KmHf7QUMNRsgjTM~aO-wX0VXP91s3rU6AoSo3y386EJagPcq~D0VYx~Bgk6HtLWCtRc~6tPQfaZFN41Qs4KmsTw50vkUHahyNdMJKx2~68ejNK4cDYashHogzsgdzxmabLFh7h4e8r1Og9gfS~LypiFJwSMxdC17ALYU9bGyzVfjX-6Pcs42Vem~cGo3IhqM0cP3wHFnxlZCBzi-OVbEMKiSqUYDTpDbLAfibjZozzPyPUWtrOPG4g~INqvwwv3aokyw9VrSp6x~wlBU3TVfjyI3XXcCHm8qIN5MNpDxxBBwltFSlPrlTv87o~m5KMj1~fi4-amOB2bB7UemvFHH2g__&Key-Pair-Id=K2NXBXLF010TJW)

## **💾 RDBMS**

- 데이터 구조가 명확하며 변경 될 여지가 없으며 **명확한 스키마가 중요한 경우** 사용하는 것이 좋다.
- 또한 중복된 데이터가 없어(데이터 무결성) 변경이 용이하기 때문에 **관계를 맺고 있는 데이터가 자주 변경이 이루어지는 시스템**에 적합하다.

## **💾 NoSQL**

- 정확한 데이터 구조를 알 수 없고 **데이터가 변경/확장이 될 수 있는 경우**에 사용하는 것이 좋다.
- **수정이 별로 없는 시스템**이 좋다. 데이터 중복이 발생할 수 있으며 중복된 데이터가 변경될 시에는 모든 컬렉션에서 수정을 해야 하기 때문이다.
- `Scale-out`이 가능하므로 **막대한 데이터를 저장해야 하는 시스템**에 적합하다.

---

## 참고자료

[https://tecoble.techcourse.co.kr/post/2021-10-12-scale-up-scale-out/](https://tecoble.techcourse.co.kr/post/2021-10-12-scale-up-scale-out/)

[https://cloud.google.com/learn/what-is-a-relational-database?hl=ko#:~:text=관계형 데이터베이스 관리 시스템](https://cloud.google.com/learn/what-is-a-relational-database?hl=ko#:~:text=%EA%B4%80%EA%B3%84%ED%98%95%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EA%B4%80%EB%A6%AC%20%EC%8B%9C%EC%8A%A4%ED%85%9C)

[https://khj93.tistory.com/entry/Database-RDBMS와-NOSQL-차이점](https://khj93.tistory.com/entry/Database-RDBMS%EC%99%80-NOSQL-%EC%B0%A8%EC%9D%B4%EC%A0%90)
