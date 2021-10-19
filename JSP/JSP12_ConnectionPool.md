# DataBase Connection Pool (DBCP)
## 커넥션 풀이란?
- 웹 환경에서처럼 DB와의 연결이 빈번하게 발생하는 경우에 매번 커넥션을 생성하고 닫게되면 리소스가 낭비된다.
- 이를 개선하기 위해 만들어 진것이 **커넥션 풀** 
  - 먼저 미리 커넥션을 생성해 Pool에 넣어 놓는다.
  - 요청이 들어오면 커넥션을 Pool에서 가져다 쓴다.
  - 다 쓰고나면 다시 Pool에 반납한다.
- Tomcat이 시작되면 **JNDI**를 이용해서 server.xml과 context.xml에 설정한 대로 커넥션 풀을 생성한다.
- 최종적으로 데이터의 근원지, 즉 데이터소스(DataSource)를 얻어와(lookup) 커넥션 객체를 얻어오게 되고, 사용자는 해당 객체를 사용하게된다.

## JNDI (Java Naming Directory Interface)
네이밍 서비스(DNS)처럼 분산된 객체를 찾기위해 특정한 이름으로 객체를 등록해서 관리할 수 있게 도와주는 서비스다. 즉 네이밍 서버에 여러 객체를 등록할 때 이름으로 매핑하여 등록하고 다른 프로그램에서 그 객체를 찾을 떄는 해당 이름으로 찾아서 (lookup) 사용한다.
- 명명 및 디렉토리 서비스에 접근하기 위한 [API](https://www.redhat.com/ko/topics/api/what-are-application-programming-interfaces)(Application Programming Interface)
- 개발자는 개체를 등록(bind, rebind)하고 찾아쓰는(lookup)방법만 알면 된다.
- 서버에 JNDI Driver(통칭 service provider)만 있으면 명명/디렉토리 서비스를 이용할 수 있다.
- JNDI 드라이버는 우리가 호출한 이름을 Naming 서비스에서 이해할 수 있는 것으로 바꿔주는 역할을 한다.
- JNDI의 구조는 윈도우 탐색기처럼 트리 구조를 가지고 있다. JNDI 트리에 들어갈 떄는 우선 InitialContext(javax.naming 패키지)에서 시작한다. 
- InitialContext에서 시작하지만 WAS(Web Application Server) 서버마다 자기만의 JNDI 컨텍스트가 있다. 해당 JNDI 컨텍스트에서 찾고자 하는 검색 작업은 java.comp/env (Tomcat의 경우)라는 서브 컨텍스트에서 시작된다.
- 즉 JNDI의 트리구조에서
  - InitialContext
  - java:comp/env
  - server.xml에 등록한 JNDI명(jdbc/myoracle)
  - getConnection() 으로 Connection객체생성

### Tomcat 버전 / conf / server.xml의 <GlobalNamingResources> 태그사이에 세팅
```xml
<Resource auth="Container"
    //JDBC 드라이버의 클래스명
   	 driverClassName="oracle.jdbc.OracleDriver" 
    
    //데이터 소스로 사용할 클래스
   	 type="javax.sql.DataSource"
    
    //풀의 최초 초기화 과정에서 미리 만들어 놓을 연결 개수(default 0)
   	 initialSize="0" 

    //풀에 반납할 때 최대로 유지될 수 있는 연결 개수(default 8)
     maxIdle="20" 
   	
    //동시에 사용할 수 있는 최대 연결 개수(default 8)
     maxTotal="20" 

    //새로운 요청이 들어왔을 때 얼만큼 대기할 지 1/1000초 단위로 기술
     maxWaitMillis="5000" 
     
    //최소한으로 유지될 연결 개수
     minIdle="5"
   	 url="jdbc:oracle:thin:@localhost:1521:xe"

    //생성할 자원(Pool)의 이름
   	 name="dbcp_myoracle"  
   	 username="아이디" password="패스워드" />
```

### Tomcat버전/conf/context.xml의 <Context reloadable="true"> 태그 사이에 아래부분 세팅
```xml
<ResourceLink global="dbcp_myoracle"   
       name="dbcp_myoracle" 
       type="javax.sql.DataSource"/>
```

### 자바코드에서 아래와 같이 코딩
```java
Context initctx = new InitialContext(); 
//톰캣 디렉토리를 찾는다 (java:comp/env)
Context ctx = (Context)initctx.lookup("java:comp/env"); 
//데이터 소스는 DB에 연결된 정보를 담고있다.
DataSource source = (DataSource)ctx.lookup("dbcp_myoracle"); 
//톰캣이 커넥션풀에 생성한 커넥션 전체를 가져다가 사용한다.
Connection conn = source.getConnection();

//혹은
Context initctx = new InitialContext();
DataSource ds = (DataSource)initContext.lookup("java://comp/env/jdbc/myoracle");
Connection con = ds.getConnection();
```