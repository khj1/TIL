# Spring - 외부파일 참조하기
## Environment 객체를 활용하는 방법
```java
@RequestMapping("/environment/main1")
public String main1(Model model) {
    
    // 1. 스프링 컨테이너 생성
    ConfigurableApplicationContext ctx = 
            new GenericApplicationContext();
    // 2. Environment 객체 생성
    ConfigurableEnvironment env = ctx.getEnvironment();
    // 3. 외부파일을 읽어올 준비를 한다.
    MutablePropertySources propertySources = env.getPropertySources();
    
    String adminIdStr = "";
    String adminPwStr = "";
    
    try {
        // 4. 외부파일의 경로를 지정한 후 addLast()로 프로퍼티 소스를 추가한다.
        /*
        classpath
            해당 키워드는 프로젝트를 배포했을 때 classes 폴더 하위로 export되는 경로를 가리킨다. 우리가 작성했던 java 파일이나 그외 xml 파일등이 포함된다.
            */
        String envPath = "classpath:EnvAdmin.properties";
        propertySources.addLast(new ResourcePropertySource(envPath));
        
        // 5. 파일의 내용을 읽어 변수에 저장한다.
        adminIdStr = env.getProperty("admin.id");
        adminPwStr = env.getProperty("admin.pw");
    }
    catch (Exception e) {
        e.printStackTrace();
    }
    
    // Model 객체에 속성 저장
    model.addAttribute("adminId", adminIdStr);
    model.addAttribute("adminPw", adminPwStr);
    
    return "05Environment/main1";
}
```
***

## XML 설정 파일을 활용하는 방법
XML 파일에서 프로퍼티 파일을 명시한 후 직접 빈을 생성하여 주입받는 방법
```java
@RequestMapping("/environment/main2")
public String main2(Model model) {
    
    /*
    프로퍼티 파일의 위치를 명시한 XML 설정 파일을 통해 
    스프링 컨테이너를 생성한다.
    */
    AbstractApplicationContext ctx = 
            new GenericXmlApplicationContext("classpath:EnvAppCtx.xml");
    
    // 프로퍼티 파일의 값을 저장한 빈(자바 객체)을 주입받는다.
    UserConnection userCon =
            ctx.getBean("userConnection", UserConnection.class);
    
    // Model 객체에 저장한 후 뷰를 호출한다.
    model.addAttribute("mainUserId", userCon.getMainUserId());
    model.addAttribute("mainUserPw", userCon.getMainUserPw());
    model.addAttribute("subUserId", userCon.getSubUserId());
    model.addAttribute("subUserPw", userCon.getSubUserPw());
    
    return "05Environment/main2";
}
```
- EnvAppCtx.xml 파일
```xml
<!-- 
	Environment 객체를 사용하지 않고 XML 설정 파일에서 바로 외부파일을
	가져오기 위해 context 네임 스페이스를 추가한다. 파일 생성 시 추가할
	수 있고, 추가 후 <context> 태그를 사용할 수 있다.
-->
<!-- 
    context 엘리먼트에서 프로퍼티 파일의 경로를 지정하여 읽을 준비를 한다.
    파일이 2개 이상인 경우 콤마로 구분하면 된다.
-->
<context:property-placeholder
		location=
           "classpath:EnvUser1.properties,
			classpath:EnvUser2.properties"/>
<!-- 
    bean 태그를 java 태그로 옮기면 다음과 같다.
    
        import env.UserConnection;
        UserConnection userConnection = new UserConnection();
    
    즉 bean 태그는 class 속성에 지정된 클래스를 통해 객체를 생성한다.
    이때 참조변수는 id 속성에 지정한다.
-->
<bean id="userConnection" class="env.UserConnection">
    <!-- property 태그에서는 해당 객체의 멤버 변수에 값을 저장한다. -->
    <property name="mainUserId">
        <value>${mainUser.id}</value>
    </property>	
    <property name="mainUserPw">
        <value>${mainUser.pw}</value>
    </property>	
    <property name="subUserId">
        <value>${subUser.id}</value>
    </property>	
    <property name="subUserPw">
        <value>${subUser.pw}</value>
    </property>	
</bean>

```
***

## 어노테이션을 이용한 외부파일 참조.
설정파일 대신 ```EnvApplicationConfig``` 클래스를 이용하여 외부 파일을 읽어온다.
- 컨트롤러
```java
@RequestMapping("/environment/main3")
	public String main3(Model model) {
		
		// 어노테이션 기반 스프링 컨텍스트 생성
		AnnotationConfigApplicationContext ctx =
				new AnnotationConfigApplicationContext(EnvApplicationConfig.class);
		
		// 설정 파일에서 생성한 빈 주입받기
		/*
		어노테이션을 통해 XML 설정 파일을 대체하는 경우 @Bean을 붙인 메소드 명이
		Bean의 참조변수가 된다.
		
			@Bean
			public BoardConnection boardConfig() {
			  => BoardConnection boardConfig = new BoardConnection() 와 동일하다
		 */
		BoardConnection bConn =
				ctx.getBean("boardConfig", BoardConnection.class);
		// 참조변수 이름이 boardConfig인 BoardConnection 타입의 객체를 주입한다는 의미
		
		model.addAttribute("id", bConn.getUser());
		model.addAttribute("pass", bConn.getPass());
		model.addAttribute("driver", bConn.getDriver());
		model.addAttribute("url", bConn.getUrl());
		
		return "05Environment/main3";
	}
```
- ```EnvApplicationConfig``` 클래스 파일
```java
/*
@Configuration
	XML 설정파일의 역할을 대신하는 클래스로 정의한다.
 */
@Configuration
public class EnvApplicationConfig {
	
	// 외부 파일의 경로를 설정하고 읽어오기
	@Bean
	public static PropertySourcesPlaceholderConfigurer Properties() {
		// 1. 프로퍼티 파일을 읽어오기 위한 객체 생성
		PropertySourcesPlaceholderConfigurer config =
				new PropertySourcesPlaceholderConfigurer();

		// 2. 프로퍼티 파일의 위치를 설정하기 위한 Resource 타입의 객체 생성
		Resource[] locations = new Resource[2];
		locations[0] = new ClassPathResource("EnvBoard1.properties");
		locations[1] = new ClassPathResource("EnvBoard2.properties");
		
		// 3. 설정된 위치의 config 참조 변수의 매개변수로 파일을 읽어옴
		config.setLocations(locations);
		
		return config;
	}
	
	
	// @Value : 멤버 변수 초기값을 어노테이션으로 정의한다.
	@Value("${board1.user}")
	private String board_user;
	@Value("${board1.pass}")
	private String board_pass;
	@Value("${board2.driver}")
	private String board_driver;
	@Value("${board2.url}")
	private String board_url;
	
	
	// 프로퍼티 소스를 통해 읽어온 값으로 빈을 생성함
	@Bean
	public BoardConnection boardConfig() {
		// DTO 객체를 생성하고 setter()를 통해 초기값을 설정한다.
		BoardConnection bconn = new BoardConnection();
		
		bconn.setUser(board_user);
		bconn.setPass(board_pass);
		bconn.setDriver(board_driver);
		bconn.setUrl(board_url);
		
		return bconn;
	}
}
```
***
## 부록
- [스프링 Environment객체를 사용하여 원하는 정보 불러오기](https://it-pig.tistory.com/24)