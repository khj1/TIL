# 서블릿 (Servlet)
## 서블릿이란?
- 사용자의 요청(request)를 받아서 요청을 분석한 후
- 비즈니스 로직(Model)을 호출하고
- Model로부터 그 결과값을 받아서
- request나 session 영역에 저장한다.
- 저장한 결과 값을 뿌릴 적절한 view (jsp페이지)를 선택하는 역할을 담당한다.
- 최종적으로 선택된 view (jsp페이지)가 요청한 사용자에게 전달(response)된다.

- 서블릿은 MVC 모델에서 Controller에 해당한다.
- 서블릿은 Servlet interface를 implements하여 생성한 자바 클래스이다.
- 서블릿은 서블릿 엔진에 의해 구동된다.
- 서블릿의 모든 메소드는 쓰레드로 동작된다.
- 서블릿은 JSP의 기반 기술이다.

## 서블릿(Model2 방식)의 처리과정
클라이언트는 요청명을 통해 서블릿을 직접 호출하게되고 JSP는 컨트롤러와 모델이 처리한 최종 결과물을 출력하는 용도로만 사용된다.

## 서블릿 작성 규칙
- javax.servlet 패키지 와 javax.servlet.http패키지 그리고 java.io패키지를 기본적으로 import한다.
- 클래스명 앞에 public 을 반드시 붙인다.
- 서블릿은 extends HttpServlet을 상속받아야 한다.
- ServletException,IOException 예외를 선언해 줘야 한다
- doGet()메서드나 doPost()메서드 메서드를 반드시 오버라이딩 해야한다. (service()는 두가지 요청 모두를 받을수있음)
- 서블릿 엔진은 사용자의 요청을 받았을때 서블릿 클래스에 정의 되어 있는 doGet() 또는 doPost() 메소드를 호출한다. 파라미터는 HttpServletRequest, HttpServletResponse 두개의 클래스를 사용한다.
  - 서블릿으로 요청이 들어가면 service() 메소드가 요청을 받아서 GET방식인지 POST 방식인지
판단을 하고 해당하는 doGet() 혹은 doPost()메소드를 실행한다. (메소드 호출 없이 자동적으로 실행된다.) 이때 doGet 혹은 doPost 메소드가 매개변수로 받는 request, response 객체는 서블릿이 자체적으로 갖고있는 객체들이다.
  - HttpServlet을 상속받은 Servlet 클래스 (이하 하위 클래스) 에서 service() 메서드를 
오버라이드 하지 않았다면, 그 부모인 HttpServlet의 service()가 호출된다.

## web.xml에서 요청명을 매핑
```xml
<!-- 서순이 반드시 지켜져야 한다. -->
<servlet>
  	<servlet-name>HelloServlet</servlet-name>
  	<servlet-class>servlet.HelloServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <!-- url은 컨텍스트 루트를 제외하며 반드시 /로 시작해야한다. -->
    <url-pattern>/13Servlet/HelloServlet.do</url-pattern>
</servlet-mapping>
```

## 어노테이션을 이용해서 요청명을 매핑
- 서블릿 페이지에서 클래스 상단에 ```@WebServlet("요청명")```을 작성한다.
- 요청명을 *.요청명으로 지정하면 해당 요청명과 동일한 모든 요청을 받을 수 있다.

## 서블릿의 라이프 사이클
생명주기에 관한 모든 메서드는 서블릿 엔진이 호출한다. 개발자가 호출하지 못한다.
- ```init()```
  - 클라이언트가 요청 시 해당 클래스를 메모리에 로딩 시 최초 실행된다. (단, 한번만 수행된다)
  - init() 메소드가 호출되고 나서 invoker라는 thread가 start 되는데, invoker는 service() 메소드를 호출한다.

- ```service()```
  - thread로 처리되는 메소드로 서블릿 엔진은 클라이언트의 두번째 요청을 받았을 때 다시 service() 메소드를 호출하여 서블릿 클래스에 정의되어있는 ```doGet()``` 혹은 ```doPost()``` 메소드를 호출한다. 즉 여러번 호출될 수 있다.

- ```destroy()```
  - 톰캣을 중지할 때 딱 한번 수행된다. 또 다른 클라이언트가 접속하면 service() 메소드가 다시 실행된다.


# 부록
- HTTP상의 데이터 전송 방식
  - GET 방식
    - http 헤더에 정보를 전달
    - 기본 정보 전달 방식
    - URL뒤에 이름=값의 형태로 정보 전달, 여러개의 값을 전달할때는 & 로 연결
    - 최대 4kb까지 전달
  - POST 방식 
    - http Body에 정보를 전달
    - URL에 노출이 안되기때문에 보안이 GET방식보다 우수
    - 데이터 사이즈에 제한이 없음