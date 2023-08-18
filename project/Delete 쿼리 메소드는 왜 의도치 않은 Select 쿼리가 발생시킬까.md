# Delete 쿼리 메소드는 왜 의도치 않은 Select 쿼리가 발생시킬까?

## 원인 파악
리프레시 토큰 값오르 DB에서 리프레시 토큰 객체를 제거하는 기능을 테스트 하던 중 의도하지 않은 select 쿼리가 발생하는 것을 확인했다.

```
Expected: 
delete ~ where refresh_token_value = ?

Acutal: 
select ~ where refresh_token_value = ?
delete ~ where refresh_token_id = ?
```

#### `deleteById` 구현 코드
```java
@Transactional
@Override
public void deleteById(ID id) {
    Assert.notNull(id, ID_MUST_NOT_BE_NULL);

    delete(findById(id).orElseThrow(() -> new EmptyResultDataAccessException(
            String.format("No %s entity with id %s exists!", entityInformation.getJavaType(), id), 1)));
}
```
예시로 `deleteById` 구현코드를 살펴보면 엔티티를 삭제하기 전에 해당 엔티티가 실제로 존재하는지 검증하는 과정을 거치고 있다. 처음 의도한 대로 delete 쿼리만 단 한번 보내도록 하려면 어떻게 수정해야할까?

> 조회 쿼리가 선행하는 부분은 단일 연산시에는 크게 문제가 되지 않는다. 하지만 벌크 연산을 수행한다고 가정한다면 성능 저하의 원인이 될 수 있다.

## 문제 해결
```java
@Modifying
@Query("delete from RefreshToken r where r.tokenValue = :tokenValue")
void deleteByTokenValue(@Param("tokenValue") final String tokenValue);
```
- `@Query` 어노테이션으로 JPQL을 직접 정의해주는 방식으로 문제를 해결했다.
- JavaDoc에 의하면 INSERT, UPDATE, DELETE 쿼리를 `@Query` 어노테이션으로 직접 정의해준 경우 반드시 `@Modifying` 어노테이션을 함께 쓰라고 강제하고있다.

> `@Query` 어노테이션으로 데이터 변경 쿼리를 작성할 때 `@Modifying` 어노테이션을 함께 사용하지 않으면 `InvalidDataAccessApiUsageException: Expecting a SELECT query` 예외가 발생한다. 즉, 데이터 변경 시 반드시 `@Modifying` 어노테이션을 붙여줘야 한다.

> `No EntityManager with actual transaction available for current thread - cannot reliably process 'remove' call` 예외가 발생한다면?<br />
> delete 메소드가 실행되는 시작점에 `@Transactional` 어노테이션을 붙여서 해결하면 된다.

## `@Query`와 `@Modifying`

### `@Modiyfing` 어노테이션은 왜 필요한걸까?
`@Modifying` 어노테이션은 `@Query` 메소드가 변경 작업을 수행할 때 해당 메서드가 데이터베이스의 내용을 수정한다는 것을 Spring Data JPA에게 알려주는 역할을 하는 것이다.
- `@Query` 어노테이션만 사용하면 해당 메서드가 트랜잭션의 관리를 받지 못할 수 있다. `@Modifying` 어노테이션은 메서드를 트랜잭션 내에서 실행하도록 도와준다.
- `@Modifying` 어노테이션은 이전에 캐시된 데이터가 의도치않게 재사용되는 것을 방지하기 위한 추가적인 처리를 수행할 수 있다.

`@Query` 어노테이션으로 데이터를 수정, 삭제 또는 추가하는 작업을 할 때, `@Modifying` 어노테이션을 통해 데이터의 일관성과 정확성을 유지하며 효율적인 트랜잭션 관리를 할 수 있습니다.

### JPQL 벌크 연산 시 주의 사항
`@Query`로 정의된 벌크연산 JPQL은 기존 JPA처럼 영속성 컨텍스트를 거쳐 쓰기 지연 SQL로 동작하는 것이 아니라 바로 DB로 쿼리를 날린다. 즉 영속성 컨텍스트를 거치지 않기 때문에 영속성 컨텍스트와 동기화되지 않는다. 이는 쿼리 실행 후 수동으로 `em.flush`를 해주면 해소되지만 번거롭다.

> JPA의 경우 엔티티의 값을 변경하는 메소드를 실행해도 `flush`해주기 전까진 DB에 쿼리를 날리지 않는다. `flush`를 해주면 1차 캐시와 DB의 내용이 동기화되기 때문에 의도한 값을 조회해주기 위해선 `flush`를 호출해줘야 한다.

이러한 문제를 해결해주기 위해 `@Modifying(clearAutomatically = true)`로 설정해주게 되면 쿼리 실행 직후 영속성 컨텍스트가 비워지기 때문에 데이터 조회 시 DB에 반영된 내용을 그대로 가져올 수 있다.

### JPQL은 쿼리 실행 직전에 `flush`를 강제한다.
Hibernate에는 `flushModeType`이라는 속성이 있다. `flushModeType`는 `AUTO`가 기본값인데, 이는 쿼리가 실행되기 **직전에** `flush`가 동작하도록 한다.

> 또다른 속성으로 `COMMIT`이 있는데 이는 트랜잭션 커밋 이후에 `flush`가 발생하도록 한다.

따라서 `@Modifying` 어노테이션에 `flushAutomatically` 속성을 `true`로 설정하지 않아도 쿼리를 실행하면 자동으로 `flush`된다.

## 참고
- [JPA 에서 entity를 삭제하는 여러가지 방법과 차이](https://aljjabaegi.tistory.com/681)