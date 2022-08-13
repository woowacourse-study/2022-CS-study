
# 🪙 세션 인증과 토큰 인증  

## 세션/토큰    

- 세션
  - 개념적 의미 : 둘 또는 그 이상의 컴퓨팅 디바이스간에서 일어나는 일시적이고 상호적인 정보 교환  
  - 사용상 의미 : 사용자 식별을 위한 데이터를 서버에 저장하고 세션 키를 발급해 인증 처리하는 방식  
  - 스프링 내 구현체  

```java
public abstract class ManagerBase extends LifecycleMBeanBase implements Manager {
    ...
    protected Map<String, Session> sessions = new ConcurrentHashMap<>();

    @Override
    public void add(Session session) {
        sessions.put(session.getIdInternal(), session);
        ...
    }

    ...
    public HashMap<String, String> getSession(String sessionId) {
        Session s = sessions.get(sessionId);
        ...
}
```

<br>  
  
- 토큰
  - 개념적 의미 : 식별자로 사용될 수 있는 데이터  
  - 사용상 의미 : JWT를 이용한 인증 처리 방식  
  - jwt 토큰 예제  
<img width="1203" alt="image" src="https://user-images.githubusercontent.com/80666066/181855181-1542cf2a-2f51-4cb9-9428-3fcf463d11f3.png">

<br>

둘 다 HTTP 프로토콜의 `stateless` 상태를 극복하기 위한 두 방안  

<br>  

## 세션 인증 절차  

1. 새로운 로그인이 발생하면 새로운 세션 생성  
2. 세션을 식별할 수 있는 `session ID`를 발급해 쿠키로 전달  
3. 사용자가 http 요청 시 서버는 쿠키로 해당 세션 탐색  

<br>

## 토큰 인증 절차  

1. 새로운 로그인이 발생하면 새로운 `jwt` 토큰 생성  
2. 해당 토큰 문자열을 전달  
3. 사용자는 http 요청 시 `header`에 `Authorization` 키로 값을 전달  
4. 서버는 토큰의 유효성을 검증 후 사용  

<br>

## 세션/토큰 인증 선택 시 고려해야 할 사안  

1. 보안  
- 세션은 값을 서버에 저장
- 토큰은 누구나 디코딩 할 수 있음  
  - 토큰에는 중요 정보를 담으면 안된다  

<br>
 
2. 네트워크 사용량    
- 같은 `session ID` 문자열 `abc123` 데이터를 저장한다고 가정  
  - 세션은 쿠키에 `abc123`를 저장해 `6byte`만 사용  
  - 토큰은 약 `111byte`사용  
    - 실제로는 토큰에 더 많은 정보를 담기에 훨씬 많은 바이트 사용
    - 실행 수가 늘어난다면 유의미한 차이가 될 수 있음  

<br>

3. DB 쿼리문 수행 횟수  
- 세션은 객체를 저장 가능  
- 토큰은 디코딩한 값으로 (많은 경우) DB를 방문  

<br>

## 그럼에도 토큰이 대세가 된 이유?  

토큰 방식이 이점을 얻는 때가 있다  

1. 서버 또는 클라이언트(모바일 앱 또는 SPA)에게 제공할 API 서비스를 만드는 경우    
- 절차상 인증 API에서 토큰 발급  
- 클라이언트는 해당 토큰으로 다른 API 호출
- jwt는 클라이언트의 신뢰성을 증명
  - 별도의 신뢰성 검증 네트워킹 수행 없이 처리 가능  

<br>

2. 요청이 3개 이상의 서로 다른 서버 또는 클라이언트를 걸칠 경우  
- 요청 당사자가 신원 인증 토큰을 사용한다면 매번 유효성 검증을 하지 않아도 됨  

<br>  

3. 공개된 사용자 인증 시스템을 사용 할 경우  
- `single sign-on`이나 `OpenID Connect` 같은 서드 파티 인증을 사용할 때 유용  

<br> 

### 예상 질문  

1. 세션 인증과 토큰 인증 사이의 차이  
  - 세션 인증은 서버측에서 데이터 관리, 쿠키를 통해 세션 식별자인 `session id` 전달  
  - 토큰 인증은 서명된 데이터 문자열을 클라이언트가 요청 헤더에 포함  

2. 세션과 토큰을 사용할 때 각각 주의해야 할 점  
  - 세션
    - 서버 간 공유가 되지 않기에 여러 서버를 사용중이라면 해결법을 찾아야 함
    - 쿠키 비허용 시 제대로 동작하지 않을 수 있음  
  - 토큰
    - 데이터를 누구나 볼 수 있으므로 보안 상 중요한 정보를 담으면 안 됨  
    - 탈취를 대비해 `access token + refresh token` 방식을 사용하면 좋음  

3. 분산 서버에서 세션의 한계를 극복할 방법  
  - `Sticky session, Session clustering, Session storage DB` 등을 이용할 수 있다  


<br> 

## 참고 자료  

- [okta - 왜 jwt토큰이 세션 토큰보다 별로인가](https://developer.okta.com/blog/2017/08/17/why-jwts-suck-as-session-tokens)  

<br>  

