
## 화이트박스 테스트(White Box Test) vs 블랙박스 테스트(Black Box Test)  

### 둘의 차이   

- 화이트박스 테스트  
    - 실사용자는 알 수 없는 내부 소스 코드를 테스트한다  
    - 동작의 유효성 뿐만 아니라 실행되는 과정을 살핀다  
    - 불필요한 코드 혹은 테스트 되지 못한 부분도 찾을 수 있다  

- 블랙박스 테스트  
    - 실사용자의 입장. 즉 내부 구조나 작동 원리를 모르는 상태에서 동작을 테스트한다  
    - `이 소프트웨어는 무슨 역할을 수행하는가?` 같이 특징, 요구 사항에 초점을 맞춘다  
    - 인수 테스트가 이에 속한다  


극단적인 예시로 회원가입, 게시글 작성, 댓글 작성의 기능을 인수테스트 한다 했을 때...  

- 블랙박스 테스트에서 모두 `상태코드 201`이 나와 잘 된다고 판단  
- 화이트박스 테스트결과 사실 어떤 요청이 오든 `상태코드 201`을 뱉고 있었던 걸 수도 있다  

이하 상세 기법은 테스트 코드를 짜왔다면 직관의 영역에서 다들 한 번씩 해봤을 기법들임  
따라서 이름과 내용을 외우기 보다 실례와 연결시키면 좋을 것 같음  

<br>

### 화이트박스 테스트 상세 기법  

#### 문장 검증(Statement Coverage)  

- 소프트웨어 코드의 모든 구문이 한 번 이상 수행되도록 설계  

```
feature: 자동차 경주 어플리케이션 코드 전체가 다 수행되는지 검증한다  
step1: 이름을 입력 받는다  
step2: 입력된 문자열을 공백 단위로 쪼개 리스트로 변환한다  
step3: 이름 리스트로 자동차 인스턴스를 생성한다  
...
stepN: 우승자 이름을 모두 출력한다  
```

- - - 

#### 분기 검증  

- 소프트웨어 코드의 모든 조건문을 한 번 이상 수행되도록 설계  

```
feature: 자동차 경주의 모든 조건문을 한 번 이상 수행시킨다    
step1: 자동차는 숫자 5 이상일 때만 전진한다  
step2: 자동차의 위치값을 비교해 승자를 찾는다  
```

- - - 

#### 조건 검증  

- 소프트웨어 코드의 모든 조건문의 참, 거짓을 각 한 번 이상 수행되도록 설계  

```
feature: 자동차의 전진 기능 코드를 검증한다  
case1: 숫자 4 이하일 때 전진하지 않는다  
case2: 숫자 5 이상일 때 전진한다  
```

- - - 

#### 분기 / 조건 기준  
- 소프트웨어 코드의 모든 조건문과 각 조건문 안의 개별 조건식의 결과가 참, 거짓인 경우 각 한 번 이상 수행되도록 설계  

```
```

### 블랙박스 테스트 상세 기법  

#### 동치분할 테스트(Equivalence Partitioning Testing)  

- 정상적인 입력자료와 비정상적인 입력 자료의 개수가 균등하도록 테스트 케이스를 정한다  
- 각 해당 입력 자료에 맞는 결과가 출력되는지 확인한다  

```
feature: 체스 말이 이동할 위치를 입력한다  
when: a1 e3
then: 체스말이 이동한 결과물이 출력된다  
when: b1 f5
then: 체스말이 이동한 결과물이 출력된다  
when: aa e3
then: 이동 위치를 다시 입력하라는 안내문이 출력된다  
when: b1 55
then: 이동 위치를 다시 입력하라는 안내문이 출력된다  
```  

- - -

#### 경계값 분석Boundry Value Analysis)  

- 입력 조건의 중간값이 아닌 경계값을 테스트 케이스로 선정한다  

```
feature: 회원가입 시 비밀번호는 최소 15자 길이여야 한다  
when: "abcdefghijklmn"
then: 비밀번호 칸이 빨간색이 되며 다시 입력하라는 안내문이 나온다  
case2: "abcdefghijklmno"
then: 비밀번호 칸이 초록색이 된다  
```

- - - 

#### 원인-효과 그래프 검사(Cause-Effect Graphing Testing)  

- 여러 입출력 데이터를 분석해서 영향을 미치는 상황을 체계적으로 분석하고, 효율성이 높은 테스트 케이스를 선정한다  
- 요구사항 명세를 입력및 출력 조건 간의 논리적 관계로 표현한다  

```
feature: 게시글 삭제 기능  
when: 유저A가 자신의 게시글을 삭제한다  
then: 게시글이 삭제된다  
when: 유저B가 삭제된 게시글 경로로 접근한다  
then: 404 페이지가 나오며 게시글을 볼 수 없다  
```

- - - 

#### 오류 예측 검사(Error Guessing)  

- 과거의 경험이나 확인자의 감각으로 테스트 케이스 선정  

```
feature: 게시글 무한 스크롤 시 게시글이 늦게 로딩된다  
case1: 테스트 수행자가 스크롤을 빠른 속도로 아래로 내린다  
case2: 테스트 수행자가 스크롤을 적당한 속도로 아래로 내린다  
```
