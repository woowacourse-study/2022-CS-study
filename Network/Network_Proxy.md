# Proxy 와 Forward & Reverse

> 참고
>

해당 내용에서는 `Network`에서 말하는 `Proxy`를 정의합니다.

## Proxy


### 정의

클라이언트와 서버가 서로 직접 통신하지 않고, 중간에서 대리로 통신을 수행하는 것을 `Proxy`라고 합니다. 이러한 중계 역할을 하는 서버를 `Proxy Server` 라고 합니다.

즉, `Proxy Server`는 클라이언트와 서버 사이의 중계 서버로서의 역할을 수행합니다.

<br>

## Forward Proxy


일반적으로 `Proxy`라고 하면 보통 `Forward Proxy`를 말합니다. Forward Proxy는 클라이언트들 앞에 위치를 하는 Proxy를 말합니다.

클라이언트 A가 서버 C에 요청을 보내면, `Forward Proxy`가 해당 요청을 서버 대신 받아서 서버에게 대신 전달을 해주게 됩니다. 마찬가지로 서버의 응답을 `Forward Proxy`가 대신 받아 클라이언트에게 전달을 해줍니다.

이러한 `Forward Proxy`를 사용한다면 다음과 같은 이점이 있습니다.

1. 특정 콘텐츠 제한
    - 사용자들이 특정 컨텐츠에 접속하는 것을 제한할 수 있습니다. 공공 사이트에서 도박과 같은 유해성 사이트에 접근하는 것을 포워드 프록시를 통해서 제한을 할 수 있습니다.
2. 캐싱 기능
    - 클라이언트가 서버에 A라는 요청을 하게 되면, 서버는 B라는 결과를 만들어 클라이언트에게 응답하게 됩니다. 이때, 해당 결과를 `Proxy`에서 저장하여 캐싱 기능을 수행합니다. 이를 통해, 성능을 향상 시킬 수 있습니다.
3. IP 우회 및 보안
    - 클라이언트에서 `Forward Proxy` 를 거쳐 요청을 보내게 되면, 서버 측은 클라이언트의 정보가 아닌 `Forward Proxy`의 정보를 받게 되어 클라이언트의 정보를 숨길 수가 있습니다.
    - 즉, 본 서버에서 IP 주소를 역추적해도 `Forward Proxy`의 IP 주소만 남기 때문에 정체를 파악하기 어려워 보안을 향상 시키는 효과를 가질 수 있습니다.

<br>

## Reverse Proxy


위에서 설명한 `Forward Proxy`와 반대로, `Reverse Proxy`는 웹 서버들 앞에 위치를 합니다.

`Reverse Proxy`와 `Forward Proxy`의 차이점은 일단 `Proxy`가 존재하는 위치가 다르다는 점 입니다. 또한, 주체가 다르다는 점이 있습니다. `Forward Proxy`가 관련 클라이언트들을 위해 임의의 서버에 접속하는 중간 매개체라면, `Reverse Proxy`는 관련 서버들을 위해 임의의 클라이언트가 해당 서버에 접속하는 중간 매개체 역할을 수행합니다.

이러한 `Reverse Proxy`r를 사용한다면 다음과 같은 이점이 있습니다.

1. 캐싱 기능
    - `Forward Proxy`와 동일하게 캐싱을 할 수 있습니다.
2. 보안
    - 만약 A라는 서버가 해킹 당하게 되면, A에 연결되어 있는 DB 서버까지 전부 해킹을 당할 수 있습니다. 이러한 문제를 해결하기 위해서 `Reverse Proxy`를 서버의 앞 단에 두고, 실제 서비스를 하는 WAS 서버는 내부망에 위치시킨 후 서비스를 하면 안전하게 서비스를 운영할 수 있습니다.

    - 이번 프로젝트를 하면서 일반적으로 구성하는 `Web(Nginx) - Was(Tomcat)` 의 분리 형태가 바로 `Reverse Proxy`를 사용한 형태라고 할 수 있습니다.
    - 또한, `Reverse Proxy`를 경유해서 서버에 요청을 보내기 때문에, 클라이언트는 본 서버의 `URL` 주소를 모른채 `Reverse Proxy URL`을 통해 서비스를 이용하게 되고, 이는 본서버의 정보를 숨기는 효과를 가지게 되어 향후, `DDos` 공격같은 해커들의 공격을 막는데 유용하게 사용이 가능합니다.
3. 로드 밸런싱
    - 만약 사용자의 요청이 많아져 서버의 성능을 높여야 한다면 물리적으로 기기의 성능을 끌어올리는 것은 한계가 있습니다. 이런 문제를 해결하기 `Reverse Proxy` 를 사용할 수 있습니다.

   `Reverse Proxy`를 사용하면, 사용자가 증가하는 상황에 맞게 `Web Server` 혹은 `Was`를 늘려서 성능을 올려 특정 서버가 과부하 되지 않게 사용을 할 수 있습니다.
4. 암호화
    - 서버가 클라이언트들과 통신을 할 때 `SSL`을 통해 암호화, 복호화를 하게 되는데 이는 비용이 드는 작업입니다. `Reverse Proxy`를 이용한다면 요청이 들어올 때는 복호화를 해주고, 나가는 응답에는 암호화를 해주기 때문에 클라이언트와 안전한 통신을 할 수 있으며, 본래 서버의 부담을 줄여줄 수가 있습니다.


<br>

> 참고 자료
>
- [https://www.youtube.com/watch?v=YxwYhenZ3BE&t=434s](https://www.youtube.com/watch?v=YxwYhenZ3BE&t=434s)
- [https://www.fortinet.com/resources/cyberglossary/proxy-server](https://www.fortinet.com/resources/cyberglossary/proxy-server)
