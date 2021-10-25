# 의존관계 자동주입
## 다양한 의존관계 주입 방법
- 생성자 주입
- 수정자 주입( setter )
- 필드 주입
- 일반 메서드 주입

> ### 생성자 주입
- 이름 그대로 생성자를 통해서 의존 관계를 주입 받는 방법이다.
- 생성자 호출 시점에 딱 1번만 호출되는 것이 보장된다.
- 불변, 필수 의존 관계에 사용된다.
- **생성자가 1개만 있으면 ```@Autowired```를 생략해도 자동 주입 된다.( 스프링 빈에만 해당 )**
    ```java
    @Component
    public class OrderServiceImpl implements OrderService {
        private final MemberRepository memberRepository;
        private final DiscountPolicy discountPolicy;
       
        @Autowired
        public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
            this.memberRepository = memberRepository;
            this.discountPolicy = discountPolicy;
        }
    }
    ```

> ### 수정자 주입( setter 주입 )
- ```setter```라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존 관계를 주입하는 방법이다.
- 선택, 변경 가능성이 있는 의존 관계에 사용한다.( 중간에 DB를 변경해야 한다던지 )
- **자바빈 프로퍼티 규약**의 수정자 메서드 방식을 사용하는 방법이다.
    ```java
    @Component
    public class OrderServiceImpl implements OrderService {
        private MemberRepository memberRepository;
        private DiscountPolicy discountPolicy;
        
        @Autowired
        public void setMemberRepository(MemberRepository memberRepository) {
            this.memberRepository = memberRepository;
        }
        
        @Autowired
        public void setDiscountPolicy(DiscountPolicy discountPolicy) {
            this.discountPolicy = discountPolicy;
        }
    }
    ```

> ### 필드 주입( 사용하지 않는 것을 권장한다. )
- 이름 그대로 필드에 바로 주입하는 방법이다.
- 코드가 간결해서 유용해보이지만, ***외부에서 변경이 불가능 하기때문에 테스트하기 힘들다는 치명적인 단점이 있다.***
  - 다른 의존 관계 주입 방식은 생성자나 ```setter```를 통해 외부에서 의존 관계를 변경할 수 있다.( 테스트 용도 )
- **참고:** 다음 코드와 같이 ```@Bean``` 에서 파라미터에 의존관계는 자동 주입된다. 수동 등록시 자동 등록된 빈의 의존관계가 필요할 때 문제를 해결할 수 있다.
    ```java
    @Bean
    OrderService orderService(MemberRepository memberRepoisitory, DiscountPolicy
    discountPolicy) {
        new OrderServiceImpl(memberRepository, discountPolicy)
    }
    ```

***
## 생성자 주입을 선택하라!
과거에는 수정자 주입과 필드 주입을 많이 사용했지만, 최근에는 스프링을 포함한 DI 프레임워크 대부분이 생성자 주입을 권장한다. 그 이유는 다음과 같다.

> ### 불변
- 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존 관계를 변경할 일이 없다. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다.( 불변해야 한다. )
- 수정자 주입을 사용하면, setXxx 메서드를 ```public```으로 열어두어야한다.
- 이 경우 누군가 실수로 값을 변경할 수도 있다. 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다.
- ***생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 불변하게 설계할 수 있다.***

> ### 누락
- **생성자 주입**을 사용하면 **주입 데이터를 누락**했을 때( 매개인자가 필요한 생성자에 아무런 값도 넘겨주지 않았을 때 ) **컴파일 오류**가 발생한다.
- 그리고 IDE에서 바로 어떤 값을 필수로 주입해아하는지 알 수 있다.

> ### ```final``` 키워드
- 생성자 주입을 사용하면 field에 **```final```**키워드를 사용할 수 있다.
  - 생성자 주입을 사용하면 객체가 생성될 때 값이 설정된 이후 해당 값이 절대 변하지 않기 때문이다.( 불변 )
- 그래서 생성자에서 혹시라도 값이 설정되지 않는 오류를 ```컴파일 시점```에서 막아준다.
- **참고!** 수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 ```final``` 키워드를 사용할 수 없다. **오직 생성자 주입 방식만 ```final``` 키워드를 사용할 수 있다.**

***
## 롬복과 최신 트렌드
막상 개발을 해보면, 대부분이 다 불변이다. 그래서 생성자에 final 키워드를 사용하게 된다. 그런데 생성자도 만들어야하고, 주입 받은 값을 대입하는 코드도 만들어야하고... 필드 주입처럼 생성자 주입을 편리하게 사용하는 방법은 없을까?

> ### 최신 트렌드
최근에는 생성자를 딱 1개만 두고, ```@Autowired```를 생략하는 방법을 주로 사용한다. 여기에 Lombok 라이브러리의 ```@RequiredArgsConstructor``` 어노테이션을 함께 사용하면 기능은 다 제공하면서, 코드는 깔끔하게 사용할 수 있다.

- 생성자가 딱 1개만 있으면 ```@Autowired```를 생략할 수 있다.
- 롬복 라이브러리가 제공하는 ```@RequiredArgsConstructor``` 기능을 사용하면 ```final```이 붙은 필드를 모아서 생성자를 자동으로 만들어준다. 

***
## ```@Autowired``` 필드명 ```@Primary```, ```@Qualifier```
조회 대상 빈이 2개 이상일 때 해결 방법
- ```@Autowired``` 필드명 매칭
- ```@Qualifier``` : ```@Qualifier```에 설정된 빈 이름으로 매칭
- ```@Primary``` 사용

> ### ```@Autowired``` 매칭
- ```@Autowired```는 우선적으로 타입을 먼저 매칭시킨다.
- 타입 매칭의 결과가 2개 이상일 때는 필드명, 파라미터 명으로 빈 이름을 매칭한다.

> ### ```@Qualifier```
- ```@Qualifier```는 추가 구분자를 붙여주는 방법이다.
- 의존 관계 주입 시 추가적인 방법을 제공하는 것이지 **빈 이름을 변경하는 것은 아니다.**
- 사용 방법은 빈 등록시 ```@Qualifier```를 붙여주면 된다.
    ```java
    @Component
    @Qualifier("mainDiscountPolicy")
    public class RateDiscountPolicy implements DiscountPolicy {}
    ```
    ```java
    @Component
    @Qualifier("fixDiscountPolicy")
    public class FixDiscountPolicy implements DiscountPolicy {
    ```
- 주입 시 매개인자 앞에 ```@Qualifier```를 붙여주고 등록한 이름을 적어준다.
    ```java
    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository,
        @Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {

        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
    ```
- ***의문점) 이렇게 되면 OCP를 위반하는 것이 아닌가?***

> ### ```@Primary```
- ```@Primary```는 우선순위를 정하는 방법이다.
- ```@Autowired```시에 여러 빈이 매칭되면 ```@Primary```가 우선권을 가진다.
    ```java
    @Component
    @Primary // 우선권을 갖는다.
    public class RateDiscountPolicy implements DiscountPolicy {}
    
    @Component
    public class FixDiscountPolicy implements DiscountPolicy {}
    ```
- ```@Primary```를 사용하면 클라이언트 코드에는 아무런 변화를 주지 않아도 된다.

> ### ```@Primary```, ```@Qualifier``` 활용
- 코드에서 자주 사용하는 메인 DB의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 서브 DB의 커넥션을 획득하는 스프링 빈이 있다고 생각해보자. 메인 DB의 커넥션을 획득하는 스프링 빈은 ```@Primary```를 적용해서, 조회하는 곳에서 ```@Qualifier``` 지정 없이 편리하게 조회하고, 서브 DB의 커넥션 빈을 획득할 때는 ```@Qualifier```를 지정해서 명시적으로 획득하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있다. 물론 이때 메인 DB의 스프링 빈을 등록할 때 ```@Qualifier```를 지정해주는 것은 상관없다.

> ### 우선순위
- ```@Primary```는 기본값 처럼 동작하고, ```@Qualifier```는 매우 상세하게 동작한다. 
- 스프링은 자동보다는 수동이, 넓은 범위의 선택권 보다는 좁은 범위의 선택권이 우선순위가 높다. 따라서 여기서도 ```@Qualifier```에 우선권이 있다.

***
## 조회한 빈이 모두 필요할때, List, Map
의도적으로 해당 타입의 스프링 빈이 다 필요한 경우도 있다. 예를 들어서 할인 서비스를 제공하는데, 클라이언트가 할인의 종류( rate, fix )를 선택할 수 있다고 가정해보자. 스프링을 사용하면 소위 말하는 **전략 패턴**을 매우 간단하게 구현할 수 있다.

- ```Map```으로 한번에 주입 받는법
    ```java
    public DiscountService(Map<String, DiscountPolicy> policyMap) { ... }
    ```
  - ```Map```으로 주입받을 때 ```key```값은 스프링에 자동 등록된 빈 이름으로 설정된다.
- ```List```로 한번에 주입 받는법
    ```java
    public DiscountService(List<DiscountPolicy> policies) ) { ... }
    ```

***
## 실무에서의 자동 빈 등록과 수동 빈 등록
애플리케이션은 크게 업무 로직과 기술 지원 로직으로 나눌 수 있다.
> ### 업무 로직 빈
- 웹을 지원하는 컨트롤러
- 핵심 비즈니스 로직이 있는 서비스
- 데이터 계층의 로직을 처리하는 리포지토리등이 모두 업무 로직이다.
- 보통 비즈니스 요구사항을 개발할 때 추가되거나 변경된다.
> ### 기술 지원 빈
- 기술적인 문제나 공통 관심사( AOP )를 처리할 때 주로 사용된다.
- 데이터 베이스  연결이나, 공통 로그 처리 처럼 업무 로직을 지원하기 위한 하부 기술이나 공통 기술들이다.
> ### 수동 빈 등록은 언제 사용하면 좋을까?
- **업무 로직**은 숫자도 매우 많고, 한번 개발해야 하면 컨트롤러, 서비스, 리포지토리 처럼 어느정도 유사한 패턴이 있다. 이런 경우 자동 기능을 적극 사용하는 것이 좋다. 보통 문제가 발생해도 어떤 곳에서 문제가 발생했는지 명확하게 파악하기 쉽다.
- **기술 지원 로직**은 업무 로직과 비교해서 그 수가 매우 적다. 보통 애플리케이션 전반에 걸쳐서 광범위하게 영향을 미친다. 그리고 업무 로직은 문제가 발생했을 때 어디가 문제인지 명확하게 잘 드러나지만, 기술 지원 로직은 적용이 잘 되고 있는지 아닌지 조차 파악하기 어려운 경우가 많다. 그래서 이런 기술 지원 로직들은 가급적 수동 빈 등록을 사용해서 명확하게 드러내는 것이 좋다.

***
## 부록
> ### ```@Autowired```에 주입할 대상이 없다면?
- 오류가 발생한다. 주입할 대상이 없어도 동작하게 하려면 ```@Autowired(required = false)``` 로 지정하면 된다.

> ### 어쩌면 당연한 이야기지만...
-  어쩌면 당연한 이야기이지만 의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동
작한다. 스프링 빈이 아닌 ```Member``` 같은 클래스에서 ```@Autowired``` 코드를 적용해도 아무 기능도 동작하지 않는다.