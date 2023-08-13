# DB에 저장되는 LocalDateTime이 반올림 되는 문제 해결 과정

JPA Auditing으로 생성된 `createdDate`을 DB에 저장한 뒤 값을 조회하니 DB에 저장된 `createdDate`와 서버에서 생성된 `createdDate`의 값이 서로 달랐다.

```java
@Test
void findFirstCreatedDateByMemberId() {
	/... 생략 .../

	LocalDateTime createdDate = duoRepository.findFirstCreatedDateByMemberId(member.getId())
			.orElseThrow();

	assertThat(createdDate).isEqualTo(latestDuo.getCreatedDate());
}
```

```
Expected: 2023-08-08T10:56:30.831718300 (java.time.LocalDateTime)
Actual: 2023-08-08T10:56:30.831718 (java.time.LocalDateTime)
```

서버에서 생성된 `LocalDateTime`은 DB에 저장 시 Micro 단위에서 반올림 된다고 한다.

### 1차 수정

```java
@Test
void findFirstCreatedDateByMemberId() {
	Duo duo = memberRepository.save(테스트_듀오());

	LocalDateTime createdDate = duoRepository.findFirstCreatedDateByMemberId(member.getId())
			.orElseThrow();

	assertThat(createdDate).isEqualTo(latestDuo.getCreatedDate().truncatedTo(ChronoUnit.MICROS));
}
```

서버에 생성된 `createdDated`에 `truncatedTo(ChronoUnit.MICROS)`를 사용해 문제를 해결함. 하지만 테스트를 수행하는 시점에 따라 테스트 결과가 달라지는 또 다른 문제가 발생했다.

원인은 `truncatedTo()`메서드는 마이크로 단위의 시간을 절삭시켜버리지만 DB에 저장된 값은 반올림되기 때문에 `LocalDateTime`이 생성된 시간에 따라 테스트 결과가 달라진 것이다.

### 2차 수정
```java
@Configuration
@EnableJpaAuditing(dateTimeProviderRef = "localDateTimeProvider")
public class JpaAuditingConfig {

	@Bean
	public DateTimeProvider localDateTimeProvider() {
		return new LocalDateTimeProvider();
	}

	private static class LocalDateTimeProvider implements DateTimeProvider {

		@Override
		public Optional<TemporalAccessor> getNow() {
			return Optional.of(LocalDateTime.now().truncatedTo(ChronoUnit.MICROS));
		}
	}
}
```

- JPA Auditing시 생성된 `LocalDateTime.now()`의 MICRO 단위가 절삭되도록 일괄 처리
- 직접 정의한 `CustomDateTimeProvider`를 `dateTimeProviderRef`에 등록해줘야 제대로 동작한다.