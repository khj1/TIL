# Lock
- 여러 사용자들이 존재하는 환경에서 동시성 제어를 위해 필요하다.
- Lock은 트랜잭션 격리 수준을 구현하기 위한 방법 중 하나이다.
- Lock의 종류와 전략은 벤더 사 마다 각기 다르다.
- MySQL 5.7 버전 이후부터는 InnoDB가 Default

## 낙관적 락
- 트랜잭션이 충돌하지 않을 것이라고 가정하고 사용하는 Lock 전략
- 트랜잭션이 커밋될 때 애플리케이션은 구분 컬럼을 통해 격리가 위반되었는지 체크한다.
- 만약 위반하였다면 해당 트랜잭션은 Rollback 한다.
- Application Lock 이라고도 불린다.
  - Application 내에서 버전을 통해 Lock을 구현할 수 있기 때문이다.
  - version 뿐만 아니라 timestamp나 hashcode를 사용할 수도 있다.
- 경쟁이 심하지 않은 상황에서는 낙관적 락이 비관적 락보다 성능이 좋다.
- 하지만 경쟁이 심하다면 Rollback 비용이 높아지기 떄문에 성능이 떨어진다.

## 비관적 락
- 트랜잭션이 매번 충돌한다고 가정하고 사용하는 Lock 전략
- 트랜잭션을 활용하여 충돌을 예방하는 것이 비관적 락이다.
- 각 트랜잭션이 실행되는 동안 전체 데이터베이스에 독점 잠금을 획득한다.
- Database Transaction Lock 이라고도 불린다.
- s Lock, x Lock 이 대표적인 비관적 락이다.
  - s Lock을 걸게 되면 쓰기 연산을 위해 x Lock을 얻어야 한다.
  - 다른 트랜잭션에서 s Lock을 걸어버렸다면 x Lock을 얻지 못해 데이터를 업데이트할 수 없다.
- Repeatable Read, Serializable 정도의 격리 수준을 제공한다.

## 언제 어떤 방식을 사용하는 것이 효과적일까?
- 낙관적 락은 트랜잭션을 필요로 하지 않는다. 따라서 비관적 락보다 성능이 더 좋다.
- 충돌이 많이 발생하지 않을 것 같다고 예상된다면 낙관적 락을 사용하는 것이 더 좋다.
- 하지만 낙관적 락은 트랜잭션을 사용하지 않기 때문에 롤백을 수동으로 구현해줘야 한다.

> 일반적인 웹 어플리케이션은 읽기 연산의 비중이 매우 높다. 따라서 낙관적 락을 주로 사용한다.

### JPA에서의 락
#### 낙관적 락 구현
- `@Version` 어노테이션을 통해 version 필드를 생성해주는 방식으로 구현할 수 있다.
- DB에서 해당 엔티티에 접근할 때마다 version 필드의 값도 같이 증가하게 된다.
- JPA의 낙관적 락은 최초의 요청만 commit한다.
- 따라서 동일한 데이터에 동시에 접근한 경우 최초의 요청을 제외한 나머지 요청은 실패한다. (조회한 버전과 현재 버전이 달라지기 때문에)
- 따라서 한번만 요청한다고 가정했을 때 그 결과가 매번 달라질 수 있다.

```java
@Entity
@OptimisticLocking(type = OptimisticLockType.VERSION)
public class Product {
  
  @Id
  private Long id;

  private String name;
  
  @Version
  private Long version;
}
```

#### 비관적 락 구현
- 조회 쿼리에 `@Lock(LockModeType.PESSIMISTIC_WRITE)` 어노테이션을 붙여 구현할 수 있다.
  - s Lock: PESSIMISTIC_READ
  - x Lock: PESSIMISTIC_WRITE
- 데이터 자체에 락이 걸려있기 때문에 다른 사용자는 락이 풀릴 때까지 데이터에 접근하지 못하고 대기한다.

## InnoDB Lock
### Exclusive Lock (베타 잠금)
- 쓰기 연산에 관련된 락이다.
- 데이터를 수정하는 동안 다른 트랜잭션에서 해당 데이터를 읽을 수 없게 만든다. (쓰기 연산도 불가능)
- SELECT ... FOR UPDATE, UPDATE, DELETE 등의 수정 쿼리를 날릴때 각 레코드에 걸리는 Lock
- X Lock이 걸려 있으면 다른 트랜잭션은 S Lock, X Lock 둘 다 걸 수 없다.

### Shared Lock (공유 잠금)
- 읽기 연산에 관련된 락이다.
- 데이터를 조회하는 동안 다른 트랜잭션에서 해당 데이터를 수정할 수 없게 만든다. (조회는 가능)
- 일반적인 SELECT 쿼리가 아닌 SELECT ... FOR SHARE를 사용해 락을 걸 수 있다.
- S Lock이 걸려있는 row에 다른 트랜잭션이 S Lock을 중첩해서 걸 수 있다.
  - X Lock은 걸 수 없다.

### Record Lock (레코드 잠금)
- row가 아닌 DB index record에 걸리는 Lock
- 테이블에 index가 없다면?
  - 숨겨져 있는 클러스터 인덱스를 사용하여 Record Lock을 건다.

### Gap Lock (갭 잠금)
- index record의 gap에 걸리는 Lock이다.
- gap이란 index record가 없는 부분을 말한다.
- 조건에 해당하는 새로운 row가 추가되는 것을 방지한다.

### Next Key Lock
- Next Key Lock은 Record Lock과 Gap Lock을 합친 것이다.
- 앞서 살펴본 Gap Lock은 단독으로 사용되기 보다는 Next Key Lock의 일부로 더 자주 사용된다.
- Next Key Lock은 바이너리 로그에 기록되는 쿼리가 레플리카 서버에서 실행될 때 소스 서버에서 만들어낸 결과와 동일한 결과를 만들어내도록 보장해주는 것이 주 목적이다.
- 하지만 의외로 Next Key Lock으로 인한 데드락이 발생할 수 있으므로, 바이너리 로그 포맷을 ROW 형태로 바꿔서 Next Key Lock을 줄이는 것이 좋다고 한다.

### Auto Increment Lock
- MySQL은 자동 증가하는 숫자값을 채번하기 위해 AUTO_INCREMENT라는 컬럼 속성을 제공한다.
- AUTO_INCREMENT 컬럼은 여러 레코드가 동시에 INSERT 되더라도 중복되지 않고 증가하는 일련번호를 제공하기 위해 내부적으로 테이블 수준의 잠금인 Auto Increment Lock을 제공한다.
- 해당 락은 INSERT, REPLACE와 같이 새로운 레코드를 저장하는 쿼리에서만 사용된다.
- Auto Increment Lock은 트랜잭션과 무관하다.
- 따라서 자동 증가 값을 얻어 내는데 성공했지만 이후 쿼리에 실패하여 트랜잭션이 롤백됐다고 하더라도 이미 늘어난 자동 증가 값이 복구되진 않는다.

### 분산 락
- 여러 서버에서 공유된 데이터를 제어하기 위해 사용한다.
- 분산 락 저장소로 Redis를 많이 사용하고 Zookeeper를 사용하여 구현할 수 있다.
- Java와 Redis를 사용한다면 Redisson을 사용하여 쉽게 분산락을 사용할 수 있다.

> 스핀락, Pub/Sub