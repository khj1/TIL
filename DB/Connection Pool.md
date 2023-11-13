# 커넥션 풀

## 데이터베이스 연결의 생애주기
1. DB 드라이버를 사용하여 데이터베이스 연결을 연다.
2. 데이터를 읽고 쓰기 위해 TCP 소켓을 연다.
3. TCP 소켓을 사용하여 데이터 통신을 한다.
4. 데이터베이스 연결을 닫는다.
5. TCP 소켓을 닫는다.

이처럼 데이터베이스 연결을 수립하고 해제하는 과정은 비용이 많이 들어가는 작업이다.

## 커넥션 풀은 왜 사용하는가?
### DB와 네트워크의 연결 시간을 단축한다.
- 사용자로부터 요청이 들어올 때마다 DB 연결을 수립하고 해제하는 과정을 매번 진행하는 것은 굉장히 비효율적이다.
- 커넥션 풀 안의 커넥션들은 항상 DB와의 연결을 열린 상태로 유지한다.
- 이로서 데이터베이스 연결을 열고, 다는 비용을 절약할 수 있다.
- 응답 시간 단축은 처리량 증가로 이어진다.
  
### DB에 대한 커넥션 갯수를 일정 수준으로 제한할 수 있다.
- DB 포화를 방지한다.
- 일관된 DB 성능을 유지할 수 있다.

## Hikari CP
Hikari CP는 스프링부트에 기본적으로 내장되어 있는 JDBC 데이터베이스 커넥션 풀링 프레임워크다. Hikari CP의 성능이 타 커넥션 풀에 비해 압도적으로 우수하기 때문이다.

> 스프링 부트는 Hikari CP를 사용할 수 없는 경우 Tomcat DBCP, Apache DBCP,Oracle UCP 순으로 커넥션 풀을 선택한다.

### Hikari CP의 성능이 왜 더 좋을까?
#### 하나의 쿼리가 실행되는 과정 예시
```java
Connection connection = null;
PreparedStatement preparedStatement = null

try {
    connection = hikariDataSource.getConnection();
    preparedStatement = connection.preparedStatement(sql);
    preparedStatement.executeQuery();
} catch(Throwable e) {
    throw new RuntimeException(e);
} finally {
    if (preparedStatement != null) {
        preparedStatement.close();
    }
    if (connection != null) {
        connection.close(); // 여기서 connection pool에 반납됩니다.
    }
}
```

#### hikariDataSource.getConnection()의 내부 동작 시나리오
hikari CP는 내부적으로 `ConcurrentBag` 이라는 구조체를 이용해 Connection을 관리한다.

`ConcurrentBag.borrow()`라는 메서드를 통해 사용 가능한(idle) Connection을 반환한다.

```
[Thread-1님] 안녕하세요 Hikari님! Connection 하나만 주세요~
[Hikari님] 안녕하세요 Thread-1님! 저희 pool에서는 Connection을 주는 규칙이 있답니다. 확인해볼게요~
[Hikari님] 이전에 Thread-1님이 저희 pool에 방문한 내역 먼저 살펴볼게요!
[Hikari님] 오! 이전에 방문한 내역이 있으시네요~ 근데 그때 사용한 Connection은 다른 Thread 님이 사용 중(active) 이시네요~ 다른 Thread님이 안 쓰셨으면 빠르게 이 Connection 먼저 드렸을 텐데 아쉽네요ㅠㅠ
[Thread-1님] 괜찮아요! Hikari님 다른 Connection 주세요~
[Hikari님] Pool 전체에서 사용 가능한(idle) Connection이 있는지 찾아볼께요~
[Hikari님] (Loop 돌면서 찾는중…)
[Hikari님] Thread-1님 지금 전체 Connection이 다 사용중이에요! ㅠㅠ 저기 handoffQueue 앞에 가서 좀 기다리셔야 할 것 같아요 ㅜㅜ
[Thread-1님] 네 괜찮아요! (안 괜찮음) 저기서 30초만 기다려보고 없으면 Exception내면 되죠^^ (깨알 Tip. HikariCP default Connection timeout은 30초 입니다.)
[Hikari님] 네 죄송합니다 Thread-1님. 다음에 또 다시 방문해주세요~
[Thread-1님] (handoffQueue에서 다른 Thread가 쓰고 반납한 Connection을 얻었다!)
```

#### connection.close()의 내부 동작 시나리오
```
[Thread-1님] Transaction내의 쿼리를 모두 수행하고 Commit이 되었어 이제 Connection을 반납해볼까?
[Thread-1님] Thread-1님이 (ProxyConnection) connection.close()을 실행하였습니다.
[Hikari님] 안녕하세요 Thread-1님! Connection 이용은 만족스러우셨나요? Connection 반납 절차 도와드리겠습니다.
[Hikari님] 일단 idle Connection으로 바꿀게요 (poolEntry.setState(STATE_NOT_IN_USE))
[Hikari님] handOffQueue에서 Connection을 받길 원하는 다른 Thread님이 있나봐요~
[Hikari님] (handOffQueue에 Connection 삽입)
[Thread-2님] 나이스!! handOffQueue에 Connection 하나 떨어졌다~
[Hikari님] Thread-1님 고생하셨습니다~ 이번에 사용한 Connection 정보 등록해 드릴께요~! 다음에 빠르게 이용하실 수 있으실거에요
[Thread-1님] 감사합니다! Hikari님 또 Connection 받으러 올께요~
```

![image](https://github.com/khj1/TIL/assets/74335027/bebae35c-2427-4334-9ae4-931df1830177)

> hikari CP는 내부적으로 Connection 객체를 PoolEntry 라는 타입으로 한번 wrapping 해서 Connection을 관리한다.

> [우아한 형제들 - Hikari CP Dead lock에서 벗어나기(이론편)](https://techblog.woowahan.com/2664/)를 참고했습니다. <br />
> [Hikari CP Dead lock에서 벗어나기(실전편)](https://techblog.woowahan.com/2663/)도 함께 보면 좋습니다.

### Hikari CP Dead lock
DBCP를 사용할 때 교착상태를 항상 주의해야 한다. 교착상태란 쓰레드가 서로의 DB Connection이 반납되기만을 무한정 대기하는 상황이다.

#### Dead lock 시나리오
1. Thread-1이 insert query를 실행하기 위해 Transaction을 시작한다.
2. Root Transaction용 Connection을 하나 가져온다. DBCP의 사이즈는 1이다. (total = 1, active = 0, idle = 1, waiting = 0)
3. Repository.save를 하기위해 추가적으로 Connection이 하나 더 필요하다고 가정해보자.
4. Thread-1이 작업을 수행하기 위해 DBCP로부터 커넥션을 요청한다.
   1. Thread-1의 CP 방문 내역을 확인한다.
   2. 아직 방문 내역에 등록된 게 없다면 idle 상태의 Connection들을 스캔한다.
   3. DBCP 사이즈가 1이고 Connection이 이미 in use 상태임을 확인한다. (total = 1, active = 1, idle = 0, waiting = 0)
   4. handOffQueue에서 누군가 Conenction을 반납하길 기대하며 30초 동안 기다린다.
   5. 유일한 Connection을 이미 자신이 사용하고 있기 때문에 결국 Connection Timeout이 발생한다.
5. SQL TransactionConnectionException으로 인해 Sub Transaction이 Rollback 된다.
6. Sub Transaction Rollback으로 인해 Root Transaction이 rollbackOnly = true가 되며 Root Transaction이 Rollback 된다. (JpaTransactionManager를 사용한다고 가정한다.)
7. Rollback과 동시에 Root Transaction용 Connection이 다시 Pool에 반납된다. (total = 1, active = 0, idle = 1, waiting = 0) 

### Dead lock을 피하기 위한 DBCP 최소 사이즈 공식
```
WAS의 전체 쓰레드 개수 * (쓰레드가 작업하기 위해 동시에 필요한 DB Connection의 갯수 - 1) + 1
```
- 위 공식은 어디까지나 데드락이 발생하지 않을 최소 값이다. 따라서 최적화를 위해선 +a가 필요하다.
- 그렇다고 무제한으로 DB Connection의 갯수를 늘릴 수는 없다. 
- 커넥션도 일종의 객체이기 떄문에 메모리를 차지한다.
- 따라서 메모리와 커넥션의 갯수는 Trade-off 관계이다.
- 적절한 성능 테스트를 통해 최적의 a 값을 찾는 것이 중요하다.

## 대략적인 커넥션 풀 설정방법
### 커넥션 풀 설정 유의사항
- 올바르게 설정하지 않으면 오히려 성능 문제가 유발될 수 있다.
- 설정이 성능에 주는 영향을 반드시 알아야 한다.

### 최대 커넥션 갯수 (maximumPoolSize)
- 풀이 제공할 수 있는 최대 커넥션 갯수
  - in use + idle
- **계산에 필요한 항목**
  - 한 커넥션 당 쿼리 실행 시간, 최대 TPS(Transaction Per Second)
- **단순 계산 식 (커넥션 갯수를 추정해볼 수 있다)**
  - 최대 TPS = 1개의 커넥션의 초당 처리 요청 갯수 * 동시 커넥션 갯수
  - 동시 커넥션 갯수 = 최대 TPS / 1개의 커넥션의 초당 요청 처리 갯수
  - 동시 커넥션 갯수 = 최대 TPS / (1초 / 쿼리 실행 시간)

### 커넥션 대기 시간 (connectionTimeout)
- 풀에서 커넥션을 구하기 위해 대기하는 시간
- 풀의 모든 커넥션이 사용중일 때 대기가 발생한다.
- connectionTimeout의 기본 값은 30초
- 기본 값 대신 0.5 ~ 3초 이내로 설정하는 것을 권장한다.
  - 응답이 없는 것보다는 빨리 에러 화면을 띄워주는 것이 더 좋을 수 있다.

### 커넥션 최대 유지 시간 (maxLifetime)
- 커넥션의 최대 유지 시간
  - 커넥션을 생성한 이후 이 시간이 지나면 커넥션을 닫고 풀에서 제거한다.
  - 제거한 뒤 커넥션을 새로 생성한다.

> **왜 커넥션을 제거하고 새로 생성하는 걸까?** <br />

#### 기본 규칙
- 네트워크나 DB의 관련 설정 값 보다 작은 값을 사용해야 한다.
  - 예를들어 사용중인 네트워크 장비의 최대 TCP 커넥션 유지 시간

#### 커넥션 최대 유지 시간이 관련 설정 값보다 크다면
- 이미 유효하지 않은 커넥션이 풀에 남게된다.
- 풀에서 유효하지 않은 커넥션을 구하는 과정에서 커넥션을 새로 생성한다.
- 트래픽이 몰리는 시점일 경우 성능 저하가 유발된다.

### 커넥션 확인 주기 (keepaliveTime)
- 커넥션이 살아있는 지 확인하는 주기
  - 유휴 커넥션들을 대상으로 확인한다.
  - 유효하지 않은 커넥션은 풀에서 제거한다.
  - 제거한 뒤 커넥션을 새로 생성한다.

#### 기본 규칙
- 네트워크나 DB 관련 설정 값 보다 작은 값을 사용해야 한다.
  - 예를들어 DB의 미활동 커넥션 대기 시간

### 최소 유휴 커넥션 (minimumIdle)
- 풀에 유지할 최소 유휴 커넥션 갯수를 지정한다.
  - 설정하지 않으면 maximumPoolSize와 동일한 값을 사용하게 된다.
- 트래픽이 적은 시간대에 DB 자원 사용을 줄이기 위해 사용한다.
  
#### 기본 규칙
- Hikari 문서에서는 해당 옵션을 설정하지 않는 것을 권장한다.
  - 즉 maximumPoolSize와 동일한 크기로 설정할 것을 추천한다.
  - 이 값이 작으면 급격한 트래픽 증가 시 성능 저하를 일으킬 가능성이 높다.

## 용어 정리
- **idle connection**
  - 풀 안에서 사용 대기중인 커넥션
- **in use connection**
  - 사용 중인 커넥션