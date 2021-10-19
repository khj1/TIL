# FrontController
## FrontController 패턴 특징
- 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받는다.
- 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출한다.( 입구를 하나로! )
- 이로인해 공통 처리가 가능해진다.
- 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 된다.

### 스프링 웹 MVC와 프론트 컨트롤러
- 스프링 웹 MVC의 핵심도 바로 **Front Controller**에 있다.
- 스프링 웹 MVC의 **DispatcherServlet**이 FrontController 패턴으로 구현되어있다.

## 프론트 컨트롤러 패턴 도입 - v1
[v1](../../img/V1.png)