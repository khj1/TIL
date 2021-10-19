# 내장 객체의 영역(Scope)
## 공통
- 4가지 주요 메소드
    ```jsp
    setAttribute(String name, Object);
    Object getAttribute(String name);
    removeAttribute(String name);
    Enumeration getAttributeNames()
    ```
- JSP의 모든 영역들은 모든 타입의 객체를 저장할 수 있다. Object 타입으로 자동 형변환되어 저장되고, 사용시에는 각 객체로 형변환 후 사용하면 된다.
- 영역의 크기 page < request < session < application

## page 영역
- pageContext 객체를 가진다.
  ```jsp
    pageContext.setAttribute(key, value);
    pageContext.getAttribute(key);
  ```
- 한번의 클라이언트 요청에 대하여 하나의 JSP 페이지가 호출된다.
- 페이지를 벗어나는 순간 영역이 소멸된다. 즉 다른 페이지와는 속성이 공유되지 않는다.
- EL, JSTL에서 자주 사용된다.

## request 영역
- request 객체를 가진다.
- 클라이언트가 요청할 때 마다 새로운 request 객체가 생성되고 ***같은 요청을 공유하는 페이지가 대응된다.*** (include/forward)
- 요청한 jsp 페이지가 다른 jsp를 ```<jsp:include>```, ```<jsp:forward> ```하는 경우 이 두 페이지는 같은 요청 범위에 속하게 되어 같은 request 객체를 공유하게 된다.
  - ```<%@ include file = "경로" %> ```
  - 포워드를 이용한 페이지 전달 방법
    ```jsp
    RequestDispatcher 객체이름 = request.getRequestDispatcher("경로");
    객체이름.forward(request, response);

    request.getRequestDispatcher("경로").forward(request, response);
    ```
    - 페이지를 전달하는 것과 페이지를 이동하는 것은 다르다.
    - 포워드는 기존 페이지의 도메인을 유지하면서 포워드된 경로의 파일을 화면에 출력한다.
## session 영역
- session 객체를 가진다.
- 웹 브라우저를 최초로 연 후 닫을 때까지 요청되는 모든 페이지들은 같은 session 객체들을 공유하게 된다. (페이지를 닫는 것과 웹 브라우저를 닫는것은 완전히 다르다.)
- 즉 session 이란 클라이언트가 서버에 접속해있는 상태 혹은 단위를 말하는 것이다.

## application 영역
- application 객체를 가진다.
- 웹 어플리케이션 당 1개의 application 객체가 생성되고, 같은 웹 어플리케이션에서 요청되는 페이지들은 같은 application 객체를 공유하게된다.

## 영역에 저장된 속성의 소멸 시기
- page 영역: page를 벗어날 때 소멸된다.
- request 영역: 서버에서 요청에 대해 응답이 완료될 때 소멸된다.
- session 영역: session 종료 시 소멸된다.
- application 영역: 서버 종료 시 소멸된다.

## 부록
- Paramter와 Attribute의 차이
  - Parameter
    - Parameter는 request 객체만 갖고있으며, 클라이언트의 요청 정보를 전달해준다.
    - Parameter는 String으로만 구성되어있다.
    - Parameter는 Request body의 정보와 쿼리스트링으로 전달된 정보를 담고있다.
  - Attribute
    - Attribute는 request 뿐만 아니라 page, session, application이 모두 갖고있다.
    - Attribute는 모든 객체를 저장할 수 있다.
    - Attribute는 개발자가 코딩으로 설정하는 값이다.
    - 따라서 ```HttpServletRequest``` 클래스에는 ```setAttribute()``` 메소드는 있지만 ```setParameter()``` 메소드는 없다.