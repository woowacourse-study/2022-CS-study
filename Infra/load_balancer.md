# ?? 로드 밸런서 

한 대의 서버로 감당하기 힘든 트래픽 !! 이대로 납두시겠습니까??

서버의 성능을 증가하는 방식으로는 

### `Scale-Up`과 `Scale-Out`이 있는데...

`Scale-Up`은 수직적 확장으로 서버의 퍼포먼스를 증가하는 것이다

`Scale-Out`은 수평적으로 확장해서 서버들의 퍼포먼스 총량을 증가시키는 것 !

---

### CPU 발전의 변천사 처럼... 

단일 코어에 대한 처리량을 증가하는데 한계가 있기 때문에 

코어의 개수로 승부를 보기 시작함...

서버도 이와 같다

한 개의 서버의 성능을 업그레이드 하는 것 보다  
여러 대의 서버를 묶어서 처리하는 것이 더 비용이 저렴하기 때문에 여러개로 묶어서 처리한다! 

이것이 로드 밸런서가 있는 이유 !!

한 대의 WAS가 처리하기 힘든 트래픽!! 분산해서 처리하자

> 이것이 로드 밸런서!

`로드 밸런싱`: 요청(`Load`)을 여러 대의 서버로 나누어(`Balancing`) 처리하는 것을 의미한다. 

![image](https://user-images.githubusercontent.com/66164361/193551435-41e0e9ee-c727-489a-8ebe-0f29cd390c60.png)

### L4 / L7

- L4: 물리적으로 스위치 등의 장비로 부하를 분산하는 방식
  - 단순히 IP, PORT 번호 등 정보를 바탕으로 부하를 나누기 때문에 L7에 비해 빠르다

- L7: 소프트웨어적으로 `NginX`등의 앱을 이용해서 부하를 분산하는 방식
  - IP, PORT 뿐만 아니라 HTTP 헤더 정보 등을 이용해서 섬세한 라우팅이 가능하다
  - 따라서 대신 느리다는 단점이 있다.

### 부하 분배하는 알고리즘

- `Round Robin` (순차방식)
  - 요청을 순서대로 WAS에 균등하게 분배하는 방식  
    서버의 현재 열결된 커넥션 수나 응답시간에 상관없이 모든 서버를 동일하게 처리, 
    다른 알고리즘에 비해서 가장 빠르다 (가장 단순하기 떄문 !)

- `IP Hash Method` (IP 해시 방식)  
  시용자의 IP를 해싱해서 로드를 분배하기 때문에 사용자가 항상 동일한 서버로 연결되는 것을 보장한다!

![image](https://user-images.githubusercontent.com/66164361/193556707-c2a0aa05-ac3a-4def-9fc3-f88bc7efd96e.png)

- `Least Connection` (최소접속방식)  
  - 서버에 연결되어 있는 Connection 개수만 갖고 단순비교하여 가장 적은곳에 연결
  - 성능이 `RR`보다 안 좋다!
  - Since it is non-deterministic, the least connections load balancer is difficult to troubleshoot.
    - 비 결정적이기 때문에 장애시 원인 파악이 어려울 수 있음
  - This algorithm for the least connections load balancing technique is complex and requires more processing.
    - 기술적으로 좀 더 복잡하고 많은 처리를 요구함

![image](https://user-images.githubusercontent.com/66164361/193557850-e82029bf-2bd5-48c9-865b-7052761c651d.png)

- `Weighted` RR / LC 
  - 가중치를 두어서 로드밸런싱을 할 수 있다
  - ![image](https://user-images.githubusercontent.com/66164361/193558194-dc16cf77-8381-4795-b657-878466d61f5c.png)  
  - ```With this configuration, every 5 new requests will be distributed across the application instances as the following: 3 requests will be directed to srv1, one request will go to srv2, and another one — to srv3.```
  - `3:1:1` 로 요청이 나뉜다 !

### 로드밸런서가 죽는 다면...?

- 로드밸런서가 죽는 경우 서비스가 아예 장애가 되는데...
  - SPOF: Single Point of Failue
    - 특정 포인트 에러시 전체 시스템이 다운이 되는 경우!
  - Master -> Slave ^^
  - 평상시 Replication (`Slave`)을 떠서 둔다 !
    - 그리고 Master가 장애가 나면 Slave를 Master화한다 

# 참고

- https://www.youtube.com/watch?v=9_6COPOMZvI  
- https://tecoble.techcourse.co.kr/post/2021-11-07-load-balancing/  
- https://www.educative.io/answers/what-is-the-least-connections-load-balancing-technique  
- https://www.youtube.com/watch?v=kYipnodgi2I&t=374s  
