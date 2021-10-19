# 타임 리프의 기본 객체들
## 기본 객체
- `${#request}`
- `${#response}`
- `${#session}`
- `${#servletContext}`
- `${#locale}`

```html
<li>request = <span th:text="${#request}"></span></li>
<li>response = <span th:text="${#response}"></span></li>
<li>session = <span th:text="${#session}"></span></li>
<li>servletContext = <span th:text="${#servletContext}"></span></li>
<li>locale = <span th:text="${#locale}"></span></li>
```

### 실행 결과
- `request` = `org.apache.catalina.connector.RequestFacade@7f3dd53a`
- `response` = `org.apache.catalina.connector.ResponseFacade@5cd072c5`
- `session` = `org.apache.catalina.session.StandardSessionFacade@44f29755`
- `servletContext` = `org.apache.catalina.core.ApplicationContextFacade@6c9c82b3`
- `locale` = `ko_KR`

***
## 편의 객체
- `#request`는 `HttpServletRequest` 객체가 그대로 제공되기 때문에 데이터를 조회하려면 `request.getParameter("data")` 처럼 불편하게 접근해야 한다.
- 이런 점을 해결하기 위해 타임리프는 편의 객체를 제공한다.

### HTTP 요청 파라미터 접근
- `${param.paramData}`
- `paramData`는 컨트롤러에서 넘겨준 데이터의 key 값

### HTTP 세션 접근
- `${session.sessionData}`
- `sessionData`는 컨트롤러에서 저장한 `setAttribute()`의 key 값

### 스프링 빈 접근( @ )
#### 스프링 빈 생성 예시
```java
@Component("helloBean")
static class HelloBean {
    public String hello(String data) {
        return "Hello " + data;
    }
}
```

#### 등록한 스프링 빈의 이름으로 바로 접근
- `${@helloBean.hello('Spring!')}`
