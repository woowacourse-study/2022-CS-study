> 히히님의 OSI 7 Layer를 보고 요약한 내용입니다... :)

# Physical Layer

## 컴퓨터가 통신한다 ...?

0과 1을 주고 받으면 된다!

그러나 실세계의 데이터는 저런 정보가 아닌 전봇대 전선을 타고 아닌 아날로그 데이터값이 오고 간다.

따라서 이것을 해석하는게 필요함.

송신자는 0과 1의 나열을 아날로그로 바꾸고 (`encoding`)

수신자는 그것을 다시 0, 1로 해석을 한다 ! (`decoding`)

PHY칩에 `하드웨어`적으로 구현되어 있다!

# Data-Link Layer

## 여러 대의 컴퓨터가 통신하려면...?

![image](https://user-images.githubusercontent.com/66164361/184459039-63998c7b-a5d8-4789-92bc-cb72878ba76e.png)

한 컴터에 전선을 여러대 꼽아야 한다!

![image](https://user-images.githubusercontent.com/66164361/184459232-a9edc4eb-b623-43c1-b40a-e705d43743b7.png)

(선도 많이 들어가게 된다!)

![image](https://user-images.githubusercontent.com/66164361/184459136-8e0426c3-63d7-4ee5-8be6-aac9c0b09300.png)

그래서 위와 같이 하나의 거대한 전선을 만들어서 내부에 넣는다

![image](https://user-images.githubusercontent.com/66164361/184459283-ab5547d5-3cc9-4d66-9fa3-390ffa05d3ac.png)

위와 같은 그림을 허브라는 박스에 감추어 넣는다!

그러나 문제는 다른 컴퓨터들도 내가 보낼 메세지를 다 읽게 된다

메시지의 목적지만을 확인해서 대상 Device에게 전달할 수 있는 것을 `스위치`라고 한다

## 서로 다른 네트워크끼리 통신?

![image](https://user-images.githubusercontent.com/66164361/184459677-71698ba2-bc8a-497f-b0ab-1a1f59d28aa2.png)

서로 다른 네트워크에 속한 컴퓨터끼리 통신이 가능하게 해주는 장비를 라우터라고 한다

엄밀히는 L3 스위치 (스위치 + 라우터)라고 함

ex) 공유기

![image](https://user-images.githubusercontent.com/66164361/184459813-185002ee-e23f-466b-bd84-c454c2afbb3f.png)

## 다시 여러대 컴퓨터가 통신한다면...?

![image](https://user-images.githubusercontent.com/66164361/184459917-a29c5ca9-7fa6-4d82-a685-1341de380383.png)

시작과 끝에 임의의 비트열을 붙여서 통신한다! (Fraiming)

- 시작: 1111
- 끝: 0000

## 그래서 Data-Link Layer란?

- 같은 네트워크 상에 여러대 Device끼리 데이터를 주고 받기 위해 필요한 계층

- Framing은 Data Layer에 속하는 작업들 중 하나 !

# Network Layer

![image](https://user-images.githubusercontent.com/66164361/184460081-51c760f1-83ca-4d3b-9804-6d7d2b8f2735.png)

IP 주소를 통해서 상대를 식별하여 데이터를 전송한다

목적지 주소를 찾아가는 과정을 라우팅이라고 한다!

## Network Layer 란?

- inter-network 속에서
- 목적지 Device로 데이터를 전송하기 위해
- IP 주소를 이용해서 길을 찾는 것 ! (`routing`)

## 구현되어 있는 곳

- 운영체제의 커널에 소프트웨어적으로 구현되어 있다!

# Transport Layer

![image](https://user-images.githubusercontent.com/66164361/184460918-3b057ef5-0c68-4c2c-b539-afc13be49304.png)

컴퓨터 안에서도 여러 개의 프로그램이 있다면...?

프로그램(정확히는! 프로세스)끼리 데이터를 주고 받으려면?

원격에서 어떤 특정 프로그램에 데이터를 주고 받으려면?

그래서 Port Number라는 개념이 도입이 되었다!

![image](https://user-images.githubusercontent.com/66164361/184461007-dea6e254-6960-4bac-99db-e13da24dab07.png)

## 구현되어 있는 곳?

- Network Layer와 마찬가지로 운영체제의 커널에 소프트웨어적으로 구현되어 있다!

# Application Layer

- 앞서 있는 것들을 보다 추상화한 계층

- TCP/IP 소켓(네트워크) 프로그래밍
- Transport Layer에서 제공하는 API를 통해서 통신 가능한 프로그램을 만드는 것

ex) HTTP

![image](https://user-images.githubusercontent.com/66164361/184461263-503e9654-bf29-4212-9248-712028e6f4e0.png)

# OSI vs TCP/IP

현대의 시장은 OSI 보다는 TCP/IP !

![image](https://user-images.githubusercontent.com/66164361/184461292-0038a491-6361-44e9-954b-99e05ffe9fd0.png)

![image](https://user-images.githubusercontent.com/66164361/184461309-73839773-1c2b-430d-91cd-ad46dbccc55e.png)

현재는 주로 TCP/IP Updated를 사용한다 !

# 참고 자료

> https://www.youtube.com/watch?v=1pfTxp25MA8
