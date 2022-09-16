
# 🌐 Socket & HTTP  

## Socket 통신  

![image](https://user-images.githubusercontent.com/80666066/188340305-62322623-b243-42e4-a075-a1f043510b30.png)

### 소켓이란
- 그림 상의 `Socket Layer`  
- 네트워크에서 데이터를 주고받는 프로그램 각 양끝의 데이터 출입구(=엔드포인트)
- `TCP/IP`를 이용하는 `인터페이스`  
- `IP`와 `port` 넘버가 필요
  - 우리는 이미 소켓을 이용해 통신하고 있었다!  
- 통신 역할에 따라 클라이언트 소켓, 서버 소켓으로 나뉜다  

### 소켓 특징
- `TCP/UDP`위에서 동작  
- 양쪽에서 데이터 송수신 가능
  - `server-client`구조이나 역할이 매번 바뀜  
- 프로그래밍 언어나 운영체제에 종속적
  - `socket`은 효준이 아닌 네트위크 프로그래밍 `인터페이스`  

<br>

## 통신 과정  

![image](https://user-images.githubusercontent.com/80666066/188340732-cafb3bc3-4163-420b-8d61-ae8b6a9f60fc.png)

**서버**

- `socket()`으로 소켓 생성
- `bind()`로 `ip`와 `port`번호 설정  
- `listen()`으로 요청 수신 대기열 초기화  

**클라이언트**

- `socket()`으로 소켓 생성 
- `connect()`로 연결할 소켓의 `ip`와 `port`번호 지정 후 연결 시도 

**서버**

- 서버가 `accept()`로 클라이언트의 `socket descriptor` 반환  
  - `descriptor`: 각 소켓에 할당된 식별값  

**서버&클라이언트**

- 성공 시 `read()/write()` 통신  
- `close()`로 소켓을 닫을 때 까지 `accept(), read()/write()`반복  

<br>

## TCP/UDP

### TCP(Transmission Control Protocol)  

![image](https://user-images.githubusercontent.com/80666066/188342269-315aed5b-34f0-4c3b-bc99-e45186d91baa.png)

- 연결형, 가상 회선 방식  
- 오류 수정, 전송 처리, 흐름제어 보장  
- 송신 순서에 따라 중복되지 않게 데이터를 수신  
- 높은 신뢰성  
- `UDP`보다 속도가 느림  
- `Point to Pont` 방식  

### UDP(User Datagram Protocol)  

![image](https://user-images.githubusercontent.com/80666066/188342471-981a493e-fccb-45a0-9837-5d3332d6dfe6.png)

- 비연결형, 데이터그램 방식  
- 최소한의 오류만 검출
- 신뢰성 낮음
- `TCP`보다 빠른 속도  
- `connect()` 함수가 불필요해 서버, 클라이언트 구분이 없다  

<br>

## 그래서 HTTP는  

### TCP를 사용하는 소켓통신  

> " 1990년대 초에 설계된 HTTP는 거듭하여 진화해온 확장 가능한 프로토콜입니다. HTTP는 애플리케이션 계층의 프로토콜로, 신뢰 가능한 전송 프로토콜이라면 이론상으로는 무엇이든 사용할 수 있으나 TCP 혹은 암호화된 TCP 연결인 TLS를 통해 전송됩니다. "  
> " 연결은 전송 계층에서 제어되므로 근본적으로 HTTP 영역 밖입니다. HTTP는 연결될 수 있도록 하는 근본적인 전송 프로토콜을 요구하지 않습니다; 다만 그저 신뢰할 수 있거나 메시지 손실이 없는(최소한의 오류는 표시) 연결을 요구할 뿐입니다. 인터넷 상의 가장 일반적인 두 개의 전송 프로토콜 중에서 TCP는 신뢰할 수 있으며 UDP는 그렇지 않습니다. 그러므로 HTTP는 연결이 필수는 아니지만 연결 기반인 TCP 표준에 의존합니다."  
> [mdn web docs](https://developer.mozilla.org/ko/docs/Web/HTTP/Overview)  

- `HTTP`는 애플리케이션 계층의 프로토콜  
  - 기본적으로 `HTTP`만으로 통신(데이터 송수신)을 할 수 없다  
  - 하위 계층에 의존하는데, 이게 `TCP/TLS`  
  - `HTTP`를 사용하려면? -> `TCP/TLS`를 써야하는데, 얘네를 쓰려면? -> 소켓을 사용해야 함  
  - `HTTP`사양에 따라 데이터를 전송하기만 하면 된다  

<br>

## 도움받은 곳들 

- [oracle solaris 문서](https://docs.oracle.com/cd/E19120-01/open.solaris/817-4415/sockets-18552/index.html)  
- [stackoverflow: difference between socket programming and Http programming](https://stackoverflow.com/questions/15108139/difference-between-socket-programming-and-http-programming)  
- [on1ystart: 소켓 프로그래밍 - 소켓의 이미/특징/종류](https://on1ystar.github.io/socket%20programming/2021/03/16/socket-1/)  
- [망나니개발자: [TCP/UDP] TCP와 UDP의 특징과 차이](https://mangkyu.tistory.com/15)   
- [bitcodic on the bit: HTTP는 소켓 통신이에욧!](https://bitcodic.tistory.com/151)  

