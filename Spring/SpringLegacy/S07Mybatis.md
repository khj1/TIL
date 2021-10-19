# Spring - Mybatis
객체 지향 언어인 자바의 관계형 데이터베이스 프로그래밍을 좀 더 쉽게 할 수 있도록 도와주는 개발 프레임 워크이다. ```Mybatis```는 ```SQL Mapper```를 이용하여 자바의 객체를 SQL 문과 연결하여 빠르고 편리하게 데이터 베이스에 접근할 수 있게 해준다.

## Mybatis 설정
> ### 의존 설정
```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.2.8</version>
</dependency>      
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.2.2</version>
</dependency>
```
> ### 빈 생성
```xml
<!-- DB연결 정보를 가진 데이터 소스 빈 -->
<beans:bean name="dataSource" 
    class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <beans:property name="driverClassName" value="oracle.jdbc.OracleDriver" />
    <beans:property name="url" value="jdbc:oracle:thin:@localhost:1521:xe" />
    <beans:property name="username" value="계정명" />
    <beans:property name="password" value="비밀번호" />
</beans:bean>

<!-- Mybatis 사용을 위한 빈 생성 -->
<beans:bean name="sqlSessionFactory" 
    class="org.mybatis.spring.SqlSessionFactoryBean">
    <beans:property name="dataSource" ref="dataSource" />
    <beans:property name="mapperLocations" value="classpath:mybatis/mapper/*.xml" />
</beans:bean>	
<beans:bean name="sqlSession" 
    class="org.mybatis.spring.SqlSessionTemplate">
    <beans:constructor-arg index="0" ref="sqlSessionFactory" />
</beans:bean>
```
***
## Mybatis 사용
> ### 인터페이스
```java
/*
해당 인터페이스는 컨트롤러와 DAO 사이에서 
매개역할을 하는 서비스 객체로 사용된다.
 */
public interface MybatisDAOImpl {
	// 추상 메소드 정의
	public int getTotalCount();

    // 매개변수명은 중요하지 않다.
	public ArrayList<MyBoardDTO> listPage(int s, int e);
}
```
> ### mapper
```xml
<mapper namespace="mybatis.MybatisDAOImpl">
	<select id="getTotalCount" resultType="int">
		SELECT COUNT(*) FROM myboard	
	</select>

	<select id="listPage" resultType="mybatis.MyBoardDTO">
		SELECT * FROM (
			SELECT Tb.*, rownum rNum FROM (
				SELECT * FROM myboard 
                <!-- Mapper에서 사용하는 if문은 JSTL과 동일하다. -->
				<if test="searchTxt!=null and !searchTxt.equals('')">
					WHERE 
					<foreach collection="searchTxt" item="str" open="(" close=")" separator="or">
						${searchField} like '%'||#{str}||'%'
					</foreach>
				</if>
				ORDER BY idx DESC
			) Tb
		)
		WHERE rNum<![CDATA[>=]]>#{start} AND rNum<![CDATA[<=]]>#{end}
	</select>
</mapper>
```
- Mapper의 속성
  - ```namespace```
    - 해당 ```Mapper``` 파일을 호출하기 위해서 우선 인터페이스를 정의한다.
    - ```namespace``` 속성에 인터페이스의 풀 경로를 기술한다.( 패키지 포함 )
    - 해당 인터페이스는 컨트롤러와 ```Mapper( DAO )``` 사이에서 중재 역할을 하는 서비스 객체로 사용된다.
  - ```id```
    - 실제 호출되는 함수명과 동일한 이름으로 기술한다.
  - ```resultType```
    - 쿼리 실행 후 반환되는 값의 타입을 명시한다.
    - 이때 패키지명을 포함한 풀 경로를 기술해야 한다.
  - ```parameterType```
    - 쿼리문에서 사용할 파라미터를 저장한 DTO 혹은 VO 객체의 타입을 명시한다.
    - 패키지명을 포함한 풀 경로를 기술한다.
- Mapper의 엘리먼트별 반환값
  - ```select```
    - ```select```문에 해당하는 결과를 반환한다.
    - ```int```인 경우 정수 값, DTO객체인 경우 해당 레코드를 저장한 List 계열의 컬렉션을 반환한다.
  - ```insert```
    - 입력 성공시 1, 실패시 0을 반환한다.
  - ```update```, ```delete```
    - 수정, 삭제에 성공한 행의 갯수가 반환된다.
- Mapper에서 \<foreach> 태크를 통해 동적 쿼리를 만들 수 있다.
  - ```collection```: 반복에 사용할 컬렉션 객체
  - ```item```: 반복 시 사용할 변수
  - ```open```: 동적 쿼리문의 시작 부분
  - ```close```: 동적 쿼리문의 종료 부분
  - ```seperator```: 반복할 때 사용할 구분자로 ```and```, ```or```, ```콤마``` 등을 사용할 수 있다.
- CDATA( Unparsed Character Data )
  - 파싱되지 않는 문자데이터를 의미한다.
  - Mapper 안에서 기본적으로 비교 연산자를 사용할 수 없다.
  - Mapper에서는 꺽쇄로 시작되는 모든 문자를 태그 엘리먼트로 인식하기 때문이다.
  - 쿼리문 사이에 비교 연산자를 사용할 때는 반드시 CDATA를 사용해서 처리한다.
- ```${}```와 ```#{}```의 차이
  - ```${}```
    - ```Statement```를 의미한다.
    - 자주 바뀌지 않거나, 사용자의 입력을 받는 경우가 아닐 때 사용하면 좋다.
    - 사용자로부터 입력을 받는 경우, 테이블명이나 컬럼명을 입력받을 때 사용하면 좋다.
    - ```${}```는 문자열에 자동으로 ''쿼터를 감싸주지 않는다.
  - ```#{}```
    - ```PreparedStatement```를 의미한다.
    - 오라클로 전달될 때 ?로 넘어간다.
    - 사용자의 입력을 받는 경우 또는 데이터가 많은 경우 사용한다.
    - ```#{}```는 문자열에 자동으로 ``쿼터를 감싸준다.( 숫자 데이터 X )
- Mapper에서 파라미터 사용하는 법
  - ```param``` 인덱스를 사용한다.
    - 예) ```#{param1}```
  - 인덱스를 사용한다.
    - 예) ```#{0}```
  - ```param``` 어노테이션에 지칭된 변수명을 사용한다.
    - 예) ```#{변수명}```
  - 전달받은 DTO 혹은 VO 객체의 멤버 변수명을 사용한다.
    - 예) ```#{멤버변수명}```
> ### Controller
```java
/* Mybatis를 사용하기위해 빈을 자동 주입 받는다. */
@Autowired
private SqlSession sqlSession;

/* @RequestMapping된 메소드 내에서 인터페이스에 접근 */
int totalRecordCount = 
                sqlSession.getMapper(MybatisDAOImpl.class).getTotalCount();
ArrayList<MyBoardDTO> lists =
				sqlSession.getMapper(MybatisDAOImpl.class).listPage(start, end);
```
1. 컨트롤러에서 ```mybatis```인터페이스에 접근한다.
   - ```MybatisDAOImpl```( ```mybatis``` 인터페이스) 는 ```service``` 객체의 역할을 한다.
2. 인터페이스에 정의된 추상 메소드를 호출한다.
3. ```mapper```에 정의된 쿼리문이 실행되는 형식으로 동작한다.
   - ```sqlSession.getMapper(mybatis 인터페이스.class)``` 메소드를 활용한다.

***
## 부록
> ### Model 객체와 Redirect
- Spring MVC에서는 ```model```에 데이터를 담고 ```redirect```하는 경우, ```Primitve Type```( 기본 자료형 )에 한해서 자동으로 URL 쿼리 파라미터에 추가해서 데이터를 넘겨준다.

- SpringBoot의 경우 이러한 기능이 off되어있다. 이럴땐 ```RedirectAttributes``` 매개변수를 핸들러에 추가하여 ```Redirect```시 원하는 데이터만을 선택적으로 전달할 수 있다.
  - 예) ```redirectAttribute.addAttribute("key", value);```

> ### 컨트롤러에 매핑된 메소드의 매개변수들은 어떻게 호출되는 것일까?
- 메서드의 매개변수로 입력해주면 스프링 컨테이너가 스프링 기본객체에 한해서 자동으로 객체를 생성해준다.