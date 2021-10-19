# 타임리프 유틸리티 객체
- [유틸리티 객체 종류](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#expression-utilityobjects) 
- [유틸리티 객체 예시](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#appendix-b-expressionutility-objects)

> **참고**
> 
> 이런 유틸리티 객체들은 필요할 때 찾아서 사용하면 된다.

## 자바8 날짜
- 타임리프에서 자바8 날짜인 `LocalDate`, `LocalDateTime`, `Instant를` 사용하려면 추가 라이브러리가 필요하다.
- 스프링 부트에서 타임리프를 사용하면 해당 라이브러리가 자동으로 추가되고 통합된다.

<br>

### 자바8 날짜용 유틸리티 객체
#### `#temporals`
- 기본 사용법
```html
<h1>LocalDateTime</h1>
<ul>
    <li>default = <span th:text="${localDateTime}"></span></li>
    <li>yyyy-MM-dd HH:mm:ss 
        = <span th:text="${#temporals.format(localDateTime, 'yyyy-MM-dd HH:mm:ss')}"></span></li>
</ul>
```
- 관련 메서드
```html
<h1>LocalDateTime - Utils</h1>
<ul>
    <li>${#temporals.day(localDateTime)} 
        = <span th:text="${#temporals.day(localDateTime)}"></span></li>
    
    <li>${#temporals.month(localDateTime)} 
        = <span th:text="${#temporals.month(localDateTime)}"></span></li>
    
    <li>${#temporals.monthName(localDateTime)} 
        = <span th:text="${#temporals.monthName(localDateTime)}"></span></li>
    
    <li>${#temporals.monthNameShort(localDateTime)} 
        = <span th:text="${#temporals.monthNameShort(localDateTime)}"></span></li>
    
    <li>${#temporals.year(localDateTime)} 
        = <span th:text="${#temporals.year(localDateTime)}"></span></li>
    
    <li>${#temporals.dayOfWeek(localDateTime)} 
        = <span th:text="${#temporals.dayOfWeek(localDateTime)}"></span></li>
    
    <li>${#temporals.dayOfWeekName(localDateTime)} 
        = <span th:text="${#temporals.dayOfWeekName(localDateTime)}"></span></li>
    
    <li>${#temporals.dayOfWeekNameShort(localDateTime)} 
        = <span th:text="${#temporals.dayOfWeekNameShort(localDateTime)}"></span></li>
    
    <li>${#temporals.hour(localDateTime)} 
        = <span th:text="${#temporals.hour(localDateTime)}"></span></li>
    
    <li>${#temporals.minute(localDateTime)} 
        = <span th:text="${#temporals.minute(localDateTime)}"></span></li>
    
    <li>${#temporals.second(localDateTime)} 
        = <span th:text="${#temporals.second(localDateTime)}"></span></li>
    
    <li>${#temporals.nanosecond(localDateTime)} 
        = <span th:text="${#temporals.nanosecond(localDateTime)}"></span></li>
</ul>
```