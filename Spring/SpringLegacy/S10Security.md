# Spring - Security
스프링 시큐리티는 스프링 기반의 애플리케이션 보안( 인증과 권한, 인가 등 )을 담당하는 스프링 하위 프레임워크다.
![Security](../img/Security.png)
## 기본 설정
> ### pom.xml
```xml
<!-- 스프링 시큐리티를 사용하기 위한 의존 설정 -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>4.2.1.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>4.2.1.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-taglibs</artifactId>
    <version>4.2.1.RELEASE</version>
</dependency>
```
> ### web.xml
- 스프링 시큐리티 설정 파일 생성 및 위치 지정
    ```xml
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>
            /WEB-INF/spring/root-context.xml
            /WEB-INF/spring/security-context.xml <!-- 
                security 설정파일을 생성하고 경로에 추가해줘야 한다. 
            -->
        </param-value>
    </context-param>
    ```
- Security 환경에서 파일 업로드를 위한 필터
    ```xml
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <filter>
        <display-name>springMultipartFilter</display-name>
        <filter-name>springMultipartFilter</filter-name>
        <filter-class>org.springframework.web.multipart.support.MultipartFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>springMultipartFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ```
- 시큐리티를 해당 웹 어플리케이션에 적용하기 위한 필터
    ```xml
    <filter>
        <filter-name>springSecurityFilterChain</filter-name>
        <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>springSecurityFilterChain</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ```
- 파일 업로드를 위한 추가 부분: 스프링 시큐리티
    ```xml
    <multipart-config>			
        <max-file-size>10240000</max-file-size> <!--10MB -->
        <max-request-size>40960000</max-request-size><!-- 40MB -->
        <file-size-threshold>20480000</file-size-threshold> <!-- 20MB -->
    </multipart-config>	
    ```
***
## Security 설정 - security 설정 파일( .xml )
> ### 시큐리티 설정파일 관련 태그/속성
- ```intercept-url``` : 인증이 필요한 페이지의 요청명을 설정한다.
  - ```pattern``` : URL 패턴을 이용해서 경로 및 파일명을 설정한다.
  - ```access``` : 접근 권한을 결정한다.
    - ```permitAll``` : 권한 없이 누구나 접근 가능함
    - ```hasRole( 권한명 )``` : 특정 권한을 획득했을 때만 접근 가능함
    - ```hasAnyRole( 권한명1, 권한명2, ... )``` : 여러 권한 중 하나만 획득하면 접근 가능함
        
  - ※ ```intercept-url```을 설정할 떄는 반드시 디테일한 권한( 작은 권한 )을 먼저 지정한 후 아래로 가면서 전체에 대한 권한( 큰 권한 )을 부여해야 한다.

- ```form-login``` : 로그인 페이지를 커스터 마이징 하기 위한 설정
  - ```login-page``` : 로그인 페이지의 요청명
  - ```default-target-url``` : 로그인 성공 시 이동할 페이지의 요청명
  - ```authentication-failure-url``` : 로그인 실패 시 이동할 페이지의 요청명( 포워드 )
  - ```login-processing-url```
    - action은 ```/login```이 default이므로 만약 해당 요청명을 변경하고 싶다면 사용한다.
  - ```username-parameter``` : 로그인 시 사용할 아이디 입력상자의 name 속성
  - ```password-parameter``` : 패스워드의 name 속성

- ```logout```
  - ```logout-url``` : 로그아웃에 사용할 요청명
  - ```logout-success-url``` : 로그아웃에 성공했을 때 이동할 요청명
    
access-denied-handler
    error-page
        로그인 했으나 권한이 부족하여 접근할 수 없는 페이지로 이동했을 때
        보여지는 페이지의 요청명
> ### 시큐리티 1단계: 기본 페이지 사용하기
```xml
<!-- 
    스프링 설정파일을 생성한 후 security 네임 스페이스를 추가한다.
        xmlns:security = ""
-->
<security:http>
    <!-- 
        로그인을 통해 'USER' 권한을 획득해야 접근할 수 있는 페이지 경로를 지정한다.
        만약 해당 권한이 없다면 해당 경로에 접근하기 전에 인터셉트 당한다.
    -->
    <security:intercept-url pattern="/security1-1/**" access="hasRole('USER')"/>
    
    <!-- 권한이 없어도 누구나 접근 가능한 경로를 설정( 기본값으로 반드시 설정해주자. ) -->
    <security:intercept-url pattern="/**" access="permitAll"/>
    
    <!-- 로그인 폼과 로그아웃을 기본 설정 그대로 사용한다. -->
    <security:form-login/>
    <security:logout/>
</security:http>

<!-- 인증( 권한 설정 )을 위한 엘리먼트 -->
<security:authentication-manager>
    <security:authentication-provider>
        <security:user-service>
            <!-- 
                USER 권한을 획득하기 위한 아이디/패스워드 설정
                authorities( 권한 )에 "ROLE_" 접두어는 반드시 있어야한다.
            -->
            <security:user name="kosmo" password="1234" authorities="ROLE_USER"/>
        </security:user-service>
    </security:authentication-provider>
</security:authentication-manager>
```
> ### 시큐리티 2단계: 로그인 화면 커스터마이징
```xml
<security:http>
    <security:intercept-url pattern="/security2/login.do" access="permitAll"/>
    <security:intercept-url pattern="/security2/admin/**" access="hasRole('ADMIN')"/>
    <security:intercept-url pattern="/security2/**" access="hasAnyRole('USER', 'ADMIN')"/>
    <security:intercept-url pattern="/**" access="permitAll"/>
    <security:form-login
        login-page="/security2/login.do"
        default-target-url="/security2/index.do"
        authentication-failure-url="/security2/login.do?error"
        username-parameter="id"
        password-parameter="pass"/>
    <security:logout
        logout-url="/security2/logout"
        logout-success-url="/security2/index.do"/>
    <security:access-denied-handler
        error-page="/security2/accessDenied.do"/>
</security:http>

<!-- 인증( 권한 설정 )을 위한 엘리먼트 -->
<!--  ※ 권한 부여를 위한 권한명을 지정할 때는 일반적으로 접두어 "ROLE_" 형태로 제작한다. -->	
<security:authentication-manager>
    <security:authentication-provider>
        <security:user-service>
            <security:user name="kosmo" password="1234" authorities="ROLE_USER"/>
            <security:user name="kosmo_admin" password="1234" authorities="ROLE_ADMIN"/>
        </security:user-service>
    </security:authentication-provider>
</security:authentication-manager>
```
> ### 시큐리티 3단계 : 로그인을 JDBC와 연동
```xml
<authentication-manager>
    <authentication-provider>
        <jdbc-user-service 
            data-source-ref="dataSource"
            role-prefix=""
            users-by-username-query=" SELECT 
                user_id, user_pw, enabled 
                FROM security_admin WHERE user_id=? "
            authorities-by-username-query=" SELECT 
                user_id, authority
                FROM security_admin WHERE user_id=? "/>
    </authentication-provider>
</authentication-manager>
```
- ```jdbc-user-service```
  - ```data-source-ref```
    - 데이터 베이스 연결을 나타내는 dataSource로 해당 빈은 ***반드시*** ```root-context.xml```에서 기술해야 한다.
  - ```role-prefix```
	- "ROLE_"과 같이 권한명 앞에 붙는 접두어를 지정한다. 만약 해당 항목을 "ROLE_"로 지정하면 테이블에서는 "USER"와 같이 입력해야 한다.
  - ```users-by-username-query```
	- 아이디를 이용하여 사용자 정보를 가져오는 쿼리문
  - ```authorities-by-username-query```
	- 아이디를 이용하여 권한 정보를 가져오는 쿼리문

***
## Security 사용
> ### 컨트롤러
```java
@GetMapping("/security1-1/index.do")
public String security1_1() {
    return "09Security/Step1/index";
}
```
- 인증이 필요하다면 security 설정파일에서 설정해준 ```intercept-url```( ```/security1-1/**``` )을 요청명에 포함시켜준다.
> ### 뷰
```xml
<!-- taglib을 설정해준다. -->
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>

<!-- 시큐리티에서 제공하는 taglib으로 <form> 태그를 구성한다. -->
<!-- 
`	taglib의 form태그를 사용해야하는 이유? 
        로그인 페이지의 페이지 소스를 확인해보면 알 수 있다.
        '_csrf'의 이름으로 hidden value가 전송됨을 확인할 수 있다. 
-->
<form:form method="POST" action="../logout">
    <input type="submit" value="로그아웃"/>
</form:form>
```
- 페이지 소스
```xml
<input name="_csrf" type="hidden" value="2927971c-8a43-4063-9aaa-f9749c273d5a" />
```

***
## 부록
> ### 시큐리티 설정파일을 좀 더 간결하게 사용하는 법
1. 기존 xmlns:security를 xmlns로 변경
2. 기존 xmlns를 xmlns:beans로 변경
3. 기존 beans 태그를 beans:beans로 변경
4. 이러면 security:xxx 태그를 xxx만으로 사용할 수 있다.
```xml
<beans:beans xmlns="http://www.springframework.org/schema/security"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security-4.2.xsd">

	<http>
		<intercept-url pattern="/security2/login.do" access="permitAll"/>
		<intercept-url pattern="/security2/admin/**" access="hasRole('ADMIN')"/>
		<intercept-url pattern="/security2/**" access="hasAnyRole('USER', 'ADMIN')"/>
		<intercept-url pattern="/**" access="permitAll"/>
		<form-login
			login-page="/security2/login.do"
			default-target-url="/security2/index.do"
			authentication-failure-url="/security2/login.do?error"
			login-processing-url="/loginAction"
			username-parameter="id"
			password-parameter="pass"/>
		<logout
			logout-url="/security2/logout"
			logout-success-url="/security2/index.do"/>
		<access-denied-handler
			error-page="/security2/accessDenied.do"/>
	</http>

	<authentication-manager>
		<authentication-provider>
			<jdbc-user-service 
				data-source-ref="dataSource"
				role-prefix=""
				users-by-username-query=" SELECT 
					user_id, user_pw, enabled 
					FROM security_admin WHERE user_id=? "
				authorities-by-username-query=" SELECT 
					user_id, authority
					FROM security_admin WHERE user_id=? "/>
		</authentication-provider>
	</authentication-manager>
</beans:beans>
```