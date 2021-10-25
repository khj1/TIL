# Bean LifeCycle Callback
데이터 베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 진행하려면, 객체의 초기화와 종료 작업이 필요하다.

- 스프링 빈은 간단하게 다음과 같은 라이프 사이클을 가진다.
  - **객체 생성** -> **의존 관계 주입**
- 스프링 빈은 객체를 생성하고, 의존 관계 주입이 다 끝난 다음에야 필요한 데이터를 사용할 수 있는 준비가 완료된다,
- 따라서 초기화 작업은 의존 관계 주입이 모두 완료되고 난 다음에 호출해야 한다. 그런데 개발자가 의존 관계 주입이 모두 완료된 시점을 어떻게 알 수 있을까?
- ***스프링은 의존 관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다.***
- 또한 ***스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 준다.***

> ### 스프링 빈의 이벤트 라이프 사이클
1. 스프링 컨테이너 생성
2. 스프링 빈 생성
3. 의존 관계 주입
4. 초기화 콜백
5. 스프링 빈 사용
6. 소멸 전 콜백
7. 스프링 컨테이너 종료

- **참고: 객체의 생성과 초기화를 분리하자.**
  - 생성자는 필수 정보( 파라미터 )를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다.
  - 반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행한다.
  - 따라서 생성자 안에서 무거운 초기화 작업을 함께하는 것 보다는 **객체를 생성하는 부분과 초기화하는 부분을 명확하게 나누는 것**이 유지보수 관점에서 좋다.( **단일 책임의 원칙** )  
  - 물론 초기화 작업이 내부 값들만 약간 변경하는 정도로 단순한 경우에는 생성자에서 한번에 다 처리하는게 더 나을 수 있다.
- 스프링은 크게 3가지 방법으로 빈 생명주기 콜백을 지원한다.
  - 인터페이스( InitializingBean, DisposableBean ) ( 거의 사용하지 않는다. )
  - 설정 정보에 초기화 메서드, 종료 메서드 지정
  - ```@PostConstruct```, ```@PreDestroy``` 어노테이션 지원

***
## ```@PostConstruct```, ```@PreDestroy``` 어노테이션
- 빈의 초기화 메서드와 종료 메서드를 지정하기 위해 최신 스프링에서 가장 권장하는 방법이다.
- 어노테이션 하나만 붙이면 되므로 매우 편리하다.
- 해당 어노테이션의 패키지를 잘 보면 ```javax.annotation.PostConstruct```이다. 스프링에 종속적인 기술이 아니라 JSR-250이라는 자바 표준이다. 따라서 스프링이 아닌 다른 컨테이너에서도 동작한다.
- 컴포넌트 스캔과 잘 어울린다.
- 유일한 단점은 외부 라이브러리에는 적용하지 못한다는 것이다. 외부 라이브러리를 초기화, 종료해야만 한다면 ```@Bean``` 어노테이션의 ```initMethod```, ```destroyMethod```속성을 활용하면된다.

***
## 예제
> ### 스프링 빈
    ```java
    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;

    public class NetworkClient {

        private String url;

        public NetworkClient() {
            System.out.println("생성자 호출, url = " + url);
        }

        // 의존 관계 주입
        public void setUrl(String url) {
            System.out.println("NetworkClient.setUrl");
            this.url = url;
        }

        // 서비스 시작시 호출
        public void connect() {
            System.out.println("connect= " + url);
        }

        public void call(String message) {
            System.out.println("call = " + url + ", message = " + message);
        }

        // 서비스 종료 시 호출
        public void disconnect() {
            System.out.println("close = " + url);
        }

        @PostConstruct
        public void init() {
            System.out.println("NetworkClient.init");
            connect();
            call("초기화 연결 메세지");
        }

        @PreDestroy
        public void close() {
            System.out.println("NetworkClient.close");
            disconnect();
        }
    }
    ```
> ### 테스트 코드
    ```java
    public class BeanLifeCycleTest {

        @Test
        public void lifeCycleTest() {
            ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
            NetworkClient client = ac.getBean(NetworkClient.class);
            ac.close();
        }

        @Configuration
        static class LifeCycleConfig {

            @Bean
            public NetworkClient networkClient() {
                NetworkClient networkClient = new NetworkClient();
                networkClient.setUrl("http://hello-spring.dev");
                return networkClient;
            }
        }
    }
    ```
> ### 실행 결과
```java
생성자 호출, url = null
NetworkClient.setUrl
NetworkClient.init
connect = http://hello-spring.dev
call = http://hello-spring.dev, message = 초기화 연결 메세지
22:30:20.765 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@32ee6fee, started on Thu Jul 15 22:30:20 KST 2021
NetworkClient.close
close = http://hello-spring.dev
```