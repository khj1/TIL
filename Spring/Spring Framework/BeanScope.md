# Bean Scope
## 빈 스코프란?
지금까지 우리는 스프링 빈이 스프링 컨테이너의 시작과 함께 생성되어 스프링 컨테이너가 종료될 때 까지 유지된다고 학습했다. 이것은 **스프링 빈이 기본적으로 싱글톤 스코프로 생성되기 때문이다.** 스코프는 번역 그대로 **빈이 존재할 수 있는 범위**를 뜻한다.

스프링은 다음과 같이 다양한 스코프를 지원한다.
- **싱글톤**
  - 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.
- **프로토타입**
  - 스프링 컨테이너는 프로토 타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프이다.
- **웹 관련 스코프**
  - ```request```: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프이다.
  - ```session```: 웹 세션이 생성되고 종료될 떄 까지 유지되는 스코프이다.
  - ```application```: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프이다.
  - ```websocket```: 웹 소켓과 동일한 생명주기를 가지는 스코프

> ### 빈 스코프 등록
- 컴포넌트 스캔 자동 등록
```java
@Scope("prototype")
@Component
public class HelloBean {}
```
- 수동 등록
```java
@Scope("prototype")
@Bean
PrototypeBean HelloBean() {
    return new HelloBean();
}
```

***
## 프로토타입 스코프
**싱글톤 스코프의 빈**을 조회하면 스프링 컨테이너는 **항상 같은 인스턴스의 스프링 빈을 반환**한다. 반면에 **프로토타입 스코프**를 스프링 컨테이너에 조회하면 **항상 새로운 인스턴스를 생성**해서 반환한다.

> ### 싱글톤 빈 요청
![SingletonRequest](../img/SingletonRequest.png)

> ### 프로토 타입 빈 요청
![PrototypeRequest1](../img/PrototypeRequest.png)
1. 프로토 타입 스코프의 빈을 스프링 컨테이너에 요청한다.
2. 스프링 컨테이너는 이 시점에 프로토 타입 빈을 생성하고, 필요한 의존 관계를 주입한다.
3. 초기화 메서드를 호출한다.
![PrototypeRequest1](../img/PrototypeRequest2.png)
4. 스프링 컨테이너는 생성한 프로토 타입 빈을 클라이언트에 반환한다.
5. 이후에 스프링 컨테이너는 같은 요청이 오면 항상 새로운 프로토타입 빈을 생성해서 반환한다.

여기서 핵심은 스프링 컨테이너는 프로토타입 빈을 생성하고, 의존 관계 주입, 초기화까지만 처리한다는 것이다. 클라이언트에 빈을 반환하고 이후 스프링 컨테이너는 생성된 프로토타입 빈을 관리하지 않는다. 프로토 타입 빈을 관리할 책임은 프로토타입 빈을 받은 클라이언트에 있다. 그래서 ```@PreDestroy``` 같은 종료 메서드가 호출되지 않는다.

> ### 프로토타입 빈-싱글톤 빈 함께 사용 시 문제점
- 스프링 컨테이너에 프로토타입 스코프의 빈을 요청하면 항상 새로운 객체 인스턴스를 생성해서 반환한다. 하지만 싱글톤 빈과 함께 사용할 때는 의도한 대로 잘 동작하지 않으므로 주의해야한다.

- 스프링은 일반적으로 싱글톤 빈을 사용하므로, 싱글톤 빈이 프로토타입 빈을 사용하게 된다. 그런데 **싱글톤 빈은 생성 시점에만 의존관계 주입을 받기 때문에, 프로토타입 빈이 새로 생성되기는 하지만, 싱글톤 빈과 함께 계속 유지되는 것이 문제다**.

***
## 프로토타입 빈을 싱글톤 빈과 함께 사용 시 문제 해결
> ### 스프링 컨테이너에 요청
- 싱글톤 빈이 프로토타입 빈을 사용할 때 마다 스프링 컨테이너에 새로 요청하는 것이다.
- 가장 간단하면서도 무식한 방법이다.

  ```java
  // 싱글톤 빈
  static class ClientBean { 
    
    // 스프링 컨테이너를 주입 받고
    @Autowired 
    private ApplicationContext ac;

    // 싱글톤 빈에서 스프링 컨테이너로 직접 프로토 타입 빈을 요청한다.
    public int logic() { 
      PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
      prototypeBean.addCount();
      return cprototypeBean.getCount();
    }
  }
  ```
- 의존 관계를 외부에서 주입( DI ) 받는게 아니라 이렇게 직접 필요한 의존 관계를 찾는 것을 **Dependency Lookup( DL ) 의존관계 조회( 탐색 )이라 한다.**
- 그런데 이렇게 스프링의 애플리케이션 컨텍스트 전체를 주입받게 되면, 스프링 컨테이너에 종속적인 코드가 되고, 단위 테스트도 어려워진다.
- 지금 필요한 기능은 지정한 프로토타입 빈을 컨테이너에서 대신 찾아주는, 정확이 **DL** 정도의 기능만 제공하는 무언가이다.

> ### ```ObjectFactory```, ```ObejctProvider```
- 지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공하는 것이 ```ObjectProvider```이다.
- 참고로 과거에는 ```ObjectProvider```가 있었는데, 여기에 편의 기능( 옵션, 스트림 처리)을 추가해서 ```ObejctProvider```가 만들어졌다.
  ```java
  @Autowired
  private ObjectProvider<PrototypeBean> prototypeBeanProvider;
  
  public int logic() {
    PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
    return prototypeBean.getCount();
  }
  ```
- ```ObjectProvider``` 의 ```getObject()``` 를 호출하면 내부에서는 **스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다.(DL)**

> ### JSR-330 ```Provider```
- 마지막 방법은 ```javax.inject.Provider``` 라는 JSR-330 자바 표준을 사용하는 방법이다.
- 이 방법을 사용하려면 ```javax.inject:javax.inject:1``` 라이브러리를 gradle에 추가해야 한다.

  ```java
  package javax.inject;
  public interface Provider<T> {
    T get();
  }
  ```
  ```java
  //implementation 'javax.inject:javax.inject:1' gradle 추가 필수
  @Autowired
  private Provider<PrototypeBean> provider;
  
  public int logic() {
    PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
    return prototypeBean.getCount();
  }
  ```
- Provider는 자바 표준이고, 기능이 단순하므로 단위 테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워진다.
- Provider는 자바 표준이므로 스프링이 아닌 다른 컨테이너에서도 사용할 수 있다.

***
## 정리
- ```ObjectProvider``` , ```JSR330 Provider``` 등은 프로토타입 뿐만 아니라 **DL이 필요한 경우는 언제든지 사용할 수 있다.**

***
## request scope
request scope는 실제 클라이언트의 요청이 들어왔을 때 생성된다. 클라이언트의 요청이 들어오지 않아도 request scope 빈을 호출할 수 있는 방법이 있다.
> ### 스코프와 Provider
- 컨트롤러
  ```java
  @Controller
  @RequiredArgsConstructor
  public class LogDemoController {
    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
      String requestURL = request.getRequestURL().toString();
      MyLogger myLogger = myLoggerProvider.getObject();
      
      myLogger.setRequestURL(requestURL);
      myLogger.log("controller test");
      logDemoService.logic("testId");
      return "OK";
    }
  }
  ```
- 서비스
  ```java
  @Service
  @RequiredArgsConstructor
  public class LogDemoService {
    private final ObjectProvider<MyLogger> myLoggerProvider;
    
    public void logic(String id) {
      MyLogger myLogger = myLoggerProvider.getObject();
      myLogger.log("service id = " + id);
    }
  }
  ```
- ```ObjectProvider``` 덕분에 ```ObjectProvider.getObject()``` 를 호출하는 시점까지 **request scope 빈의 생성을 지연할 수 있다.**
- ```ObjectProvider.getObject()``` 를 호출하시는 시점에는 HTTP 요청이 진행중이므로 request scope 빈의 생성이 정상 처리된다.
- Provider를 사용하면 코드가 약간 더러워진다. 이를 해결하기 위한 방법 또한 존재한다.

***
## 스코프와 프록시
```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
}
```
- 이렇게 하면 MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관없이 가짜 프록시 클래스를 다른 빈에 미리 주입해둘 수 있다.
- 프록시 사용 후 컨트롤러
  ```java
  @Controller
  @RequiredArgsConstructor
  public class LogDemoController {

      private final LogDemoService logDemoService;
      private final MyLogger myLogger;

      @RequestMapping("log-demo")
      @ResponseBody
      public String logDemo(HttpServletRequest request) {
          String requestURL = request.getRequestURL().toString();

          System.out.println("myLogger = " + myLogger.getClass());
          myLogger.setRequestURL(requestURL);

          myLogger.log("controller test");
          logDemoService.logic("testId");
          return "OK";
      }
  }
  ```
- 프록시 사용 후 서비스
  ```java
  @Service
  @RequiredArgsConstructor
  public class LogDemoService {

      private final MyLogger myLogger;

      public void logic(String testId) {
          myLogger.log("service id = " + testId);
      }
  }
  ```

***
## 웹 스코프와 프록시 동작원리
스코프를 사용한 이후 주입된 myLogger를 확인해보자.
```java
System.out.println("myLogger = " + myLogger.getClass());
```
**출력결과**
```
myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$b68b726d
```
> ### CGLIB이라는 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체를 만들어서 주입한다.
- ```@Scope``` 의 ```proxyMode = ScopedProxyMode.TARGET_CLASS)``` 를 설정하면 스프링 컨테이너는 **CGLIB** 라는 바이트코드를 조작하는 라이브러리를 사용해서, ```MyLogger```를 상속받은 가짜 프록시 객체를 생성한다.
- 그리고 스프링 컨테이너에 "myLogger"라는 이름으로 진짜 대신에 이 가짜 프록시 객체를 등록한다.
- 그래서 의존관계 주입도 이 가짜 프록시 객체가 주입된다.

> ### 가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.
- 가짜 프록시 객체는 내부에 진짜 myLogger를 찾는 방법을 알고 있다.
- 클라이언트가 ```myLogger.logic()```을 호출하면 사실은 가짜 프록시 객체의 메서드를 호출한 것이다.
- 가짜 프록시 객체는 request 스코프의 진짜 ```myLogger.logic()```을 호출한다.
- 가짜 프록시 객체는 원본 클래스를 상속받아서 만들어졌기 때문에 이 객체를 사용하는 클라이언트 입장에서는 사실 원본인지 아닌지도 모르게, 동일하게 사용할 수 있다.( 다형성 )
  
> ### 동작 정리
- CGLIB이라는 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체를 만들어서 주입한다.
- 이 가짜 프록시 객체는 실제 요청이 오면 그때 내부에서 실제 빈을 요청하는 위임 로직이 들어있다.
- 가짜 프록시 객체는 실제 request scope와는 관계가 없다. 그냥 가짜이고, 내부에 단순한 위임 로직만 있고, 싱글톤 처럼 동작한다.
  
> ### 특징 정리
- 프록시 객체 덕분에 클라이언트는 마치 싱글톤 빈을 사용하듯이 request scope를 사용할 수 있다.
- 사실 Provider를 사용하든 프록시를 사용하든 핵심 아이디어는 ***진짜 객체 조회를 꼭 필요한 시점까지 지연처리 한다는 점이다.***
  - request scope 빈을 미리 가짜 프록시 객체로 주입 받아두고
  - 실제 요청이 들어왔을 때! 그때 진짜 객체를 조회한다는 의미
- ***단지 어노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있다. 이것이 바로 다형성과 DI 컨테이너가 가진 큰 장점이다.***
- 꼭 웹 스코프가 아니어도 프록시는 사용할 수 있다.

> ### 주의점
- 마치 싱글톤처럼 사용하는 것 같지만 다르게 동작하기 때문에 결국 주의해서 사용해야 한다.
- 이런 특별한 scope는 꼭 필요한 곳에서만 최소화해서 사용하자. 무분별하게 사용하면 유지보수하기 어려워진다.
  - request scope 사용 예시: 공통 로그를 찾기위해 사용하기도 한다.


