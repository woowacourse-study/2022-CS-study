# 도커의 장점

![image](https://user-images.githubusercontent.com/66164361/188338338-2523e65f-0854-43bd-8452-60870f0ac5ca.png)

## 가상화 - Hypervisor

![image](https://user-images.githubusercontent.com/66164361/188338650-22a27541-87cf-48dd-88bc-a95f55499788.png)


하나의 `물리적인 OS`에서 `여러 개의 논리적 OS`를 실행할 수 있게 하는 소프트웨어 아키텍쳐

예) Virtual Box, VM Ware

내부에 완벽한 운영체제를 만들기 때문에, 호스트의 자원을 굉장히 많이 소비하게 된다

(CPU, 램, 하드 모든 자원!)

## 컨테이너 기반의 가상화

프로세스 단위의 격리 환경  

Hypervisor와 다르게 OS를 띄워서 프로그램을 실행하는 것이 아닌 프로그램 자체만을 실행한다

OS가 필요하지 않으므로 이미지 자체의 크기도 줄어들며, 이에 따라 이미지를 실행하는 시간, CPU 자원 소모도 줄어들게 된다


## 도커

![image](https://user-images.githubusercontent.com/66164361/188338630-58541e1e-30ef-4a4d-a7ce-281df43539c0.png)

```
In 2013, Docker introduced what would become the industry standard for containers. Containers are a standardized unit of software that allows developers to isolate their app from its environment, solving the “it works on my machine” headache. For millions of developers today, Docker is the de facto standard to build and share containerized apps – from desktop, to the cloud. We are building on our unique connected experience from code to cloud for developers and developer teams.

## 구글 번역
2013년 Docker는 컨테이너의 산업 표준이 될 것을 도입했습니다. 컨테이너는 개발자가 환경에서 앱을 분리하여 "내 컴퓨터에서 작동" 문제를 해결할 수 있도록 하는 표준화된 소프트웨어 단위입니다. 오늘날 수백만 명의 개발자에게 Docker는 데스크톱에서 클라우드에 이르기까지 컨테이너화된 앱을 구축하고 공유하는 사실상의 표준입니다. 우리는 개발자와 개발자 팀을 위해 코드에서 클라우드에 이르는 고유한 연결된 경험을 기반으로 합니다.
```

13년에 릴리즈된, 컨테이너 기반 가상화 기술, 너무나도 유명해져서 사실상 컨테이너 앱 기술 표준이 되었다  

![image](https://user-images.githubusercontent.com/66164361/188339129-ddbcff32-3157-43af-9dee-d13ae4d5f46e.png)

![image](https://user-images.githubusercontent.com/66164361/188340323-e963990e-fbaf-4193-800a-89cc41534fd6.png)

- 이해를 돕기 위해 각기 다른 이미지를 가져와 보았다!

```plain
A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. 
A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

## 구글 번역
컨테이너는 애플리케이션이 한 컴퓨팅 환경에서 다른 컴퓨팅 환경으로 빠르고 안정적으로 실행될 수 있도록 코드와 모든 종속성을 패키징하는 소프트웨어의 표준 단위입니다. 
Docker 컨테이너 이미지는 코드, 런타임, 시스템 도구, 시스템 라이브러리 및 설정과 같이 애플리케이션을 실행하는 데 필요한 모든 것을 포함하는 가벼운 독립 실행형 실행 소프트웨어 패키지입니다.
```

- 의역
`컨테이너`란 한OS에서 앱을 다른 OS에서 실행하기 위한 것
컨테이너 `이미지`란 앱을 실행하기 위한 것이 담겨져 있는 것

```
Container images become containers at runtime and in the case of Docker containers – images become containers when they run on Docker Engine. Available for both Linux and Windows-based applications, containerized software will always run the same, regardless of the infrastructure. Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging.

## 구글 번역
컨테이너 이미지는 런타임에 컨테이너가 되며 Docker 컨테이너의 경우 이미지가 Docker Engine에서 실행될 때 컨테이너가 됩니다. 
Linux 및 Windows 기반 애플리케이션 모두에서 사용할 수 있는 컨테이너화된 소프트웨어는 인프라에 관계없이 항상 동일하게 실행됩니다. 
컨테이너는 소프트웨어를 환경에서 격리하고 예를 들어 개발과 스테이징 간의 차이에도 불구하고 균일하게 작동하도록 합니다.
```

- 의역
컨테이너 `이미지`는 실행시 컨테이너가 된다. 프로그램을 컨테이너에 띄우면 어떤 OS이건(Win, Mac, Linux) 동일하게 실행할 수 있다.



## 도커의 장점

```
Standard: Docker created the industry standard for containers, so they could be portable anywhere

어디에서나 휴대용으로 사용할 수 있다

Lightweight: Containers share the machine’s OS system kernel and therefore do not require an OS per application, driving higher server efficiencies and reducing server and licensing costs

앱당 OS를 요구하지 않으므로 경량화되어있다, 즉 앱 하나 당 OS를 또 필요로 사용하지는 않는다


Secure: Applications are safer in containers and Docker provides the strongest default isolation capabilities in the industry

컨테이너와 격리되어 있기 때문에 안전하다 (??...)
```

### 기존 가상화에 비해 자원 사용이 적다
OS를 포함하지 않은 이미지를 만들므로, 용량 자체가 작고 실행하는 속도도 빠르다


### 실행환경 구성이 빠르고 간단하다
MySQL을 GUI에서 설치하려면 여러가지 절차들이 필요하다... 그러나 Docker 위에서 설치하면 절차가 간소화되어 있다

또한 지울 때도 편하다!

예를들어 특정 버전의 MySQL 문제가 있거나, 정상 작동이 안되어 재설치를 하고 싶을 경우 MySQL 프로그램을 삭제해야 하는데 이것을 Windows에서 하게 된다면 은근히 번거롭다

- MySQL을 Service에서 종료
- 5가지가 넘는 서브 프로그램들 제거
- MySQL로 인해 생겨난 폴더들 제거
- 레지스트리 제거

도커에서는 제거하는게 훨씬 간편하다!
또한 셋업할 때는 기존의 이미지를 활용할 수 있다, 혹은 제거하지 않아도 컨테이너 stop후 start할 수 있음.

따라서 MySQL, Oracle, Postgres 등등 설치하고 부수기 좋아!

### Docker-Compose를 이용하면 더더욱 쉽게 설치 과정을 간소화할 수 있다

참고로 docker-compose는 별도로 설치해야 한다 !

아래는 간이 블로그 wordpress를 설치하는 것을 기술한 설정 파일이다

wordpress는 db를 필요료 한다

db를 설치한 후에 wordpress를 설치하는 설정 파일이다

```yaml
// 첫 줄에 버전을 명시함
version: "3.7"

// 실행할 로직을 services에 묶음
services:
  // 컨테이너 이름
  db:
  	// 사용할 이미지
    image: mysql:5.7
    // 산출물을 저장하는 경로를 지정
    volumes:
      - ./db_data:/var/lib/mysql
    restart: always
    // 필요한 환경 변수
    environment:
      MYSQL_ROOT_PASSWORD: 1234
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: 1234
  
  app:
    depends_on: 
      - db
    image: wordpress:latest
    volumes:
      - ./app_data:/var/www/html
    // 8080 으로 접속 시 80 포트와 연결
    ports:
      - "8080:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: 1234
```

이 내용만 있으면 설치 과정을 자동화하여 개발자들에게 배포할 수 있는 것 !

### Docker 컨테이너들간의 자원 사용을 매니징할 수 있다 (더 나아가선 쿠버네티스)

![image](https://user-images.githubusercontent.com/66164361/188340703-fc5ed8bb-8256-4451-9104-ef439e92bb89.png)


![image](https://user-images.githubusercontent.com/66164361/188340521-ba56e100-e045-4726-b94e-123af59f2319.png)

도커 스웜을 이용해서 여러개의 컨테이너를 띄워서 (`클러스트링`) 자원을 분산(`로드 밸런싱`)한다. (`스케일 아웃`)


## 이슈 (도커의 단점)

### 특정 메인보드의 펌웨어가 가상화를 지원하지 않을 경우

한성 노트북에서 가상화 지원 문제로, 도커 셋업시 윈도우가 부팅되지 않는 현상이 있었음
원인을 알기까지 3일
이 증상을 겪은 사람은 한성 제조사를 사용하고 있었던 필즈, 짱구는 미션 진행중에 상당한 고통을 겪음...


### 윈도우 WSL2와의 호환
WSL2 (윈도우의 서브 윈도우)에서 사용시 메모리 누수가 있다. 몇 년동안 해당 이슈가 닫히지 않았음...

### `ARM` Architecutre와의 호환

우선 docker설치부터가 잘 안 되며, 설치가 되더라도 오동작... docker-compose는 설치 자체가 잘 안 된다...

`ARM`에서 도커를 구동시 정말 눈물나는 경험을 할 수 있다...
분명 내가 셋업한 곳은 `Devian`계열 Linux(`Ubuntu`)인데, 도커 내부 컨테이너의 `OS`는 `Red Hat` 계열임(`Oracle`)...


참고 `Devian` - `apt-get`란 명령어를 통해 설치
`Red Hat` - `yum`을 이용해 설치...
그리고 컨테이너 위의 `Oracle Linux`는 Vim등의 text editor가 없어서 설치를 하고 시작해야 함...



## 출처

- 생활코딩 - 도커 시리즈

- 노마드 코더 - 도커

- 검프 - 도커 이론편
