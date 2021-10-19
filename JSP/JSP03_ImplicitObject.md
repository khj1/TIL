# 내장 객체(ImplicitObject)
## JSP 컨테이너(_jspService())에 의해 자동으로 생성되어 제공되는 객체
- 별도의 객체 생성 없이 각 내장 객체의 메소드들을 사용할 수 있다.
- 내장 객체는 스크립트릿과 표현식에서만 사용할 수 있다. 선언부에선 사용할 수 없다.
## request
- JSP에서 가장 많이 사용되는 객체로 클라이언트가 웹브라우저를 통해 전송한 요청 정보를 제공하는 객체이다.
- request 객체가 제공하는 기능
  - 클라이언트와 관련된 정보
    - getParameter() 메소드
    - getParameterValues() 메소드
      - Enumeration<E> 타입의 자료들을 반환한다.
      - 주로 checkbox의 value 값들을 전송받을 때 사용한다.
  - 서버와 관련된 정보
  - 요청 Parameter 및 HTTP 헤더
    - 웹브라우저가 HTTP 프로토콜을 이용해 요청 정보를 웹 서버로 전송할 때 HTTP 요청 헤더에 부가적인 정보를 담아 전송한다.
    - HTTP 요청 헤더를 통해 정보를 전송한 클라이언트의 사용언어, 쿠키, 브라우저 환경, 최근 방문한 웹사이트 등의 정보를 확인할 수 있다.
      - accept-language : 클라이언트가 지원하는 언어
      - user-agent : 클라이언트의 웹 브라우저 환경
      - host : 호스트의 이름과 URI의 Port 번호
      - connection : 클라이언트와 서버의 연결 방식
      - cookie
      - referer : 바로 직전에 머물었던 웹 링크 주소
    - getHeaderNames(): HTTP 헤더가 담고있는 헤더의 이름들을 출력한다.
    - getHeader(headerName) : 해당 헤더의 값을 출력한다.

## response
  - HttpServletResponse 타입의 객체이다.
  - 응답 헤더에 헤더 정보를 기록하거나 페이지 이동(redirect) 기능을 제공한다.
    - 응답 헤더 설정
      - 새로운 내용을 DB에 추가했는데도 변경된 내용이 웹브라우저에 출력되지 않는다면 이는 캐시에 저장된 내용을 출력하고 있기 때문이다. 아래 설정은 캐시를 사용하지 않고 매번 새로고침(F5)한 것처럼 새로운 정보로 갱신하겠다는 응답헤더 설정이다.
        ```jsp
        response.setHeader("Pragma", "no-cache");
        response.setHeader("cache-control", "no-cache");
        ```
      - 파일 다운로드 구현 시 사용하는 설정
        - 웹 브라우저가 인식하지 못하는 MIME 타입으로 설정하면 웹 브라우저는 파일 다운로드 창을 통해 파일을 다운로드 시켜버린다. jpg와 같은 이미지 파일은 웹 브라우저가 인식하는 MIME 타입 이므로 다운로드를 위해서는 아래와 같은 설정이 필요하다.
          ```jsp
          response.setContentType("binary/octect-stream");
          ```
      - 응답 헤더 추가/변경
        - set 계열
          - numberOfCrew가 99로 변경되고 'numberOfCrew: 50'이 1개 출력됨
            ```jsp
            response.addIntHeader("numberOfCrew", 100);
            response.setIntHeader("numberOfCrew", 50);
            ```
        - add 계열
          - numberOfCrew가 100으로 유지되고 'numberOfCrew: 100'이 3개 출력됨
            ```jsp
            response.addIntHeader("numberOfCrew", 100);
            response.addIntHeader("numberOfCrew", 50);
            response.addIntHeader("numberOfCrew", 1);
            ```
      - response.getHeaderNames()는 반환타입이 Collection<>이다.
      - response.sendRedirect
        - HTML의 a태그, JS의 location.href와 동일하게 해당 경로로 이동한다.
  
## application
  - 웹 어플리케이션 당 1개의 객체가 생성되며, 웹 어플리케이션에 포함된 모든 JSP 페이지는 하나의 application 객체를 공유한다. 웹 어플리케이션 전반에 걸쳐서 사용되는 정보를 담고 있고, 서버 정보 등을 읽어 올 수도 있다.
  - 웹 어플리케이션을 구성하는 모든 JSP(Servlet)에서 String 형의 상수를 공유할 목적으로 사용한다. web.xml에 context-param 엘리먼트를 통해 등록한다. 이를 컨텍스트 초기화 파라미터라고 한다.
  - 컨텍스트 초기화 파라미터의 param-name 속성 전체를 가져온다. 반환형은 열거형 데이터인 Enumeration 객체이다.
    ```jsp
    <%=application.getInitParameter("param-name") %>
    ```
  - ServletContext의 메소드인 getRealPath()로 서버의 물리적(절대) 경로를 얻어올 수 있다. 사용 시 컨텍스트 루트를 제외한 /로 시작하는 절대경로를 입력해야한다.
    ```jsp
    <li>application 내장 객체:
        <%=application.getRealPath("/images") %></li>
      <li>request 내장 객체:
        <%=request.getServletContext().getRealPath("/images") %></li>
      <li>request 내장 객체(주로 서블릿에서 사용):
        <%=request.getRealPath("/images") %></li>
      <li>session 내장 객체:
        <%=session.getServletContext().getRealPath("/images") %></li>
      <li>config 내장 객체:
        <%=config.getServletContext().getRealPath("/images") %></li>
      <li>this키워드(주로 선언부에서 사용):
        <%=this.getServletContext().getRealPath("/images") %></li>
    ```
- 선언부에서 서버의 물리적 경로를 받아오는 방법
  - 선언부에선 내장 객체를 직접적으로 호출할 수 없다. 따라서 아래와 같은 방법으로 물리적 경로를 호출해야한다.
  - this를 사용해서 ServletContext 객체 얻어오기
    ```jsp
    String getRealPath(){
          return this.getServletContext().getRealPath("/images");
        }
    ```
    - 여기서 this가 가리키는 것은 무엇인가?
      - jsp가 실행될 때 서블릿으로 변환된다. (서블릿은 class) this는 jsp가 서블릿으로 변환된 클래스를 가리킨다.
  - application 내장 객체를 매개변수로 전달하는 방법
    ```jsp
    String getRealPath(ServletContext app1){
          return app1.getRealPath("/images");
        }
    ```
  - application 내장 객체를 전역 변수로 선언후 사용하는 방법
    ```jsp
    ServletContext app2;
        String getRealPathString(){
          return app2.getRealPath("/images");
        }
    ```
## out
- out은 JspWriter 타입의 객체이다.
- out.clearBuffer() 메소드는 버퍼에 채워진 내용을 모두 비운다. 즉 버퍼에 저장한 내용이 모두 사라진다.
- 일단 버퍼에 데이터를 담고 버퍼가 가득차면 auto-flush 해주는 것이 일반적이다. 하지만 버퍼가 가득차지 않은 상태에서 프로그램의 실행이 마무리되면 자동으로 flush 된다.

## exception
- 페이지 지시어에서 errorPage와 isErrorPage 지시어를 통해 에러 발생 시 출력되는 에러 페이지를 지정할 수 있었다.
- 이러한 기능을 web.xml (배포 서술자)내에서도 수행할 수 있다.
  ```jsp
  <error-page>
  	<error-code>404</error-code>
  	<location>/03ImplicitObject/Exception.jsp</location>
  </error-page>
  <error-page>
  	<error-code>405</error-code>
  	<location>/03ImplicitObject/Exception.jsp</location>
  </error-page>
  <error-page>
  	<error-code>500</error-code>
  	<location>/03ImplicitObject/Exception.jsp</location>
  </error-page> 
  ```
- 에러 발생시 해당 error-code를 location에 지정한 경로의 파일로 반환한다.
- 해당 location의 파일에선 response.getStatus() 메소드를 통해 해당 에러코드를 받아올 수 있다.
- jsp에서의 예외 처리는 개발 단계가 모두 끝난 후 배포 단계에서 설정해줘야 한다.

## 부록
- 캐시란?
  - 컴퓨터 과학에서 데이터나 값을 미리 복사해 놓는 임시 장소를 가리킨다. 캐시는 캐시의 접근 시간에 비해 원래 데이터를 접근하는 시간이 오래 걸리는 경우나 값을 다시 계산하는 시간을 절약하고 싶은 경우에 사용한다. 예를 들어 캐시가 없다면 웹사이트에 띄워지는 이미지들은 매번 페이지를 새로고침할 때마다 해당 이미지의 경로에 다시 접근해야한다. 이러면 실행속도가 느려지므로 해당 데이터를 사용자 컴퓨터의 캐시라는 저장소에 저장해놓고 필요할 때마다 불러오는 식으로 활용된다.

- 컨텍스트 루트란?
  - 컨텍스트 루트는 해당 파일 또는 프로젝트의 최상단 디렉토리를 의미한다.

- JS 코드와 JSP 코드가 동일한 실행 블럭내에 존재하는 경우 JSP 코드가 우선순위가 더 높으므로 JS 코드는 무시된다. 즉 이와 같은 경우 JS 코드는 실행되지 않고 JSP 코드만 실행된다.

- DAO(Data Access Object)
	- 데이터 베이스의 Data에 접근하기 위한 객체로
	- DB 접근을 위한 로직으로 주로 구성된다.
	- MVC 패턴에서는 M(Model)에 해당한다.

- ResultSet 객체의 작동방식
  - ResultSet 객체는 JDBC 연동 시 쿼리문의 실행 결과를 담는 객체이다.
  - next()를 호출하여 커서를 다음 행으로 이동시킨다. 커서의 최초 위치는 첫번째 행 바로 뒤 (iterator와 비슷한 원리)
  - next()로 커서가 이동하면 커서가 가장 최근에 지나친 행이 반환되고 해당 행을 기준으로 get메소드를 통해 열을 조회할 수 있다. (DB의 인덱스는 1부터 시작함을 유의하자.)

- 배포 서술자(Deployment descriptor)
  - 해당 웹 어플리케이션을 개발하거나 배포할 때 여러가지 설정 값을 저장하는 용도로 사용하는 xml 파일.
	- web.xml은 배포 서술자로 웹 어플리케이션의 기본적인 내용들을 담고 있는 파일이다. 
	- 컨텍스트 초기화 파라미터
  	- 해당 웹 어플리케이션 전체에서 접근 가능한 영역으로 주로 application 내장 객체를 통해 접근하여 값을 읽어오게 된다. 일종의 상수와 같은 역할을 한다.
	- welcome-file-list
  	- 웹 어플리케이션 자체를 실행했을 때 제일 먼저 실행 될 파일명을 지정한다. 만약 아래 목록에 있는 파일이 없다면 404 에러가 발생하게된다.