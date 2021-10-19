# URL 링크
## `@{...}`
### 단순 URL
- `@{/hello}`
  - 결과: `/hello`

### 쿼리 파라미터
경로 변수가 설정되어있지 않다면 `()`에 있는 부분은 쿼리 파라미터로 처리된다.
- `@{/hello(param1=${param1}, param2=${param2})}`
  - 결과: `/hello?param1=data1&param2=data2`

### 경로 변수
URL 경로 상에 변수가 있으면 `()`부분은 경로 변수로 처리된다.
- `@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}`
  - 결과: `/hello/data1/data2`

### 경로 변수 + 쿼리 파라미터
경로 변수와 쿼리 파라미터를 함께 사용할 수 있다.
- `@{/hello/{param1}(param1=${param1}, param2=${param2})}`
  - 결과: `/hello/data1?param2=data2`

***
## 부록
### 상대경로, 절대경로, 프로토콜 기준을 표현할 수 도 있다.
- 참고: https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#link-urls