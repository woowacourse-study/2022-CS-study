# 프로세스 동기화

## 1. 경쟁상태(Race Condition)

### 정의

여러 프로세스들이 동시에 데이터 접근하는 상황

### 동기화(Synchornization)

공유 데이터의 동시 접근은 데이터 불일치 문제를 발생시킬 수 있다.

따라서 어떤 순서로 데이터에 접근할지 정해야 한다.

이런 프로세스 간 실행순서를 정하는 방식을 동기화(`Synchornization`) 이라고 한다.

### 발생 예시

각 프로세스는 데이터 공간이 독립적이다. 그래서 보통 경쟁상태는 스레드 단위에서 발생한다.

그럼에도 프로세스에서 경쟁상태가 발생할 때는, 시스템 콜을 수행하며 같은 커널 주소 공간의 데이터에 접근할 때이다.

1. 커널모드 수행 중 인터럽트
2. 프로세스가 시스템 콜을 호출해 커널모드 수행 중인데 컨텍스트 스위칭 발생
3. 멀티 프로세서 공유메모리 내 커널 데이터에 접근

## 2. 임계 구역(Critical Section)

### 정의

위의 경쟁상태가 발생하는 구역을 임계구역이라고 한다.

### 문제해결 조건

위의 임계구역에서 발생하는 문제를 해결하기 위해서는 다음 조건을 만족해야 한다.

1. 상호 배제(`Mutual Exclusion`) : 한번에 하나의 작업단위(프로세스) 만이 자원에 접근 가능하다.
2. 진행(`Progress`) : 임계구역에 접근중인 프로세스가 없다면, 접근을 원하는 프로세스가 진입 가능해야 한다.
3. 한정 대기(`Bounded Waiting`) : 대기중인 프로세스는 무한정 대기해서는 안된다. 일정 시간동안만 임계영역 진입을 위해 대기해야 한다.

## 3. 동기화(Synchronization) 알고리즘

### 알고리즘 1

현재 `Critical Section`에 들어갈 프로세스가 어떤 프로세스인지를 turn 변수로 나타내어

일치하는 프로세스만 진입한다.

즉, 들어가는 프로세스들이 들어갈 수 있는 빈도가 순서가 있고 동일하다!

(A 기회→B 기회→A 기회→B 기회)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Faf3fced8-8595-49e8-99d6-c37a73c28f71%2FUntitled.png?table=block&id=4dd24ae9-49b5-4d06-a838-27af4ef1deab&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=1920&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

문제: `progress` 만족하지 못한다. B 프로세스가 더 많이 자원에 접근하고 싶어도 방법이 없다.

### 알고리즘 2

프로세스들은 **flag** 변수를 통해 자신이 임계구역에 들어가고 싶다고 의사를 표시한다.

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F600b726c-5e92-4a3e-9384-a786f938d0f3%2FUntitled.png?table=block&id=fb627c63-4611-4ff3-9e9d-b464c0b887e1&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=1920&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

문제 : 역시 `Progress` 만족 못한다.

두 프로세스가 `flag = true`까지만 수행하고 나면,

두 프로세스 모두 무한히 `Critical Section`에 진입하지 못하고 기다리는 상황이 발생할 수 있다.

즉, 프로세스들이 서로 끊임없이 양보하게 된다!

### 알고리즘 3 (최종) : Peterson’s Algorithm

위 두 알고리즘의 통합!

- **깃발**로 의사 표시 + **순서** 확인
- 상대방이 둘 중 하나라도 만족하지 못한다면, 내 프로세스가 임계구역에 들어가겠다!

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0e219211-42a6-46d6-adf9-860ea7833b6d%2FUntitled.png?table=block&id=49e19aec-af51-40d3-86b5-92bc5e6b4494&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=1920&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

→ 임계구역 문제해결 조건을 모두 만족한다!

하지만 스핀락의 `busy waiting` 문제가 있다…

## 4. 스핀락

![대기중인 프로세스는 진입 가능할 때까지 루프를 돌면서 임계구역 접근을 재시도!](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Faa6557a3-25d3-4b6a-8b6e-dee03f4078dd%2FUntitled.png?table=block&id=324e9109-0f1f-433d-8500-819e951fa748&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=1920&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

대기중인 프로세스는 진입 가능할 때까지 루프를 돌면서 임계구역 접근을 재시도!

대기중인 프로세스가 계속 자신이 프로세스에 접근 가능한지 확인해야 함.

따라서, CPU, 메모리 낭비가 생긴다!

이를 `Busy Waiting` 이라고 한다.

다만, 컨텍스트 스위칭 비용보다 기다리는 비용이 더 효율적일 때,

즉 임계영역 대기시간이 매우 짧을 시엔 스핀락이 더 유용하다.

보통 멀티 프로세서 시스템에서 자주 사용된다.

## 5. Synchronization Hardware

하드웨어적으로 임계구역 문제를 해결하는 방법!

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4349130c-b8ad-463e-8a96-d013274306c4%2FUntitled.png?table=block&id=21d05f1f-1f56-45d3-a662-2ab251b7513e&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=1920&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

메모리를 읽을 때마다 락을 걸어주고, 읽은 값을 해당 읽은 값으로 변경(재할당)한다!

## 5. 뮤텍스(Mutex)

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6df86b2e-e4aa-4b76-9852-c029b599e23b%2FUntitled.png?table=block&id=73ba495f-0c05-4260-b2fc-7cd1e53dac43&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=1920&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

한 프로세스가 임계영역에 진입해 작업 중일 시, 다른 프로세스들은 임계영역에 못 들어간다!

즉, 하나의 작업단위만 임계영역 진입이 가능하다!

문제 : 위의 알고리즘 3과 같은 방식이므로, 스핀락의 `busy waiting` 문제가 발생한다.

## 6. 세마포어(Semaphores)

뮤텍스와 달리 `busy waiting` 방식을 쓰지 않는다.

여러 프로세스/스레드가 임계영역 진입이 가능하다!

![Untitled](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5b46d804-c0db-4c09-990a-f81388f9c2a8%2FUntitled.png?table=block&id=c08df1b6-1247-4461-951e-7f1002156e8d&spaceId=e42cf19b-11fa-489a-b838-9fa7c907d88b&width=1920&userId=85e5532e-ee96-45b7-9d38-da2e23ad85f0&cache=v2)

### 방식

1. `busy waiting` : 계속 이용 가능한지 물어본다
2. `block & wakeup` : 일단 잠들었다가, 자원이 반납될 때 깨움당한다. 보통 더 좋다.

### 특징

- 카운터를 통해 동시 접근가능한 프로세스 수(자원 개수)를 제한한다.
- 접근한 프로세스들 중 하나가 임계영역 변수를 수정할 때, 다른 프로세스는 변수 수정 불가능하다.
- 각 접근한 프로세스들은 자원 획득 연산(P 연산), 자원 반납 연산(V 연산) 중 하나를 수행하게 된다.

### 종류

- `Binary Semaphore` : 0,1값만 가질 수 있는 세마포어. 즉, 뮤텍스!
- `Counting Semaphore` : 뮤텍스가 아닌 세마포어. 즉, 카운터 개수가 2 이상인 세마포어.

---

## 참고자료
[[운영체제(OS)] 6. 프로세스 동기화(Process Synchronization)](https://rebro.kr/176)

[운영체제](http://www.kocw.net/home/cview.do?cid=3646706b4347ef09)
