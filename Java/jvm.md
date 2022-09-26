# JVM

> 누구든 JVM의 규격(`Specification`)을 만족하는 '무언가'를 만든다면,  
> 그 `무언가`는 `JVM`이다.  

- 사실 JVM 자체는 구현체가 아닌 하나의 표준이다 !  
- 이를 구현한 Vendor사는 오라클, IBM, azul등 이 있다.  

```
The Java Virtual Machine is the cornerstone of the Java platform. It is the component of the technology responsible for its hardware-and operating system-independence, the small size of its compiled code, and its ability to protect users from malicious programs.

Java Virtual Machine은 Java 플랫폼의 초석입니다. 하드웨어 및 운영 체제와 독립적으로 가집니다.
```

사실 저기 있는 `무언가` 는 `SW`뿐만 아니라 **`HW`** 도 될 수 있다~!!

![image](https://user-images.githubusercontent.com/66164361/192105302-66190fcb-6700-482a-adcc-b86deca96140.png)

---

![image](https://user-images.githubusercontent.com/66164361/192105334-88f7d890-bdc1-42cb-b158-88243481ca13.png)

> 의외로 `JIT`랑 `GC`가 그림에 없다 !

```
To implement the Java Virtual Machine correctly, you need only be able to read the class file format and correctly perform the operations specified therein.

Java 가상 머신을 올바르게 구현하려면, 당신은 단지 class 파일 형식을 읽고 스펙에 지정된 작업을 올바르게 수행할 수만 있으면 됩니다.

Implementation details that are not part of the Java Virtual Machine's specification would unnecessarily constrain the creativity of implementors. 

현 JVM 규격에 명시되어 있지 않ㄴ은 구현과 관련된 상세 사항들은 구현자의 창의성을 저해하는 불필요한 제약이 될 수 있습니다.

For example, the memory layout of run-time data areas, the garbage-collection algorithm used, and any internal optimization of the Java Virtual Machine instructions (for example, translating them into machine code) are left to the discretion of the implementor.

예를 들자면, 데이터 영역의 메모리 레이아웃이나 Garbage-Collection에 사용되는 알고리즘, 그리고 JVM 명령어 실행과 관련된 내부의 최적화 (e.g. 기계어로 번역하는 과정)에 관한 것들은 구현자의 재량으로 남겨두겠습니다.
```

--- 

## JVM이 왜 있어야 할까?

C/C++는 컴파일 플랫폼과 타겟 플랫폼이 다를 경우, 프로그램이 동작하지 않는다.  
즉 윈도우에서 개발한 테트리스 게임을 맥북으로 끌고 오면 동작하지 않는다 !!

> 플랫폼: OS + CPU Architecture

OS마다 system call interface가 다르고  
CPU 아키텍처마다 지원하는 instruction 아키텍쳐가 다르다.

```
WORA(Wirte Once, Run Any Where) - Sun Microsystems

네가 짠 자바 코드를 컴파일해서 배포하면, 어떤 플랫폼이든 다시 컴파일할 필요 없이 실행시킬 수 있어! 
근데 실행하려면 그 플랫폼에 맞는 JVM이 설치되어 있어야 해!
```

## JVM Memory Feature

- Method Area: 클래스에 있는 메타 정보가 있음. (클래스 이름, 부모 클래스 누군지, 메서드, 필드 정보 등)
- Heap: 동적으로 인스턴스를 생성한 객체 정보가 여기 있다.
- Java Stacks: 지역적으로 실행되는 정보들이 쌓인다, 로컬변수, 메서드 호출할 때 정보 등, 쓰레드마다 있는 스택이고 메서드 호출 정보를 쌓는다. 쓰레드를 종료하면 해당 스택도 사라진다. 
- PC Registers: 쓰레드마다 실행할 때 당시의 `instruction`의 위치를 가리키는 값이 생성되는 곳.
- Native Method Stacks: `Native Method`를 호출하는 코드를 실행되는 위한 스택

![image](https://user-images.githubusercontent.com/66164361/192259307-0f97db70-cdba-4428-bb77-314249632cbe.png)


### Native Method Stacks

```
An implementation of the Java Virtual Machine may use conventional stacks, colloquially called "C stacks," to support native methods 
(methods written in a language other than the Java programming language). 

Java 가상 머신의 구현은 네이티브 메소드를 지원하기 위해 구어체로 "C 스택"이라고 하는 기존 스택을 사용할 수 있습니다.  
(Java 프로그래밍 언어 이외의 언어로 작성된 메소드).  



Native method stacks may also be used by the implementation of an interpreter for the Java Virtual Machine's instruction set in a language such as C. 

Native Method Stacks 또한 JVM의 인터프리터 구현체를 사용할 수 있다. C의 명령어 집합 처럼.
```

### PC Register

```
If that method is not native, the pc register contains the address of the Java Virtual Machine instruction currently being executed. 

해당 메소드가 기본이 아닌 경우 pc 레지스터에는 현재 실행 중인 JVM(Java Virtual Machine) 명령의 주소가 포함됩니다.



If the method currently being executed by the thread is native, the value of the Java Virtual Machine's pc register is undefined.

스레드가 현재 실행하고 있는 메소드가 원시인 경우 JVM(Java Virtual Machine)의 pc 레지스터 값은 정의되지 않습니다.
```

## JVM Execution

- `인터프리터`: 동적으로 바이트 코드를 한 줄씩 컴파일하여 실행.(바이트 -> 바이너리)
- `JIT 컴파일러`: 인터프리터가 반복하여 컴파일하는 코드를 발견하면 네이티브(바이너리) 코드로 바꾸어 미리 컴파일 해두어 캐시에 저장해준다!. 그러면 다음부터 캐시에 있는 코드를 바로 사용한다.
- `GC`(가비지 컬렉터): 더 이상 참조되지 않는 객체를 모아서 정리한다.
- `JNI`(네이티브 메서드 인터페이스): 자바 애플리케이션에서 C, C++, 어셈블리로 작성된 함수를 사용할 수 있게 해준다.
- `네티이브 메서드 라이브러리`: C, C++로 작성된 라이브러리 (JNI의 구현체)
- `클래스 로더` - 클래스들을 동적으로 가져오는 역할을 한다!
로딩, 링크, 초기화


### 클래스 로더

- `.class` 는 각각의 디렉터리에 흩어져있다.  
- 각각의 클래스 파일을 찾아서 JVM 메모리에 올려주는 역할을 한다

- 과정
  - `로딩`: 클래스를 읽어 옴
  - `링크`: 레퍼런스를 연결함
  - `초기화`: static 값들 초기화 및 변수에 할당

- 클래스 로더의 가장 큰 특징은 클래스들을 한번에 로딩하지 않고, 런타임에 동적으로 JVM으로 로딩한다는 것이다.

> G1GC

![image](https://user-images.githubusercontent.com/66164361/192253888-d2e9c6b8-11d2-4259-9d5b-af79cc12bd79.png)


# 참고
> https://github.com/binghe819/TIL/blob/master/JAVA/JVM/jvm_structure.md  
> [10분 테코톡] 🎹 김김의 JVM Specification  https://www.youtube.com/watch?v=6reapO0gLPs  
> [10분 테코톡] 어썸오의 JVM Memory Layout  https://www.youtube.com/watch?v=GU254H0N93Y  
> [10분 테코톡] 🎅무민의 JVM Stack & Heap  https://www.youtube.com/watch?v=UzaGOXKVhwU  
> extension://bfdogplmndidlpjfhoijckpakkdjkkil/pdf/viewer.html?file=https%3A%2F%2Fcr.openjdk.java.net%2F~iris%2Fse%2F17%2FlatestSpec%2Fjava-se-17-jvms-fr-diffs.pdf  
> https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html  
