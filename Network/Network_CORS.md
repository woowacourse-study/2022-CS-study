# 사전 개념

## 1. `Origin`(출처)

- `URL`에서 확인 가능한 `프로토콜`, `호스트`, `포트` 가 같을 때 같은 출처!
    - [`localhost`](http://localhost) 와 `127.0.0.1`은 다른 호스트로 판단됨!(실제로는 같은 주소지만)

## 2. `SOP`(Same Origin Policy)

### 정의

- 다른 출처 리소스를 사용하는 걸 제한하는 보안 방식

### 왜 `SOP`를 사용하는가?

- 페북 로그인 후 해커 링크 클릭 시!
    - 해커가 페북 토큰을 통해 게시물을 마음대로 올릴 수 있다!
    - 송금 등 민감 정보를 가져올 수도 있다!
- `SOP`를 이용하면 `Origin`이 다르기 때문에 요청을 거절한다!
    - (해커는 `hacker.ck`에서 요청을 보낸다. 원래는 `facebook.com`에서 요청이 와야 한다!)

# CORS

- 교차 출처 리소스 공유(`Cross-Origin Resorce Sharing`, `CORS`)

## 1. 정의

> 다른 `Origin`(도메인/프로토콜..) 등으로 들어오는 `요청`을 `허용해주는 것`!
>
- `추가 HTTP 헤더`를 사용해
    - `한 출처`에서 실행 중인 웹 어플리케이션이
        - `다른 출처의 선택한 자원에 접근`할 수 있는
        - `권한을 부여`하도록
- 브라우저에 알려주는 체제

## 2. 접근 제어 시나리오

### 1) `Simple Request`

- **사전요청(`Preflight`) 없이**! 바로 요청을 날림!
- **필요조건**
    1. `GET`, `POST`, `HEAD` 메서드 중 하나여야 함
    2. 헤더는 다음만 허용됨!
        - `Accept`
        - `Accept-Language`
        - `Content-Language`
        - `Content-Type`
        - `**WebKit`/`Safari`  브라우저의 경우**
            - **헤더 중 하나에 `nonstandard` 값이 존재하면,**
            - **더 이상 요청을 `simple request`로 간주하지 않!**
    3. `POST` 메서드의 경우, `Content-Type`이 다음 중 하나여야 함
        - `application/x-www-form-urlencoded`
        - `multipart/form-data`
        - `text/plain`

### 2) `Preflight Request`

> 너네 집에 놀러가도 돼?
>
- 본요청을 보내기 전, 요청을 보내도 되는지 `**Options` 메소드**를 통해 확인

**과정**

1. 사전 요청(`preflight`)로 이 요청을 보내도 되는지 물어봄
    1. 헤더에 다음 항목이 포함되어야 함
        1. `Origin` : 해당 위치에서 요청을 보낸다는 뜻
        2. `Access-Control-Request-Method` : 어떤 메소드의 요청을 보낼건지 물어봄
        3. `Access-Control-Request-Headers` : 실제 요청에 추가 헤더를 무엇을 보낼 수 있는지 물어봄
2. 보내도 된다는 응답을 받음
    1. 헤더에 다음 항목이 포함되어야 함
        1. `Access-Control-Allow-Origin` : 해당 `Origin`을 허가한다는 뜻
        2. `Access-Control-Allow-Methods` : 해당 `메소드`들의 요청을 허가한다는 뜻
        3. `Access-Control-Allow-Headers` : 해당 `헤더`들을 허가한다는 뜻
        4. `Access-Control-Max-Age` : 응답의 `캐시` 기간
            1. 해당 캐시가 있을시 `preflight` 과정을 건너뜀
    2. 다음 조건이 필요함
        1. 응답은 200대
        2. 응답 바디는 비어있음
3. 실제 요청을 보냄~
4. 실제 응답을 받음~

### 3) `Credentialed Request`

> 자격증명(쿠키, `authorization`, 인증서..)를 포함하는 요청
>
- 2)와 같은 사전요청을 보내며, 이때 사전응답 헤더 필드가 추가됨

**과정**

1. 사전 요청(`preflight`)로 이 요청을 보내도 되는지 물어봄
    1. 헤더에 다음 항목이 포함되어야 함
        1. `Origin` : 해당 위치에서 요청을 보낸다는 뜻
        2. `credentials : include` : 자격증명 정보가 포함된다는 뜻
        3. 자격증명 정보…헤더나 쿠키 등
2. 보내도 된다는 응답을 받음
    1. 헤더에 다음 항목이 포함되어야 함
        1. `Access-Control-Allow-Origin` : 해당 `Origin`을 허가한다는 뜻
        2. `Access-Control-Allow-Credentials: true` : 자격증명(쿠키, `authorization`, 인증서..)를 허가한다는 뜻
        3. `Access-Control-Max-Age` : 응답의 `캐시` 기간
            1. 해당 캐시가 있을시 `preflight` 과정을 건너뜀
    2. 다음 조건이 필요함
        1. 응답은 200대
        2. 응답 바디는 비어있음
3. 실제 요청을 보냄~
4. 실제 응답을 받음~

**주의사항**

- 자격증명 요청의 응답은
    - 서버는  `Access-Control-Allow-Origin` 헤더 값에
    - "`*`" 와일드카드를 지정할 수 없고, 반드시 출처를 지정해야 함!

### 4) 추가 헤더

- `Access-Control-Expose-Headers` ***:*** 웹 브라우저의 `클라이언트 스크립트`에 노출될 헤더를 결정함

## 3. 스프링부트 적용 방식

```java
    private static final String ALLOWED_METHOD_NAMES = "GET,HEAD,POST,DELETE,TRACE,OPTIONS,PATCH,PUT";

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("http://{프론트 public ip}", "http://{도메인}"
                , "http://www.{도메인}", "https://{도메인}", "https://www.{도메인}")
                .allowedMethods(ALLOWED_METHOD_NAMES.split(","))
                .allowCredentials(true)
                .exposedHeaders(HttpHeaders.LOCATION)
                .exposedHeaders(HttpHeaders.AUTHORIZATION);
    }
```

- 모든 `URL`에서
    - 특정 `오리진`의 경우
    - `ALLOWED_METHOD_NAMES`  에 해당하는 HTTP 메소드인 경우
    - 자격증명 필드를 허용해주고
    - `exposedHeaders` 로 `클라이언트 스크립트`에 넘겨줄 헤더들을 설정한다!

---

# 참고자료

- [[10분 테코톡] 🌳 나봄의 CORS](https://www.youtube.com/watch?v=-2TgkKYmJt4)
- [교차 출처 리소스 공유 (CORS) - HTTP | MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
- [노션 링크](https://positive-dracorex-b1e.notion.site/CORS-9bae79e85ddf435392d9118ca5dd0df6)
---

# 질문 리스트

1. **`SOP` 란 무엇이며, 왜 필요한지 설명해보세요.**
    1. `sop`는 `same origin policy`의 약자로, `url`로 확인 가능한 `Origin`이 다른 리소스를 사용하는 걸 제한하는 `브라우저` 정책입니다.
    2. 즉 같은 오리진(출처)에서 온 요청이어야 브라우저는 정상적으로 응답을 보여주게 됩니다.
    3. 이는 보안을 위해 필요합니다.
        1. 예를들어, 인스타그램에 로그인한 제가 어떤 게시물 링크를 클릭했을 때, 다른 오리진(도메인)의 해커는 제 인증정보를 탈취할 수 있습니다. 이 토큰을 활용해 제 개인정보를 빼내거나 이상한 글을 제 계정에 올릴 수 있습니다.
    4. 이런 일을 일차적으로 막기 위해, 브라우저는 같은 도메인과 포트, 프로토콜 등의 요청의 응답만 허용합니다.
2. **`CORS`란 무엇이며, 왜 필요한지 설명해보세요.**
    1. 경우에 따라, 서버는 다른 오리진(출처)에서 온 요청에 대해서도 응답을 보내주고 싶을 수 있습니다.
    2. 이때, `CORS`, 즉 `cross origin resource sharing` 을 해줘야 합니다.
    3. 이를 통해 서버는 다른 도메인(서브도메인) 이나 다른 프로토콜/포트로 온 요청을 `SOP`에서 제외시킬 수 있습니다.
3. **`CORS` 접근제어 시나리오들과 그 과정들을 설명해보세요**
    1. 일반적으로 브라우저는 실제 요청을 보내기 전 사전 요청을 보내 응답을 받습니다. 이를 `preflight request-response` 라고 부릅니다.
        1. 이 사전요청은 `Options` 메소드로 보내지고, 이에 대한 서버의 사전 응답을 받습니다.
        2. 이를 통해 브라우저는 해당 요청이 허용된 요청임을 확인하고, 실제 요청을 서버로 쏘게 됩니다!
    2. 그런데 이 사전요청을 보내지 않는 경우도 있습니다. 이런 요청을 `simple request`라 부릅니다.
        1. `심플 리퀘스트`는 요청 메소드가 GET, 일부 POST, HEAD 메소드 등이고 안전한 헤더만 들어가 있을 시 해당합니다.
    3. 사전요청을 보낼 때, 자격증명에 대한 추가 검증이 필요할 때도 있습니다.
        1. 이때는 사전요청 헤더에 `credentials: include` 가 포함되고
        2. 이에 대한 사전응답에 `자격증명 요청을 허가한다는 헤더` 가 `True`로 포함되어 있어야 실제 요청을 보낼 수 있습니다.
