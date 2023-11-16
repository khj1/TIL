# Transaction
- 애플리케이션에서 몇 개의 읽기와 쓰기를 **하나의 논리적 단위로 묶는 방법이다.**
- 개념적으로 한 트랜잭션 내의 모든 읽기와 쓰기는 하나의 연산으로 실행된다.
- 트랜잭션은 전체가 성공(Commit)하거나 실패(RollBack)한다.

> ACID는 트랜잭션이 안전하게 수행된다는 것을 보장하기 위한 성질이다.

## Atomicity
- 더 작은 부분을 쪼갤 수 없다는 뜻이다. (All or Nothing)
- 만약 A 쓰레드에서 원자적 연산을 수행한다고 하면, B 쓰레드에서는 중간에 알 수 없다.
- 시스템 연산을 실행하기 전과 후 상태만 있을 수 있고 중간 상태는 있을 수 없다.
- 이 개념을 트랜잭션에 적용한다면, 트랜잭션이 원자성을 보장한다는 의미는 트랜잭션의 결과는 Commit과 RollBack만 존재할 수 있다는 뜻이다.
- Commit과 RollBack 이전의 중간 연산이 DB에 반영될 수 없다는 의미다.

### 트랜잭션의 범위
- 트랜잭션의 범위는 커넥션을 기준으로 한다.
- 여러 메소드를 하나의 트랜잭션으로 묶고 싶다면, 여러 메소드가 하나의 커넥션을 사용하도록 만들어야한다.
- **트랜잭션 전파**가 바로 이렇게 여러 메서드 호출을 하나의 트랜잭션으로 묶고자 만들어졌다.

### 트랜잭션과 외부 연동
- 트랜잭션 내부에 외부 연동이 섞여 있으면 롤백 처리에 주의해야 한다.
- 외부 API 호출에 성공한 뒤 이후의 쿼리 작업에서 문제가 발생하더라도 외부 API와 관련된 내용은 롤백되지 않는다.

## Consistency
- 트랜잭션 작업처리 결과는 항상 일관성 있어야 한다.
- 데이터베이스는 항상 일관된 상태로 유지되어야 한다.
- 트랜잭션이 일어난 이후의 데이터베이스는 데이터베이스의 제약이나 규칙을 만족해야한다.
- 일관성은 데이터베이스의 속성이지만 일반적으로 일관성에 대한 책임은 애플리케이션에 있다.
- 애플리케이션에서 데이터가 유효한지 아닌지를 정의하고, 데이터베이스는 단순히 데이터를 저장할 뿐이다.
- 데이터베이스가 일관성을 위해 사용할 수 있는 방법은 외래키 제약 조건 또는 Unique 제약 조건 뿐이다.

## Isolation
- 격리성은 동시에 실행되는 트랜잭션은 서로 격리된다는 뜻이다.
- 서로 다른 트랜잭션이 동일한 데이터베이스 레코드에 접근하면 동시성 문제가 발생할 수 있다.
- ACID 특성을 모두 준수하려고 노력하다보면 동시성에 대한 성능이 저하될 수 밖에 없다.
- Isolation 성질을 완벽하게 준수하기 위해선 Serializable 하게 트랜잭션을 사용해야한다.
- 이 방식에서는 하나의 트랜잭션에서 특정 데이터를 점유하고 있다면 다른 모든 트랜잭션은 대기를 해야한다. (성능 저하)
- 따라서 트랜잭션 격리 수준을 나누어 성능과 정합성 사이의 최적의 지점을 찾아야 한다.

### READ-UNCOMMITTED

![image](https://github.com/khj1/TIL/assets/74335027/56d505d1-709a-4c25-a24c-8fb96ebc73df)

- 격리 수준이 가장 낮다.
- 커밋이나 롤백 전의 트랜잭션의 데이터 변경 내용을 다른 트랜잭션에서 읽을 수 있게 허용한다.
- 정합성에 문제가 많은 격리 수준이기 때문에 사용하지 않는 것을 권장한다.

#### Dirty Read
- 트랜잭션 작업이 완료되지 않았음에도 다른 트랜잭션에서 변경 내용을 조회할 수 있다.

### READ-COMMITTED

![image](https://github.com/khj1/TIL/assets/74335027/66217114-9c0b-42e4-be67-9790bd65f935)

- Commit이 완료된 트랜잭션의 변경 사항만 다른 트랜잭션에서 조회할 수 있게 허용한다.
- Dirty Read가 발생하지 않는다.
- Undo 영역의 백업된 레코드에서 값을 가져온다.
- 많은 서비스에서 가장 많이 사용하는 트랜잭션 격리 수준이다.
- 하지만 Non-Repeatable Read가 발생할 수 있다.

> **Undo 영역?** <br />
> - 트랜잭션이 완전히 Commit 되거나 Rollback 되기 이전에 데이터 원본을 저장하는 영역이다.
> - 트랜잭션이 Rollback 됐을 때 변경된 데이터를 복구할 때도 사용된다.
> - Undo 영역에는 하나의 데이터에 대해 여러 개의 백업 데이터가 존재할 수 있다.
> - DBMS는 불필요한 백업 데이터를 판단하고 백그라운드 쓰레드를 통해 주기적으로 삭제한다.

#### Non-Repeatable Read
- 한 트랜잭션 안에서 특정 데이터를 조회할 때마다 항상 같은 결과 값을 가져오지 않는 문제를 의미한다.
- Non-Repeatable Read는 금전을 다루는 서비스에서 특히 주의해야 한다.
- **해결 방법**
  - Transaction A가 커밋이나 롤백을 하기 전까진 Transaction B의 실행을 지연시킨다.
  - MVCC (Multiversion Concurrency Control)을 사용한다.

> **MVCC?** <br />
> - DBMS에서 서로 세션이 동일한 데이터에 접근했을 때 각 세션마다 스냅샷 이미지를 보장해주는 메커니즘이다. (Undo 영역)
> - 동일한 데이터에 대해 여러 버전이 존재하기 때문에 MVCC라고 불린다.
> - MVCC의 최종적인 목표는 Lock을 사용하지 않고도 일관된 읽기를 제공하는 것이다.
> - InnoDB에서는 Undo log를 활용하여 MVCC 기능을 제공하고 있다.

### REPEATABLE READ

![image](https://github.com/khj1/TIL/assets/74335027/a6fbb3b2-bcc7-48c1-8c10-954799ab4190)

- 트랜잭션 범위 내에서 조회한 내용이 항상 동일함을 보장하는 격리 수준이다.
- MySQL의 InnoDB에서 기본적으로 사용하는 트랜잭션 격리 수준이다.
- 트랜잭션은 각자 고유한 번호를 가지고 있다. (순차적으로 증가한다.)
- Undo 영역에서 백업된 모든 레코드에는 변경을 발생시킨 트랜잭션의 번호가 포함되어 있다.
- 하나의 트랜잭션 안에서 일어나는 모든 SELECT 쿼리는 Undo 영역에서 자신의 트랜잭션 번호보다 작은 트랜잭션 번호에서 변경한 사항들만 조회할 수 있다.
- 다만 Repeatable Read는 새로운 레코드의 추가까진 막지 못한다.
- 따라서 Phantom Read가 발생할 수 있다.

#### Phantom Read

![image](https://github.com/khj1/TIL/assets/74335027/f0e0d433-2f10-45d2-975f-3f6025d6e7cc)

- Non-Repeatable Read의 일종이다.
- SELECT 쿼리를 사용할 떄 조회되는 데이터에서 새로운 행이 추가되거나 사라지는 현상을 의미한다.
- SELECT FOR UPDATE, SELECT FOR SHARE 등의 Lock을 사용하는 경우 발생할 수 있다.
- Undo 영역은 append only 형태이므로 잠금 장치가 없다.
- 따라서 SELECT FOR UPDATE, SELECT FOR SHARE의 경우 Undo 영역이 아닌 테이블의 레코드를 직접 가져온다.
- 이로 인해 Phantom Read가 발생하는 것이다.

> InnoDB는 **index-row locking**과 **gap locking**을 섞은 **next-key locking** 알고리즘을 사용하고 있기 때문에 Repeatable Read에서도 Phantom Read를 예방할 수 있다.

### SERIALIZABLE
- 가장 높은 트랜잭션 격리 수준이다.
- 한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없다.
- 인덱스 기반의 잠금이나, 조건(WHERE) 기반 잠금 등의 방법을 사용한다.
- Phantom Read가 발생하지 않는다.
- Deadlock에 걸릴 가능성이 존재한다.
- 동시성 처리 성능이 저하된다.

## Durability
- 성공적으로 Commit이 되었다면 하드웨어 결함이 발생하거나 데이터베이스가 죽더라도 데이터가 손실되지 않는 것을 보장한다는 뜻이다.
- 메모리가 아니라 SSD나 HDD와 같은 비휘발성 저장소에 데이터가 기록되어있다는 뜻이다.
- 하지만 완벽한 지속성은 존재하지 않는다. 하드디스크와 백업 데이터가 모조리 날아갈 수도 있다.

## 트랜잭션 예외
### MySQL 명령어와 트랜잭션 예외
- 모든 명령어에 대해 트랜잭션의 롤백 명령이 적용되는 것은 아니다.
- DDL문 (CREATE, DROP, ALTER, RENAME, TRUNCATE)은 트랜잭션의 롤백 대상이 아니다.

## MySQL 트랜잭션 전역 설정
- MySQL은 기본적으로 AUTOCOMMIT이 활성화 되어있다.
- MySQL에서 명령어를 입력할 때 마다 커밋이 이루어지는 것이다.

#### 커밋 설정 변경
```
-- 오토커밋 off
SET AUTOCOMMIT = 0;

-- 오토커밋 on
SET AUTOCOMMIT = 1;

-- 오토쿼리 설정 확인
select @@autocommit;
```

> 트랜잭션 기능은 MySQL의 엔진에 영향을 받는다. <br />
> DB 엔진을 InnoDB로 설정해야 트랜잭션을 사용할 수 있다.