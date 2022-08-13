## 프로세스와 스레드  

### 프로세스  

#### 프로세스란?  

![image](https://user-images.githubusercontent.com/80666066/184461169-5d516b2b-a48b-4c49-b11b-fb687d156965.png)

- 메모리에 올라와 실행되고 있는 프로그램의 인스턴스  
- 운영체제로부터 시스템 자원을 할당받는 작업의 단위  

#### 특징  

- 운영체제로부터 각각 독립된 메모리 영역을 할당받는다  
    - 메모리 영역은 [Code, Data, Stack, Heap](https://box0830.tistory.com/150)으로 이루어져 있다  
- 각 프로세스는 별도의 주소 공간에서 실행되기에, 서로 변수나 자료구조에 접근할 수 없다  
- 접근하려면 IPD(inter-process communication)을 사용해야 한다  
    - ex. 파이프, 파일, 소켓 등을 이용  


### 스레드  

#### 스레드란?  

- 프로세스의 특정한 수행 경로  
- 프로세스가 할당받은 자원을 이용하는 실행 단위  

#### 특징  

- 프로세스 내에서 스레드 끼리 Code, Data, Heap 영역울 공유한다  
- 각각의 스레드는 별도의 sp(Stack Pointer), fp(Frame Pointer), pc(Program Counter) 레지스터를 갖고 있다  
    - 이는 프로세스간 [Context Switching](https://velog.io/@jaeyunn_15/CS-Context-Switching)에서 자신이 작업하던 위치를 기억하기 위함이다  
    

### 멀티 프로세스와 멀티 스레드  

#### 멀티 프로세스  

- 하나의 응용프로그램을 여러 개의 프로세스로 구성하여 각 프로세스가 하나의 작업을 처리  
- 여러개의 프로세스 중 하나에 문제가 발생해도, 그 프로세스만 죽고 영향이 확산되지 않는다  
- 크롬이 멀티 프로세스 응용 프로그램이다 (탭 별로 다른 프로세스)  
- 분리된 메모리 영역으로 인한 단점이 있다  
    - Context Switching 과정에서 캐시 메모리 초기화 등 무거운 작업이 진행되고 많은 시간이 든다  
    - 이는 프로세스 사이에서 공유하는 메모리가 없어, 캐쉬 데이터를 모두 리셋하고 다시 불러와야 하기 때문  

#### 멀티 스레드  

- 하나의 응용프로그램을 여러 개의 스레드로 구성하고 각 스레드가 하나의 작업을 처리  

![image](https://user-images.githubusercontent.com/80666066/184461152-732a0f38-df99-4b16-af3c-3030d5b4e909.png)

- 스프링도 멀티 스레드 응용 프로그램  
- 장점  
    - 프로세스를 생성하여 자원을 할당하는 시스템 콜이 줄어들어 자원을 효율적으로 관리 가능  
    - 스레드 간 데이터를 주고받는 것이 간단해져 자원 소모가 줄어듦  
    - Context Switching이 빠름  
    - 스레드 간 프로세스 내 Stack을 제외한 메모리 영역을 공유하므로 통신의 부담이 적다  
- 단점  
    - 디버깅이 까다롭다  
    - 프로세스 밖에서 스레드 각각을 제어할 수 없다  
    - 자원 공유 문제가 발생한다  
    - 하나의 스레드에 문제가 발생하면 전체 프로세스가 영향을 받는다  

<br>

### 스프링부트의 멀티 스레딩  

- 스프링부트는 `서블릿 컨테이너(Tomcat)`가 다중요청을 처리해준다  

![image](https://user-images.githubusercontent.com/80666066/184460645-de2ec000-11f0-4e9a-9167-d1381442365c.png)  

1. 프로세스 시작 시 스레드 컬렉션인 `Thread Pool`을 생성  
2. 유저 요청(HttpServletRequest)가 들어오면 스레드를 하나씩 할당  
3. 해당 Thread에서 유저 요청 처리  
4. 작업이 끝나면 스레드는 스레드풀로 반환  

### 스프링 빈과 스레드 세이프티  

- 스프링 빈은 기본 싱글턴, 스레드 간에 공유된다  
- 따라서 상태값이 있으면 여러 요청에서 변경이 가능함  
- 상태값이 없게 만들어야 스레드 세이프하게 처리된다  
