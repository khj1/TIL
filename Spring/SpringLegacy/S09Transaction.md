# Spring - Transaction
## Transaction 이란?
여러 작업을 묶어서 한번에 처리해주는 프로세스를 의미한다. 하나의 작업이라도 잘못되면 전체 프로세스가 ```rollback```된다. 주문 결제 처리를 예로 들면 주문 처리 작업이 정상적으로 수행되지 않았는데 결제는 정상적으로 처리된다면 심각한 문제가 된다. 이를 방지하기위해 ```Transaction```을 활용해서 혹시모를 불상사를 예방해준다.
***
## Transaction 설정
> ### servlet-context.xml( 빈 생성 )
- **주의!** 트랜잭션 매니저와 트랜잭션 템플릿 방식은 동시에 사용할 수 없다.
```xml
<!-- DB 연결 정보 -->
<beans:bean name="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <beans:property name="driverClassName"
        value="oracle.jdbc.OracleDriver" />
    <beans:property name="url" 
        value="jdbc:oracle:thin:@localhost:1521:xe" />
    <beans:property name="username" value="계정명" />
    <beans:property name="password" value="비밀번호" />	
</beans:bean>
<!-- JDBCTemplate 타입의 빈 생성( DB 연결 정보 참조 )-->
<beans:bean name="template" class="org.springframework.jdbc.core.JdbcTemplate">
    <beans:property name="dataSource" ref="dataSource" />
</beans:bean>

<!-- 트랜잭션 매니져 빈 생성( DB 연결 정보 참조 ) -->
<beans:bean name="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <beans:property name="dataSource" ref="dataSource" />
</beans:bean>

<!-- 트랜잭션 템플릿 빈 생성( 트랜젝션 매니져 참조 ) -->
<!-- 'spring-tx-4.1.4.RELEASE.jar'에서 참조함 -->
<beans:bean name="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
    <beans:property name="transactionManager" ref="transactionManager" />
</beans:bean>

<!-- 
    위에서 생성한 template, transactionManager 빈을 참조해서
    최종적으로 transactionDAO 빈을 생성한다. 우리는 해당 객체를 통해
    트랜잭션 처리를 하게된다.

    아래와 같이 설계하면 DAO 클래스 내에서 setter를 적절히 생성해줘야 한다.
-->
<!-- 트랜잭션 매니저를 사용할 떄 -->
<beans:bean name="transactionDAO" class="transaction.TicketDAO">
    <beans:property name="template" ref="template" /> 
    <beans:property name="transactionManager" ref="transactionManager" />
</beans:bean>
<!-- 트랜잭션 템플릿을 사용할 떄 -->
<beans:bean name="transactionTplDAO" class="transaction.TicketTplDAO">
    <beans:property name="template" ref="template" />
    <beans:property name="transactionTemplate" ref="transactionTemplate" />
</beans:bean>
```

***
## Transaction Manager
> ### Model
```java
/*
멤버 변수 선언
    Spring-JDBC를 사용하기 위한 멤버변수와 setter() 정의
    트랜젝션 처리를 위한 매니저클래스의 멤버변수와 setter() 정의

    해당 예제에서는 bean으로 DAO를 생성하므로 setter를 만들어준 것이다.
*/
JdbcTemplate template;
PlatformTransactionManager transactionManager;

// setter
public void setTemplate(JdbcTemplate template) {
    this.template = template;
}
public void setTransactionManager(PlatformTransactionManager transactionManager) {
    this.transactionManager = transactionManager;
}
```
```java
TransactionDefinition def = new DefaultTransactionDefinition();
TransactionStatus status = transactionManager.getTransaction(def);
```
```java
// 한 프로세스 안에서 여러가지의 업무를 처리해준다.
// 만약 하나라도 문제가 생기면 모든 업무가 rollback 처리된다.
try {
    template.update(new PreparedStatementCreator() {
        
        @Override
        public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
            String query = "";
            PreparedStatement psmt = con.prepareStatement(query);
            return psmt;
        }
    });
    
    template.update(new PreparedStatementCreator() {
        
        @Override
        public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
            String query = "";
            PreparedStatement psmt = con.prepareStatement(query);
            return psmt;
        }
    });
    System.out.println("정상처리 되었습니다.");
    transactionManager.commit(status);
}
catch (Exception e) {
    System.out.println("제약 조건을 위배하여 모두 취소되었습니다.");
    transactionManager.rollback(status);
}
```
***
## Transaction Template
> ### Model
```java
// 멤버 변수
JdbcTemplate template;
TransactionTemplate transactionTemplate;

// setter
public void setTemplate(JdbcTemplate template) {
    this.template = template;
}
public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
    this.transactionTemplate = transactionTemplate;
}
```
```java
try {
    transactionTemplate.execute(new TransactionCallbackWithoutResult() {
        
        // 익명 클래스 내부에서 오버라이딩
        @Override
        protected void doInTransactionWithoutResult(TransactionStatus status) {
            template.update(new PreparedStatementCreator() {
                
                @Override
                public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
                    String query = "";
                    PreparedStatement psmt = con.prepareStatement(query);
                    return psmt;
                }
            });
            
            template.update(new PreparedStatementCreator() {
                
                @Override
                public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
                    String query = "";
                    PreparedStatement psmt = con.prepareStatement(query);
                    return psmt;
                }
            });
        }
    });
    // 템플릿을 사용하는 경우 별도의 commit() 혹은 rollback()이 필요없다.
    System.out.println("모두 정상처리 되었습니다.");
    return true;
}
catch (Exception e) {
    System.out.println("제약 조건을 위배하여모두 취소되었습니다.");
    return false;
}
```