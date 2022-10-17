
# 𝒇𝒙 함수형 프로그래밍  

## 함수형 프로그래밍(선언형 프로그래밍)  

#### 절차적 프로그래밍  

- `어떻게(how)`에 집중  
- 명령형 프로그래밍이라고도 부른다  

```java
Member vip = members.get(0);

for(Member member : members) {
    if(member.totalOrders() > vip.totalOrders()){
      vip = member;
    }
}
```  

- 공유된 가변 데이터구조를 읽고 갱신한다면, 결국 전체에서 데이터 갱신 사실을 추적하기 어렵다  

#### 함수형 프로그래밍  
- `무엇을(what)`에 집중
- 선언형 프로그래밍이라고도 부른다

```java
Member vip = members.stream()
    .max(comparing(Member::totalOrders());
```
- 원하는 것이 무엇이고, 시스템이 어떻게 그 목표를 달성한 것인지 등의 규칙을 정한다
- 문제 자체가 코드로 명확하게 드러난다는 장점이 있다

- 질의문 구현 방법은 라이브러리가 결정
  - 반복문을 알아서 처리하고 결과 스트림값을 어딘가에 저장해주는 `내부 반복(internal iteration)` 사용

## 함수와 함수형   

- 함수형 프로그래밍의 `함수`란?
  - 수학적인 함수와 동일
  - 즉, 0개 이상의 인수를 가지며, 한 개 이상의 결과를 반환하지만, **부작용이 없어야**한다  
  - 부작용 없음(no side effect)이란?  
    - 부수효과(side effect)가 발생하지 않기 때문에 표현식을 언제 어느때 실행해도 문제가 발생하지 않는다  

![image](https://user-images.githubusercontent.com/80666066/193534579-cf4ffb1e-b1cf-479b-92ae-9b215200819e.png)



- `함수형`이란?  
  - 수학의 함수처럼 부작용이 없는  
  - 순수 함수형
    - 함수 그리고 `if-then-else`등의 수학적 표현만 사용  
  - 함수형  
    - 시스템의 다른 부분에 영향을 미치지 않는다면, 내부적으로는 함수형이 아닌 기능도 사용  
	- 내부적으로는 부작용이 발생해도, 호출자에 아무 영향을 미치지 않는다면 호출자는 신경 쓸 필요가 없다  

## 자바의 함수형  

- 순수 함수형이 아닌 함수형 프로그램이 목표  
  - **부작용 없음**과 **불변성**에 집중한다  
  - 지역 변수만을 변경하는 함수나 메서드 사용 
  - 참조하는 객체가 있다면 `불변 객체`만 참조  
  - 함수나 메서드가 어떤 예외도 일으키지 않아야 한다  
    - 예외가 발생하는 경우 `부분 함수`(정의역에 모든 원소에 대해 정의되어 있지만 않지만, 나머지 요구조건을 만족하는 대응 관계)라 볼 수 있다  
      - ex. 0으로 나누기, 음수의 제곱근 구하기  
	  - `Optional`로 예외를 제거할 수 있다  
	  <br>
	  <img src="https://user-images.githubusercontent.com/80666066/193541731-9e3c7536-d195-4182-83cb-eeff1da515d4.png" width="50%" height="50%"/>  
	  <br>
    <br>
    <img src="https://user-images.githubusercontent.com/80666066/193544315-07069de0-e922-42a5-9fc1-3edef9281954.png" width="50%" height="50%"/>  

  - 부작용이 있는 라이브러리 함수를 사용한다면 비함수형 동작을 감춰야  
  
- 참조 투명성(referential transparanty)을 지키기
  - 같은 인수로 함수를 호출했을 때, 항상 같은 결과를 반환  
  - 함수형 프로그래밍은 데이터가 변경되지 않으므로, 같다는 의미는 ==(참조가 같음)이 아니라 **구조적인 값**이 같음을 의미  
  - 멀티코어 프로세스에서 교착상태에 빠지지 않는다  
  - 시간과 비용이 많이 드는 연산을 `기억화(memorization), 캐싱(caching)`으로 최적화 가능하다  
    
    
 ## 도움받은 곳들  
 
 - [모던 자바 인 액션](https://search.shopping.naver.com/book/catalog/32466988102?cat_id=50010920&frm=PBOKPRO&query=%EB%AA%A8%EB%8D%98+%EC%9E%90%EB%B0%94+%EC%9D%B8+%EC%95%A1%EC%85%98&NaPm=ct%3Dl8skr5ns%7Cci%3D2a53491836579d93983139610de8dbea0cf42b23%7Ctr%3Dboknx%7Csn%3D95694%7Chk%3Ddf1c9b905d60ac87b1f0f6db67b8986466238e43)  
 - [Java 8에서 왜 함수형 프로그래밍이 도입되었을까?](https://tecoble.techcourse.co.kr/post/2021-09-30-java8-functional-programming/)
 
