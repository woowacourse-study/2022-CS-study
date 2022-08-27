
# 🗑 GC (garbage collection)  

## 가비지 컬렉션 aka 쓰레기 수집  

![image](https://user-images.githubusercontent.com/80666066/187005140-169afe38-f314-49a8-810c-cef95bb17919.png)

- 개념  
  - [메모리 관리](https://ko.wikipedia.org/wiki/%EB%A9%94%EB%AA%A8%EB%A6%AC_%EA%B4%80%EB%A6%AC) 기법 중 하나  
  - 프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게 된 영역을 해제하는 기능  
  - C, C++은 수동 메모리 관리를 가정하고 설계함
    - 하지만, 지원하는 구현도 존재한다  
  - java, javascript, C#등은 언어 정의에서 지원함  

- ☕️ 자바의 GC
  - `Heap` == 프로그램이 동적으로 할당했던 메모리 영역 
  - `어떤 변수도 가리키지 않는 영역` == 필요없게 된 영역
  - 즉, `Heap`에서 `어떤 변수도 가리키지 않는 영역`의 메모리를 해제하는 작업  

- 장단점
  - 👍 장점  
    - 유효하지 않은 포인트, 즉 이미 해제된 메모리 접근을 막는다  
    - 해제된 메모리를 또 다시 해제하는 일을 방지한다  
    - [메모리 누수](https://ko.wikipedia.org/wiki/%EB%A9%94%EB%AA%A8%EB%A6%AC_%EB%88%84%EC%88%98)를 막는다  

  - 👎 단점 
    - 비용이 든다, 애플리케이션에 써야 할 자원이 소모된다  
    - GC가 실행되는 시점을 프로그래머가 알 수 없다, 프로그램이 일시정지하는 걸 예측할 수 없다  

<br>

## 가비지 컬렉션의 두 기법  

### ⏲ Reference Counting  

<img width="865" alt="image" src="https://user-images.githubusercontent.com/80666066/187005591-78b249d9-eb9c-49fa-8eb8-79f05e6c8361.png">

- Root Space란?
  - JVM의 Method Area + 각 쓰레드의 stack, native method stack 영역  

- `Heap`에 선언 된 변수는 각각 `Reference Count` 라는 숫자를 갖고 있음  
  - `Reference Count`란 각 변수에 접근할 수 있는 방법의 가짓수  
- 해당 숫자가 0이 되면 메모리 해제  

<img width="865" alt="스크린샷 2022-08-27 오전 9 04 43" src="https://user-images.githubusercontent.com/80666066/187005918-fc8bc0fc-8748-438e-9c3a-3ab76442491b.png">

- 화살표의 참조가 끊어진다면?  
  - 오른쪽 변수들은 `root space`에서 접근 할 방법이 사라짐
  - 그럼에도 서로 참조해서 `reference count`가 0이 되지 않음
  - **메모리 누수** 발생!

### ✅ Mark And Sweep 🔁  

<img width="876" alt="스크린샷 2022-08-27 오전 9 13 19" src="https://user-images.githubusercontent.com/80666066/187006072-4bfc893e-8eeb-4ed8-af1c-8d93770c3bfa.png">

- `root space`에서 그래프 탐색으로 변수에 접근 가능한지 판단  
  - 접근 불가해진 메모리를 해제  
  - 바로 위의 순환 참조 문제를 해결할 수 있다  

- mark and sweep의 단점?  
  - `reference counting`은 그냥 숫자가 0이 되는 순간 지우는 반면, 의도적으로 GC를 실행시켜야 한다  
  - 어플리케이션과 GC 실행이 병행되어 `Stop The World` 발생  

- 🚥 Stop The World ?  
  - 가비지 컬렉션 실행을 위해 `JVM`이 애플리케이션을 멈추는 작업  
  - GC를 실행하는 쓰레드를 제외한 모든 쓰레드의 작업이 일시 중단  
  - GC 성능 튜닝은 주로 이 `stop the world` 시간을 줄이는 것  

☕️ 자바는 `mark and sweep`을 사용한다  

<br>

## 자바의 GC  

### generational collection 기법

![image](https://user-images.githubusercontent.com/80666066/187006800-6c1185e2-57db-4a88-9a47-3d822a4369c7.png)

- 객체 수명의 일반적 분포(Typical Distribution for Lifetimes of Objects) 그래프  
  - x축: 객체의 수명(할당된 바이트 단위)
  - y축: 해당 수명을 가진 객체의 총 바이트  
- 수명이 짧은 객체의 수가 압도적으로 많고, 수명이 긴 객체의 수는 매우 적다  
- 많은 애플리케이션이 이런 형태를 띄고 있다  
- 효과적인 관리를 위해 수명이 짧은 객체를 `young generation`, 긴 객체를 `old generation`으로 나눠 관리  

## JVM에서 GC가 일어날 때    

<img width="939" alt="image" src="https://user-images.githubusercontent.com/80666066/187006727-f123535d-89f9-4377-ad63-7134b017fcf3.png">

- young generation
  - Eden
    - 새롭게 생성된 객체가 할당  
    - `minor gc`가 일어남  
  - Survival
    - `minor gc`에서 살아난 객체들이 저장  
    - 둘이 번갈아 사용되며, 둘 중의 하나는 반드시 비어있다  

<img width="939" alt="image" src="https://user-images.githubusercontent.com/80666066/187007124-2e691d67-a03f-4027-97a7-45c70335755e.png">

- 새로운 객체가 생성되다 보면 `Eden`이 꽉 차는 순간이 온다  
- 이때 `mark and sweep`으로 접근 불가능한 객체의 메모리를 해제  

<img width="939" alt="image" src="https://user-images.githubusercontent.com/80666066/187007221-0985f7ab-afbf-409d-959f-310244ea734b.png">

- 살아남은 객체는 `Survival` 영역으로 이동  
- 또 다시 `Eden`이 꽉 차는 순간이 오면 `minor gc` 실행  

<img width="939" alt="image" src="https://user-images.githubusercontent.com/80666066/187007300-babbf7da-d134-44bb-97fa-7639592dcf7e.png">

- 이렇게 실행되다 보면 `age-bit`가 증가  
- `age-bit`가 특정값에 도달하면 `old generation`으로 이동  
  - 이 이동을 `Promotion`으로 지칭  

<img width="939" alt="image" src="https://user-images.githubusercontent.com/80666066/187007375-c5b68a3d-17b7-4001-b75f-e2412bebce58.png">

## JVM에서 GC가 일어나는 방식  

### CMS GC(Concurrent Mark Sweep GC) ~java8  

- `Stop The World` 시간을 줄이기 위한 방식  
- `root space`에서 객체를 탐색할 때, `4 step`으로 나눠서 탐색  

![image](https://user-images.githubusercontent.com/80666066/187007488-f582c268-b33e-4130-b754-64e67ae880fe.png)  

- `4 step`?  
  - Initial Mark: GC Root가 참조하는 객체만 마킹 (stop-the-world 발생)  
  - Concurrent Mark: 참조하는 객체를 따라가며, 지속적으로 마킹. (stop-the-world 없이 이루어짐)  
  - Remark: concurrent mark 과정에서 변경된 사항이 없는지 다시 한번 마킹하며 확정하는 과정. (stop-the-world 발생)  
  - Concurrent Sweep: 접근할 수 없는 객체를 제거하는 과정 (stop-the-world 없이 이루어짐)  

- 단점  
  - 메모리와 CPU를 많이 사용  
  - `mark and sweep`이후 메모리 파편화를 제거하지 않음  

<br>

### G1 GC (Garbage Frist GC) java9~  

<img width="964" alt="image" src="https://user-images.githubusercontent.com/80666066/187007766-ba5382bb-fdd9-4520-9868-1138c7ed9694.png">

- heap을 region으로 나눠 사용  
  - 각 region은 `young generation`, `old generation`을 유동적으로 사용
  - 런타임에 영역별 개수가 튜닝된다  
- `stop the world` 시간이 더 짧다  

<br>

## 도움받은 곳들  

- [JAVA GC](https://code-factory.tistory.com/48)  
- [Java GC 튜닝](https://johngrib.github.io/wiki/java-gc-tuning/)  
- [[10분 테코톡] 🤔 조엘의 GC
](https://youtu.be/FMUpVA0Vvjw)  
