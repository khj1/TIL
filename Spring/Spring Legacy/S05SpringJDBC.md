# Spring - JDBC
## JdbcTemplate이란?
JDBC API를 이용하려면 DB 연동에 필요한 ```Connection``` 객체와 쿼리 실행을 위한 ```PreparedStatement``` 객체를 생성해야한다. 그리고 쿼리 실행 후에는 ```finally``` 블록에서 ```ResultSet```, ```PreparedStatement```, ```Connection```타입 객체들을 ```close()``` 메서드로 닫아줘야한다. 이 코드들은 DB연동할 때 데이터 처리와는 관계 없는 코드지만 JDBC 프로그래밍을 할 때 구조적으로 반복된다. 이 구조를 사용할 때 핵심 코드는 반도 되지 않는다. 이런 구조적인 반복을 줄이기 위한 방법은 템플릿 메서드 패턴과 전략 패턴을 함께 사용하는 것이다. 스프링은 이 두 패턴을 엮은 JdbcTemplate 클래스를 제공한다.
- [출처](https://jaehoney.tistory.com/34)

출처: https://jaehoney.tistory.com/34 [A work-loving developer]
## 스프링에서 JDBC 연동을 위한 기본 설정
> pom.xml - 상단
```xml
<repositories>
    <repository>
        <id>oracle</id>
        <name>ORACLE JDBC Repository</name>
        <url>https://code.lds.org/nexus/content/groups/main-repo</url>
    </repository>
</repositories>	
```
- Oracle 드라이버의 경우 메이븐 저장소에서 다운로드 할 수 없으므로 다른 저장소의 위치를 설정한다. 
- 만약 자동으로 다운로드 되지 않는다면 수동으로 설치해야 한다.
> pom.xml - ```<dependencies>``` 의 마지막 부분
```xml
<!-- 오라클 드라이버를 사용하기 위한 의존 설정 -->
<dependency>  
    <groupId>com.oracle</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>11.2.0.3</version>
</dependency>
    
    <!-- JDBCTemplate을 사용하기 위한 의존 설정 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.1.4.RELEASE</version>
</dependency>
```

> servlet-context.xml - 기본 패키지 추가
```xml
<!-- 요청이 들어왔을 때 기본 패키지로 지정된 곳에서 매핑 정보를 찾는다. -->
<context:component-scan base-package="springboard.controller" />
```

> servlet-context.xml -  SPRING JDBC를 사용하기 위한 빈 생성
```xml
<!-- 오라클 데이터 베이스의 연결정보를 담고있는 dataSource 빈 생성 -->
<beans:bean name="dataSource" 
    class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <beans:property name="driverClassName" value="oracle.jdbc.OracleDriver" />
    <beans:property name="url" value="jdbc:oracle:thin:@localhost:1521:xe" />
    <beans:property name="username" value="DB 계정" />
    <beans:property name="password" value="DB 비밀번호" />
</beans:bean>

<!-- dataSource를 기반으로 JdbcTemplate 타입의 빈 생성 -->
<!-- 
    해당 빈은 스프링 컨테이너가 시작될 때 미리 만들어지고
    차후 컨트롤러나 DAO에서 주입(Injection)받아 사용한다.
-->
<beans:bean name="template" 
    class="org.springframework.jdbc.core.JdbcTemplate">
    <beans:property name="dataSource" ref="dataSource" />
</beans:bean>
```
***

## JDBC 연결
> ### 컨트롤러
- 방법 1 - 멤버변수
```java
@Autowired
private JdbcTemplate template;
// JdbcTemplate을 해당 프로그램 전체에서 사용하기 위한 설정 (static 타입)
JdbcTemplateConst.template = this.template;
```
- 방법 2 - ```setter()```
```java
private JdbcTemplate template;

@Autowired
public void setTemplate(JdbcTemplate template) {
    this.template = template;
    JdbcTemplateConst.template = this.template;
}
```
> ### ```@Autowired``` 란?
스프링 설정파일에서 생성된 빈을 자동으로 주입받고 싶을 때 사용한다. Type을 기반으로 자동 주입된다. 만약 해당 타입의 빈이 존재하지 않으면 에러가 발생되고 서버가 시작되지 않는다.
- 생성자, 멤버변수, 메소드(```setter```)에 적용 가능하다.
- 타입을 이용해 자동으로 프로퍼티 값을 설정한다.
- 해당 어노테이션은 함수 내의 지역 변수에 사용할 수 없다.
- 타입을 통해 자동으로 설정되므로 같은 타입으로 생성된 빈 객체가 2개이상 존재하면 예외가 발생한다.
- [참고자료](https://devlog-wjdrbs96.tistory.com/166)
***
## JdbcTemplateDAO
> 생성자
```java
public JDBCTemplateDAO() {
    this.template = JdbcTemplateConst.template;
}
```
- 생성자를 통해 DB와 연결해준다.
  - 컨트롤러에서 @Autowired를 통해 자동 주입받았던 빈을 정적 변수인 ```JdbcTemplateConst.template```에 할당했다. 
  - 이를 통해 DB 연결 정보를 DAO에서 바로 사용할 수 있다.

> ### 자원해제
- ```JdbcTemplate``` 에서는 자원해제를 하지 않는다.
- Spring 설정 파일에서 빈을 생성하므로 자원을 해제하면 다시 new를 통해 생성해야하는 문제가 발생한다.

> ### ```JdbcTemplate``` 관련 주요 메소드
- ```Object queryForObject(String sql, RowMapper rm)```
  - 하나의 레코드나 결과값을 반환하는 select 계열의 쿼리문을 실행할 때 사용한다.
  - 단, 반환 결과가 0이거나 2 이상이면 예외를 발생시키므로 반드시 예외처리를 하는 것이 좋다.
- ```Object queryForObject(String sql, Object[] args, Rowmapper rm)```
  - 인파라미터가 있고, 하나의 레코드를 반환하는 select 계열의 쿼리문 실행에 사용한다.
- ```List query(String sql, RowMapper rm)```
  - 여러개의 레코드를 반환하는 select 계열의 쿼리문인 경우 사용한다.
- ```List query(String sql, Object[] args, RowMapper rm)```
  - 인파라미터를 가진 여러개의 레코드를 반환하는 select 계열의 쿼리문인 경우 사용한다.
- ```int update(String sql)```
	- 인파라미터가 없는 update/insert/delete 쿼리문을 처리할 때 사용한다.
- ```int update(String sql, Object[] args)```
  - 인파라미터가 있는 update/insert/delete 쿼리문을 처리할 때 사용한다.

> ### update 메소드
- ```update()``` 메소드를 사용할 때는 **내부 익명 클래스**를 활용한다.
- **```update()``` 메소드를 사용하는 2가지 방법.**
  1. ```update(new PreparedStatementCreator(){ 익명 클래스 });```
    - 익명 클래스 내부에서 ```PreparedStatement createPreparedStatement(Connection con)``` 메소드를 오버라이딩한다.
    - 오버라이딩 메소드 내부에서 쿼리문을 작성해준다.
    - 익명 클래스 내부에서 ```PreparedStatement``` 객체를 생성하고 해당 객체를 반환해준다.
  2. ```update(쿼리문, new PreparedStatementSetter(){ 익명 클래스 });```
    - 익명 클래스 내부에서 ```public void setValues(PreparedStatement ps)``` 메소드를 오버라이딩 한다.
    - 익명 클래스 외부에서 쿼리문을 작성해준다.



> ### ```RowMapper``` 란?
- ```RowMapper``` 인터페이스의 ```mapRow()``` 메소드
  ```java
  @Nullable
  T mapRow(ResultSet rs, int rowNum) throws SQLException;
  ```
  - 쿼리문 실행 결과를 ```ResultSet``` 객체로 받아온다.
  - 두번째 인자로 받아온 행의 결과물을 자바 객체로 반환한다.


