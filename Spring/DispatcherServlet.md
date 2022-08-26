# 0. intro

스프링을 공부해나가면서 [어노테이션](https://velog.io/@betterfuture4/Spring-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC) 엔 익숙했지만 구체적인 작동 방식에 대해서는 잘 몰랐다.

이번에 CS스터디에서 `Dispatcher Servlet` 주제를 발표하기로 결정되었다. 관련해서 찾아봤는데 익숙한 내용들(스페셜 빈…)도 있었고 몰랐거나 잘못 알고있던 부분(Filter와 Interceptor의 작동 위치)들도 있었다.


## 1) 서블릿(Java Servlet)?

> 자바 서블릿(Java Servlet)은 **자바를 사용하여 웹페이지를 동적으로 생성하는 서버측 프로그램 혹은 그 사양**을 말하며, 흔히 "서블릿"이라 불린다. 자바 서블릿은 웹 서버의 성능을 향상하기 위해 사용되는 자바 클래스의 일종이다.
(…)
> 자바 서블릿은[`자바 EE](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_EE) 사양의 일부분`으로, 주로 이 기능을 이용하여 쇼핑몰이나 온라인 뱅킹 등의 다양한 웹 시스템이 구현되고 있다.
[https://ko.wikipedia.org/wiki/자바_서블릿](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%84%9C%EB%B8%94%EB%A6%BF#:~:text=%EC%9E%90%EB%B0%94%20%EC%84%9C%EB%B8%94%EB%A6%BF(Java%20Servlet)%EC%9D%80,%EC%9E%90%EB%B0%94%20%ED%81%B4%EB%9E%98%EC%8A%A4%EC%9D%98%20%EC%9D%BC%EC%A2%85%EC%9D%B4%EB%8B%A4)
>

즉, 서블릿(Servlet)은 클라이언트 요청을 처리하고, 그 결과를 반환하는 웹 프로그래밍 기술이다.

## 2) J2EE(**Java** EE)?

> 자바 기술로 어플을 만들 때 필요한 스펙(구성요소, api, 런타임 컨테이너, 서비스 등에 대한 표준 사양)들의 집합이다.
이전에는 **J2EE**라 불리었으나 버전 5.0 이후로 **Java EE**로 개칭되었다.
[https://ko.wikipedia.org/wiki/자카르타_EE](https://ko.wikipedia.org/wiki/%EC%9E%90%EC%B9%B4%EB%A5%B4%ED%83%80_EE)
>

JSP(Java Server Page), `Java Servlet`, EJB(Enterprise JavaBeans) 등의 구성요소로 구성된다. 개발자들은 이런 구성요소들로 대규모 분산 응용 프로그램을 구축할 수도 있다.

우리는 이런 Java EE 응용 프로그램을 JAR 파일로 변환해 인스턴스로 배포하게 된다~

# 1. 디스패처 서블릿(Dispatcher Servlet)?

## 0) Servlet의 일종이다!

```java
public class DispatcherServlet extends FrameworkServlet {
}

public abstract class FrameworkServlet extends HttpServletBean {
}

public abstract class HttpServletBean extends HttpServlet {
}
```

디스패처 서블릿도 Servlet이다.

Dispatcher의 뜻은 급파자라는 의미를 가지고 있다. 단어 의미 그대로 가장 먼저 요청을 먼저 받고, 적절하게 처리할 함수, 즉 컨트롤러를 찾아서 정해주는 역할을 한다.

디스패처 서블릿은 `HttpServlet`을 상속한다.
(`DispatcherServlet` -> `FrameworkServlet` -> `HttpServletBean` -> `HttpServlet` 상속 구조)

## 1) 프론트 컨트롤러(Front Controller) 패턴이다!

![](https://velog.velcdn.com/images/betterfuture4/post/b8cd33dc-0d26-42cd-8594-139b1392f306/image.png)


과거에는 모든 서블릿을 URL 매핑을 위해 web.xml에 등록해야 했지만, 이젠 디스패처 서블릿이 있다!

디스패처 서블릿은 스프링 MVC의 중앙 서블릿이며 어플리케이션으로 오는 모든 요청을 핸들링하고 공통작업을 처리해준다.

실제 작업(비즈니스 로직, 예외처리 등)은 위임 컴포넌트를 통해 수행된다.

디스패처 서블릿을 프론트 컨트롤러 라고도 부른다. 프론트 컨트롤러는 서블릿 컨테이너 맨 앞에서 모든 요청을 받아 처리해주는 컨트롤러를 의미한다.

이러한 패턴을 프론트 컨트롤러(`Front Controller`) 패턴이라고 부른다.
[보다 자세한 설명](https://www.geeksforgeeks.org/front-controller-design-pattern/)

## 2) 정적자원과 동적 자원을 분할 처리한다!

디스패처 서블릿을 통해 얻을 수 있는 장점이 또 있다. 정적자원과 동적 자원을 따로 처리할 수 있다는 점이다. (서블릿이기 때문이다)

디스패처 서블릿에서 요청을 처리할 컨트롤러를 먼저 찾고, 컨트롤러가 없을땐 2차로 설정된 정적 자원을 탐색한다.

이를 통해 효율적인 리소스 관리가 가능해진다!

# 2. 설정 방법

## 1) 등록 및 초기화

모든 서블릿이 그렇듯, 디스패쳐 서블릿도 자바나 web.xml을 통해 등록 및 초기화 되어야 사용 가능하다.

톰캣 등 서블릿 컨테이너를 통해 디스패처 서블릿의 생명주기를 관리하는 것이 일반적이다. 아래 방법으로 원시적인 설정도 가능하다.

```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) {

        // Load Spring web application configuration
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        context.register(AppConfig.class);

        // Create and register the DispatcherServlet
        DispatcherServlet servlet = new DispatcherServlet(context);
        ServletRegistration.Dynamic registration = servletContext.addServlet("app", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/app/*");
    }
}
```

Java

```xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/app-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>

</web-app>
```

web.xml

# 3. 구조

## 0) `Servlet Container`

![](https://velog.velcdn.com/images/betterfuture4/post/0c921f33-4ca5-4f1a-84e0-82d0327a00ed/image.png)


서블릿은 스스로 동작하지 않는다. 보통 서블릿을 관리할 서블릿 컨테이너(톰캣 등)를 사용한다.

서블릿 컨테이너는 싱글톤인 서블릿 객체의 생명주기를 관리한다. 또한 웹서버와 소켓으로 통신하며, 클라이언트의 요청을 받고 응답을 보내준다. 멀티쓰레드 처리도 지원한다.

## 1) `WebApplicationContext`

![](https://velog.velcdn.com/images/betterfuture4/post/efd7a506-fe81-4631-a1da-9373a0ddbe94/image.png)


디스패쳐 서블릿은

1. `Servlet WebApplicationContext` 을 생성하고
2. 보통 `Root WebApplicationContext` 을 생성한다.

서블릿 `WebApplicationContext` 안에는 `ServletContext`와 연관된 `Servlet 링크`들이 있다.

`Controller`, `ViewResolver`, `HandlerMapping` 등이 이 안에 있다. `RequestContextUtils` 의 정적 메소드들을 사용해 어플리케이션이 `WebApplicationContext`를 조회할 수 있다.

`Root WebApplicationContext` 가 있는 경우, `infra bean`이 이 안에 들어간다. `Repository`, `Service` 빈 등이 인프라 빈이다.

`WebApplicationContext` 설정은 `java`나 `web.xml` 을 통해 할 수 있다.

```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { App1Config.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/app1/*" };
    }
}
```

Java

```xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/root-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app1</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/app1-context.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app1</servlet-name>
        <url-pattern>/app1/*</url-pattern>
    </servlet-mapping>

</web-app>
```

web.xml

## 2) 스페셜 빈(`Special Bean`)

아까 말했듯, 디스패처 서블릿은 요청을 처리하기 위해 위임 컴포넌트에 실제 작업을 맡긴다. 이런 위임 컴포넌트를 스페셜 빈(`Special Bean`)이라고도 부른다.

스페셜 빈은 프레임워크 계약을 구현하는 스프링 관리 객체 인스턴스를 의미한다. 쉽게 말하면 스프링에서 제공하는 주요 빈들이다. 스페셜 빈들마다 제공되는 일반적인 설정들이 있지만 사용자(즉, 프로그래머)가 임의로 재정의하거나 교체가 가능하다고 한다.

다음은 디스패쳐 서블릿에서 사용하는 스페셜 빈들이다.

1. `handlerMapping` : 요청을 처리할 컨트롤러를 찾는다
2. `handlerAdapter` : 요청을 컨트롤러로 전달한다
3. `handlerExceptionResolver` : 요청에 대한 예외 발생(핸들러 매핑 도중, 컨트롤러 실행 도중) 시 처리해준다
4. `viewResolver` : 리턴값에 대한 뷰 컴포넌트를 반환한다. `string`으로 뷰 이름을 전달받았을 때 이를 `modelAndView` 등의 객체로 반환한다.
5. `LocaleResolver` : `Locale`을 결정한다. 참고로 `Locale`은 유저의 언어,지역,출력형식 등을 정의하는 문자열이다.
6. `MultipartResolver` : 멀티파트 파일 업로드를 처리해준다. 참고로 멀티파트는 HTTP 응답의 바디를 여러 파트로 나눠서 보내줄 때의 파일 형식을 의미한다.

# 4. 작동 방식

![](https://velog.velcdn.com/images/betterfuture4/post/3d2e176a-927f-43c1-a01e-4ded2cd88b06/image.png)

간략 버전

![](https://velog.velcdn.com/images/betterfuture4/post/fdbedace-4c32-4a1a-add8-b5b63af8e1fa/image.png)

상세 버전

디스패쳐 서블릿은 다음 방식으로 작동한다.

1. 클라이언트(아마 프론트 서버) 요청을 디스패처 서블릿이 받는다.
    1. 이때 `filter`를 경유할 수 있다ㅋ

       ![](https://velog.velcdn.com/images/betterfuture4/post/21107cb0-4129-43b7-a049-493054954c60/image.png)

       filter도 나중에 정리해나갈 예정…

2. 요청 정보를 보고 `Servlet WebApplicationContext` 안에서 `HandlerMapping`을 통해 요청을 위임할 컨트롤러를 검색해 찾는다.
3. 찾은 컨트롤러로 요청을 위임할 `HandlerAdapter`를 찾는다.
    1. 이때 `Interceptor` `preHandle`이 실행된다.
4. `HandlerAdapter`가 컨트롤러로 요청(`ModelAndView`)을 위임한다
    1. `Argument Resolver`가 파라미터를 처리해 컨트롤러로 보낸다.
5. 컨트롤러는 `Root WebApplicationContext` 속 `Service`, `Repository`… 등을 호출해가며 비즈니스 로직을 처리한다.
6. 컨트롤러 메소드에서 최종적인 반환값(`Response Entity`)이 리턴된다.
    1. 리턴 값에 대해 `Interceptor` `postHandle`이 실행된다.
7. 핸들러 어댑터 리턴값을 `ViewResolver`에 전달한다.
8. `ViewResolver`가 `View`에 응답을 전달한다.
    1. 이후 `Interceptor` `afterCompletion`이 실행된다.
9. 디스패처 서블릿에서 `View`로부터 받은 응답을 클라이언트(프론트 서버)로 반환해준다!

각 과정들을 코드와 함께 더 자세히 알고싶다면 [tecoble](https://tecoble.techcourse.co.kr/post/2021-07-15-dispatcherservlet-part-2/) 글을 참고하자.

---

공부하고 정리하면서도 아직 모호하게 이해한 부분들도 있다.
정확히 어떤 식으로 `adapter`가 작동되는지, `dispatch` 기능을 수행하는 메소드 형태는 어떤지 등... 김영한님 강의나 책을 읽으며 부족한 부분을 차차 채워나갈 생각이다.

---

# 5. 참고자료

## 1) 공식 문서/Wiki

[15.2 The DispatcherServlet](https://docs.spring.io/spring-framework/docs/3.0.0.M4/spring-framework-reference/html/ch15s02.html)

[What is Dispatcher Servlet in Spring? - GeeksforGeeks](https://www.geeksforgeeks.org/what-is-dispatcher-servlet-in-spring/)

[Web on Servlet Stack](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-exceptionhandlers)

[자바 서블릿 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%84%9C%EB%B8%94%EB%A6%BF#:~:text=%EC%9E%90%EB%B0%94%20%EC%84%9C%EB%B8%94%EB%A6%BF(Java%20Servlet)%EC%9D%80,%EC%9E%90%EB%B0%94%20%ED%81%B4%EB%9E%98%EC%8A%A4%EC%9D%98%20%EC%9D%BC%EC%A2%85%EC%9D%B4%EB%8B%A4)

## 2) 아티클

### Tecoble

[Servlet 과 ServletContainer](https://tecoble.techcourse.co.kr/post/2021-05-23-servlet-servletcontainer/)

[DispatcherServlet - Part 1](https://tecoble.techcourse.co.kr/post/2021-06-25-dispatcherservlet-part-1/)

[DispatcherServlet - Part 2](https://tecoble.techcourse.co.kr/post/2021-07-15-dispatcherservlet-part-2/)

[Spring ArgumentResolver와 Interceptor](https://tecoble.techcourse.co.kr/post/2021-05-24-spring-interceptor/)

### 블로그

[Front Controller Design Pattern - GeeksforGeeks](https://www.geeksforgeeks.org/front-controller-design-pattern/)

[[Spring] Dispatcher-Servlet(디스패처 서블릿)이란? 디스패처 서블릿의 개념과 동작 과정](https://mangkyu.tistory.com/18)

[Spring에서 Handler Interceptor 개념 및 구현해보기](https://goodgid.github.io/Spring-HandlerInterceptor/)

[Spring MVC - DispatcherServlet 동작 원리](https://jess-m.tistory.com/15)


# 6. 질문
### 1. Spring Web MVC의 Dispatcher Servlet의 동작 원리에 대해서 간단히 설명해주세요.

우선 디스패처 서블릿은 서블릿 컨테이너(톰캣 등)을 통해 생명주기가 관리됩니다. 이러한, 싱글톤 빈인 디스패처 서블릿은 프론트 컨트롤러로, 클라이언트 요청들을 받습니다.

이후 핸들러 매핑을 통해 요청을 처리할 컨트롤러(핸들러)를 찾고, 핸들러 어댑터를 통해 웹어플리케이션컨텍스트 속에서 검색된 컨트롤러로 요청을 전달합니다.
이후 웹어플리케이션컨텍스트를 통해 서비스, DB 빈들이 호출되며 비즈니스 로직이 수행됩니다. 컨트롤러의 리턴값 역시 핸들러 어댑터를 통해 뷰리졸버에 전달되고, 안에서 파라미터 값에 대한 ModelAndView 등이 반환됩니다. 해당 객체는 뷰로 전달되고요.

디스패처 서블릿은 최종적으로 뷰에서 전달받은 리턴값을 클라이언트에게 응답해줍니다.
이렇듯 디스패처 서블릿은 요청/응답에 대한 전처리 및, 컨트롤러 매핑 등 공통 로직을 수행합니다. 이를 통해 개발자는 비즈니스 로직에 집중한 구현이 가능합니다.

### 2. 프론트 컨트롤러 패턴이란 무엇인가요?

클라이언트의 다양한 요청마다 서블릿을 만들어서 사용한다고 하면 개발과 유지보수의 효율이 떨어질 수 밖에 없습니다.

프론트 컨트롤러 패턴을 사용함으로써 각 요청을 적절한 곳으로 위임해줌으로써 개발과 유지보수의 효율성이 증가하고 모든 요청에 대해 공통 기능을 한 곳에서 캡슐화할 수 있습니다.

Spring에서는 DispatcherServlet이 프론트 컨트롤러 패턴을 사용한 예입니다. DispatcherServlet이 Bean으로 등록되어 package를 scan하고 @Controller, @RestController 애노테이션을 확인하여 어떠한 요청이 들어왔을 때 적절한 Handler Method에 위임해줍니다.
