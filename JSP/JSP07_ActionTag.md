# 액션태그(ActionTag)
## 액션태그란?
- JSP의 표준 태그이다.
- ```<jsp:태그명>```으로 표현한다.
- 페이지와 페이지 사이의 이동 제어나 자바빈 등을 사용하는데 이용된다.
- 액션태그의 각 속성 값에는 표현식을 이용해 값을 설정할 수도 있다. 단, 지시어에는 표현식을 사용할 수 없다.
    ```java
    <%@ include file = "<%=pageName %>" %>
    ```
    - 위와 같은 코드는 에러가 발생한다.

## 액션태그의 문법
- XML 문법을 따른다.
- 반드시 종료 태그를 사용해야한다.
- 액션 태그 사이에 주석을 달면 에러가 발생한다.
- 에러가 발생하는 케이스
    ```java
    <jsp:include page="example.jsp" flush="false">
    <!-주석을 달 수 없다. 액션태그 사이에 주석달면 에러발생됨 -->
    <jsp:param name="파라미터명" value="파라미터값">
    </jsp:include>
    ```
## 액션태그의 종류
### <jsp:include>
```java
<%@ include file = "./include/DirectivePage.jsp" %>

// 액션태그 내에서 jsp 표현식을 사용할 수 있다.
<jsp:include page = "<%=actionTagPath %>" />
<jsp:include page = "./include/ActionTagPage.jsp" />
```
- include한 페이지로 임시적으로 제어를 옯겼다가 그 실행 결과를 해당 페이지에 포함시킨다.
- 파일을 합치지 않고 내용을 출력할 때 include한 파일의 출력과 기존 파일의 출력을 통합한다.
- request 객체를 공유한다.
- include 지시어와 ```<jsp:include> ``` 액션태그의 차이점
  - ```<jsp:include page="" />```
    - 다른 jsp로 흐름 제어를 이동시켜 **실행된 결과물**을 현재 위치에 포함시키는 방식이다.
    - ```<jsp:include> ``` 액션태그는 인클루드 파일을 **컴파일 후**에 기존 페이지에 통합시킨다.
    - 기존 페이지에서 선언된 변수는 include된 페이지에서 사용할 수 없다.
    - 서로 다른 페이지를 의미하므로 page 영역은 공유되지 않는다. 하지만 예외적으로 request 영역은 공유된다.
  - ```<%@ include file=""%> ```
    - include 파일의 Code를 현재 위치에 그대로 삽입시킨 후에 jsp파일을 JAVA 파일로 변환하고 컴파일하는 방식이다.
    - 페이지 레이아웃을 모듈화 하고자 할 떄, 정적인 컨텐츠로 이루어진 페이지를 포함할 때 주로 사용한다.
    - 즉 현재 페이지와 include된 페이지는 서로 같은 페이지를 의미한다.

### <jsp:forward>
- 다른 페이지로 프로그램의 제어를 이동하는 경우에 사용한다.
- ```<jsp:forward>```를 만나기 전까지 출력을 모두 제거하고 해당하는 페이지로 이동한다. 즉 ``` <jsp:forward>``` 이전의 웹브라우저에 출력하는 jsp코드는 화면상에 출력되지 않는다.
- 출력 버퍼를 사용하지 않는 경우(buffer="none")에는 ```<jsp:forward>``` 액션태그를 사용할 수 없다.
- request 객체를 공유한다.
- 조건에 따라 여러 페이지로 이동해야 할 경우에 사용한다.
- 페이지 이동과 포워드의 차이점
  - ```<jsp:forward/>```
    - 페이지를 이동시키되, 이때 이동된 페이지와 이동시킨 페이지는 request 영역을 공유한다.
    - **forward는 클라이언트와 통신없이 서버에서만 처리되기 때문에 redirect보다 나은 성능을 보여준다.**
    - URL이 변경되지 않는다.
    - URL 창에는 최초 요청한 페이지의 경로가 보여지므로 사용자는 페이지 이동을 알지 못한다.
    - 절대 경로로 지정할 경우 Context Path(컨텍스트 루트)를 제외한 경로로 지정한다.
        ```java
        // 포워드 자바코드를 사용한 예
        request.getRequestDispatcher("/07ActionTag/ForwardActionResult.jsp").forward(request, response);
        // 포워드 액션태그를 사용한 예
        <jsp:forward page = "/07ActionTag/ForwardActionResult.jsp" />
        ```
  - ```response.sendRedirect()```
    - 메소드에 지정된 url로 페이지를 이동시킨다. **그러나 forward 액션과는 달리 새로운 request를 발생시켜 영역은 공유되지 않는다.**
    - 또한 sendRedirect는 새로운 요청을 발생시켜 페이지를 이동시킨 이후로도 로직을 계속 수행한다.
    - 로직을 수행하지 못하게 하려면 return; 해야한다. JS의 location 객체나 ```<a>```태그와 동일한 역할을 한다.
    - 절대경로를 지정할 경우 컨텍스트 루트명을 포함한 경로로 지정해야한다.
        ```java
        response.sendRedirect(request.getContextPath() + "/07ActionTag/ForwardActionResult.jsp");
        ```
    - URL이 변경된다

### <jsp:useBean>
- 자바빈(Java Bean) 객체 생성 시 사용한다. DTO와 같은 객체를 말한다.
- 자바빈 생성시 id에 지정한 이름으로, scope에 지정한 영역에 자바빈 객체를 설정한다.
  - class 속성 사용시
    - 기존에 객체가 존재하면 객체를 생성하지 않고, 기존의 객체를 그대로 사용한다.
  - type 속성 사용시
    - 기존에 객체가 존재하고 있다고 가정하기 때문에 만약 객체가 존재하지 않는다면 에러가 발생한다.
- MVC 프레임 워크의 사용과 표현언어(EL)의 사용으로 점차 사용빈도가 줄어들고 있다.
- scope에 지정한 값에 따라 자바빈 객체가 저장되는 속성이 달라진다. 기본값은 ```pageContext```이다.
    ```java
    //자바 빈 객체 생성
    <jsp:useBean id="member" class ="model.MemberDTO" scope = "request"/>

    //액션태그를 통해 빈을 생성한 것을 JAVA 코드로 표현한다면
    import model.MemberDTO;
        MemberDTO member = (MemberDTO)request.getAttribute("member");
        if(memberDTO == null){
            memberDTO = new MemberDTO();
            request.setAttribute("member", memberDTO);
        }
    ```
    - 위와 같이 객체를 생성하면 해당 객체는 requset 영역에 member 라는 이름으로 자바빈 객체를 저장한다.
#### 자바빈 규약
- Bean의 멤버 변수는 반드시 private으로 선언한다.
- Bean의 멤버 메소드는 반드시 public으로 선언한다.
- 속성에 해당하는 setter와 getter가 반드시 존재해야한다.
- 사용자 정의 메소드를 정의할 수 있다.
#### 자바빈 속성
- id
  - 생성될 자바빈 객체의 이름을 명시한다. 즉 인스턴스 변수 역할을 한다.
- class
  - 자바빈 클래스 명을 명시한다. 클래스의 패키지를 포함한 Full name을 명시하므로 import한것과 같다.
  - class 속성 대신 type도 가능하다.
  - 자바빈 객체가 존재한다고 가정하므로 만약 없으면 에러가 발생하게된다.
- scope
  - 자바빈의 유효 범위 및 공유 범위를 명시한다. page값이 기본값이다.

### <jsp:setProperty>
- 자바빈 객체의 속성을 저장하는 경우에 사용된다.
- 자바빈의 setter() 메소드를 호출한다.
    ```java
    <jsp:setProperty property = "id" name="member" value="KIM"></jsp:setProperty>		
    <jsp:setProperty property = "pass" name="member" value="1234"></jsp:setProperty>		
    <jsp:setProperty property = "name" name="member" value="김희선"></jsp:setProperty>
    ```
#### 속성
- name
  - 자바빈 객체의 이름(useBean 태그의 id 속성에서 지정한 값, 즉 인스턴스 변수를 넣어준다)
- property
  - 속성명. 즉 자바빈의 멤버 변수이다.
  - ```property = "*"```라고 지정한 경우 form의 하위 요소와 일치하는 자바빈의 모든 속성에 사용자가 입력한 값이 설정된다. 이러한 경우 value 속성을 생략한다.
- value
  - 멤버 변수에 설정한 값을 의미
  - value 대신에 param 속성을 사용할 수 있다.
  - param 속성 사용 시에는 form의 하위 요소의 이름을 넣어주면 그 하위 요소에 해당하는 값이 설정된다.
  
### <jsp:gettProperty>
- 자바빈 객체의 속성을 읽어오는 경우에 사용된다.
- 자바빈의 getter() 메소드를 호출한다.
#### 속성
- name
  - 자바빈 객체의 이름(useBean 태그의 id 속성에서 지정한 값, 즉 인스턴스 변수를 넣어준다)
- property
  - 속성명. 즉 자바빈의 멤버 변수이다.
    ```java
    <jsp:getProperty property = "id" name="member"/>
    <jsp:getProperty property = "pass" name="member"/>
    <jsp:getProperty property = "name" name="member"/>
    ```

#### getter와 getProperty
```java
<jsp:useBean id="actionMember" class="model.MemberDTO"/>
<jsp:setProperty property="*" name = "actionMember"/>
<ul>
    <li><%=actionMember.getId() %></li>
    <li><%=actionMember.getPass() %></li>
    <li><%=actionMember.getName() %></li>

    <li><jsp:getProperty property ="id" name ="member"><li>
    <li><jsp:getProperty property ="pass" name ="member"><li>
    <li><jsp:getProperty property ="name" name ="member"><li>
</ul>
```
- 다음과 같이 자바빈 객체를 생성하면 해당 객체의 속성 값은 입력된 해당 폼의 name 값에 맞게 설정된다.
- 또한 해당 객체는 속성값은 자바빈 객체를 통해서 불러올 수도 있고, DTO 객체를 통해서도 불러올 수 있다.

### <jsp:param>
- ```<jsp:inlcude>``` 액션 태그나 ```<jsp:forward> ``` 사용 시 데이터를 전달하고자 할 때 사용한다.
- String 타입의 값만 전달할 수 있다.
- String 형이 아닌 다른 자료형을 전달하고자 할 때는 param 액션태그가 아닌 내장 객체의 속성을 이용해야 한다.
    ```xml
    <jsp:include page ="ParamActionIncludeResult.jsp">
		<jsp:param value ="이몽룡" name ="name" />
		<jsp:param value ="Lee" name="id" />
	</jsp:include> 

    <jsp:forward page = "<%=pageURL%>">
		<jsp:param value ="한국소프트웨어인재개발원" name ="name" />
		<jsp:param value ="<%=paramValue %>" name="id" />
	</jsp:forward>
    ```
    - 위와 같이 parameter를 전송하면 request를 받는 페이지에서 해당 parameter를 ```request.getParameter```로 받는다.
#### 속성
- name
  - 파라미터명
- value
  - 파라미터명에 해당하는 값.

## 부록
- [```sendRedirect()``` 와 ```forward()``` 의 차이점](https://doublesprogramming.tistory.com/63)
