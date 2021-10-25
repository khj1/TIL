# Logging( 로깅 )
운영 시스템에서는 ```System.out.println()``` 같은 시스템 콘솔을 사용해서 정보를 출력하지 않고 별도의 로깅 라이브러리를 사용해서 로그를 출력한다.

## 로깅 라이브러리
스프링 부트 라이브러리를 사용하면 스프링 부트 로깅 라이브러리( `spring-boot-starter-logging` )가 함께 포함된다.

스프링 부트 로깅 라이브러리는 기본으로 다음 로깅 라이브러리를 사용한다.
- SLF4J - http://www.slf4j.org
- Logback - http://logback.qos.ch

로그 라이브러리는 Logback, Log4J, Log4J2 등등 수 많은 라이브러리가 있는데, 그것을 통합해서 인터페이스로 제공하는 것이 바로 **SLF4J 라이브러리**다.
- SLF4J는 인터페이스, Logback, Log4J, Log4J2는 구현체

## 로그 선언
- `private Logger log = LoggerFactory.getLogger(getClass());`
- `@Slf4j` 어노테이션을 사용하면 별도의 선언없이 로그를 바로 사용할 수 있다.

## 로그 사용
```java
log.trace("trace log={}, {}", name1, name2);
log.debug("debug log={}", name);
log.info(" info log={}", name);
log.warn(" warn log={}", name);
log.error("error log={}", name);
```

**주의!** *다음과 같은 방식으로 로그를 사용하면 안된다.*
```java
log.debug("debug log= " + name);
```
- 문법적으로 틀린 방법은 아니지만 **메모리 낭비가 발생하는 방법**이다.
- 로그를 사용하지 않아도 '+' 계산 로직이 먼저 실행된다.

## 로그 레벨 설정
`application.properties`
### 전체 로그 레벨 설정( default info )
```java
logging.level.root=info
```
### 패키지를 지정하고 그 하위의 로그 레벨을 설정
```java
logging.level.hello.springmvc=debug
```

## 로그를 사용해야하는 이유
- 쓰레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수 있다.
  - `2021-07-23 21:41:01.949  INFO 17192 --- [nio-9090-exec-1] hello.springmvc.basic.LogTestController  : info log= Spring`
- 로그 레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영 서버에서는 출력하지 않는 등 **로그를 상황에 맞게 조절할 수 있다.**
  - *별도로 코드를 변경하지 않고 설정 파일의 내용만 수정해주면 로그 조절이 가능하다는게 큰 장점 중 하나이다.*
- 콘솔에만 출력하는 것이 아니라, 파일이나 네트워크 등 로그를 별도의 위치에 남길 수 있다.
  - 특히 파일로 남길때는 일별, 특정 용량에 따라 로그를 분할하는 것도 가능하다.
- 성능도 ```System.out.println()```보다 뛰어나다.
  - 내부 버퍼링
  - 멀티 쓰레드

## 참고
### 로그에 대한 자세한 내용
- SLF4J - http://www.slf4j.org
- Logback - http://logback.qos.ch
### 스프링 부트가 제공하는 로그 기능
- https://docs.spring.io/spring-boot/docs/current/reference/html/spring-bootfeatures.html#boot-features-logging