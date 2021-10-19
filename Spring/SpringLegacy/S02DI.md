# Spring - DI
## DI (Dependency Injection) - 의존성 주입
- 스프링에서 가장 중요한 개념이다.
- IoC(Inversion of Control : 역제어)의 한 형태이다.
- 의존성 주입 또는 종속 개체 주입이라고 한다.
- 의존성이란 객체와 객체 사이에 존재하는 관계를 의미한다.
- 하나의 객체 안에서 다른 객체를 new 연산자를 이용해서 생성 시 두 객체 간의 결합도가 높아지고 독립성이 떨어지게된다.
- 객체 스스로 new를 통해 필요한 객체를 생성하지 않고 설정파일(xml) 즉 외부에서 주입을 받아 낮은 결합도를 유지하는게 목적이다.
    ```java
    public class DITest {
        /*
        강한 결합(독립성 낮음) : new를 통해 직접 객체를 생성함.
            결합도가 높기때문에 Person 클래스의 변화에 직접적인 영향을 받는다.
        */
        public static void aPerson() {
            Persons person1 = new Persons();
            person1.age = 28;
        }
        
        /*
        약한 결합(독립성 높음) : 미리 생성된 객체를 주입(Injection) 받음.
            결합도가 낮아지기 때문에 Person 클래스에 변화가 생기더라도
            직접적인 영향을 받지 않는다. 또한 코드도 좀 더 간결해진다.
        */
        public static void bPerson(Persons p) {
            p.age = 28;
        }
    }
    ```
***
## DI 예제
### XML 설정 파일을 통해 ```bean``` 객체 생성
- **```bean``` 생성 시 메소드명이 ```bean``` 객체의 참조변수가 됨을 기억하자!**
  - 컨트롤러
    ```java
    @RequestMapping("/di/mydi1.do")
	public ModelAndView mydi1(Model model) {
		ModelAndView mv = new ModelAndView();
		
		// XML 설정파일의 위치를 저장
		String configLocation = "classpath:my_di1.xml";
		
		// 설정파일을 통해 스프링 컨테이너를 생성
		AbstractApplicationContext ctx =
				new GenericXmlApplicationContext(configLocation);
		
		// getBean()으로 Circle 타입의 객체 circle을 주입받음.
		Circle circle = ctx.getBean("circle", Circle.class);
		
		mv.setViewName("04DI/mydi1");
		mv.addObject("circle", circle);
		
		return mv;
	}
    ```
  - XML 파일 (my_di1.xml)
    ```xml
    <!-- 
	아래 bean 태그를 Java 코드로 표현하면 다음과 같다
		
		import di.Point;
		Point pointXY = new Point(3, 4);
	 -->
	<bean id="pointXY" class="di.Point">
		<constructor-arg value ="3"/>
		<constructor-arg>
			<value>4</value>
		</constructor-arg>
	</bean>
	
	<!-- 
	<property>는 setter()를 통한 초기화를 진행한다.
	아래 bean 태그를 Java 코드로 표현하면 다음과 같다.
		
		import di.Circle;
		Circle circle = new Circle();
		circle.setPoint(pointXY);
		circle.setRadian(20);
	 -->
	<bean id="circle" class="di.Circle">
		<property name="point">
			<ref bean="pointXY"/>
		</property>
		<property name="radian" value="20" />
	</bean>

    <!-- ArrayList<> 타입의 멤버 변수 초기화 -->
    <property name="hobbys">
        <list>
            <value>자전거</value>
            <value>퀵보드</value>
            <value>레고</value>
        </list>
    </property>
    ```
***
### 어노테이션을 통한 빈 생성 및 주입받기
  - 컨트롤러
    ```java
    @RequestMapping("/di/mydi3.do")
	public ModelAndView mydi3(Model model) {
		ModelAndView mv = new ModelAndView();
		
		// 어노테이션을 통해 생성된 빈을 받기위한 컨텍스트 선언
		AnnotationConfigApplicationContext aCtx =
				new AnnotationConfigApplicationContext(AnnotationBean.class);
		
		// 빈 주입받기
		Circle circle1 = aCtx.getBean("circleBean", Circle.class);
		Person person1 = aCtx.getBean("personBean", Person.class);
		
		mv.setViewName("04DI/mydi3");
		mv.addObject("person", person1.getInfo());
		mv.addObject("circle", circle1);
		
		return mv;
	}
    ```
  - AnnotationBean 클래스 파일
    ```java
    /*
    @Configuration
        해당 클래스를 스프링 설정 파일로 사용하겠다는 선언으로
        XML 설정 파일을 사용하는 대신 Java 파일을 설정파일로 사용한다.
    */
    @Configuration
    public class AnnotationBean {
        
        /*
        @Bean
            <bean> 태그와 같이 자바빈을 생성할 때 선언한다.
            생성된 빈은 컨트롤러에서 getBean()을 통해 주입받을 수 있다.
            
            메소드명은 주입받을 때의 참조변수 명으로 사용한다.
            즉 Circle circleBean = new Circle()과 동일하다.
        */
        @Bean
        public Circle circleBean() {
            Point point2 = new Point(7, 8);
            Circle circle2 = new Circle();
            circle2.setPoint(point2);
            circle2.setRadian(20);
            
            return circle2;
        }
        
        @Bean
        public Person personBean() {
            Person person2 = new Person();
            person2.setName("김한준");
            person2.setAge(28);
            
            ArrayList<String> hobbys = new ArrayList<String>();
            hobbys.add("기타");
            hobbys.add("칼림바");
            hobbys.add("피아노");
            person2.setHobbys(hobbys);
            
            return person2;
        }
    }
    ```