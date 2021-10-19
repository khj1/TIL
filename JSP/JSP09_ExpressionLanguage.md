# 표현언어(EL: Expression Language)
- 값을 표현하는데 사용되는 스크립트 언어로 JSP의 기본 문법을 보완해주는 역할을 한다. 즉 값을 출력하기 위해 사용한다.
- 기본적으로 EL은 표현(출력)하는 용도로만 사용하는 것이 좋다.
- Tomcat8.0 부터는 EL에서 변수할당이 가능해졌다. 하지만 개발 시에는 실제 서비스할 웹 서버의 버전을 반드시 확인 후 사용여부를 결정해야 한다.
    ```java
    // 변수 할당 및 즉시 출력
    ${num1 = 7 } // '7'이 출력됨

    // 변수 할당 및 별도 출력
    ${num2 = 8; ' ' } => ${num2 } // '=> 8'이 출력됨
    ```
- 자바 코드를 사용해야하는 모든 곳에서 사용 가능하다.
- 자바스크립트에서 확장하고 Xpath(XML문서의 정보에 접근하기 위해 사용되는 언어)에서 힌트를 얻어 만들어진 언어이다.
- null 값을 갖는 변수에 대해 ***좀 더 관대***하고, 데이터형 변환을 ***조금 더 자동화*** 시켜준다. 즉 값이 존재하지 않을 경우 에러를 발생시키지 않고 아무런 값도 출력하지 않는다.

## 표현 언어의 기능
- JSP의 4가지 영역 속성을 사용할 수 있다.
- 자바 클래스에서 메소드를 호출할 수 있다.
- 표현 언어만의 새로운 객체를 제공한다.
- 변수와 연산자를 포함한다.

## 표현 언어의 구문
### 형식
```java
${expression}
```
- expression에는 자바 코드에서 생성한 개체 변수에 직접 접근할 수 없다. 그러므로 JSTL을 이용하여 변수를 생성한 후 간접적으로 접근해야 한다.
    ```java
    <% 
    int num1 = 3;
    pageContext.setAttribute("num2", 4);
    %>

    ${num1 } // 출력되지 않는다.
    ${num2 } // 4
    ```
- JSP 스크립트릿, 표현식, 선언부에서는 사용할 수 없다.
- Action Tag나 JSTL등의 속성 값으로 사용할 수 있다.
    ```xml
    <c:set var="elVar" value="${elVar}" />
    <jsp:include page="${path}" />
    ```
- HTML 문서 내에서도 표현식 처럼 값을 출력하기 위해 사용할 수 있다.
    ```xml
    <b>${sessionScope.member.id}</b>
    ```
- 즉 JSP 스크립트 요소(Scripting Elements)에서만 사용할 수 없고, 다른 모든 태그 요소(HTML, ActionTag, JSTL)에서는 사용할 수 있다.

## EL에서 객체 접근
```java
${param.name} // 가능
${param["name"]} // 가능
${header["user-agent"]} // 가능
${header.user-agent} // 불가능
```
- 특수문자와 한글이 포함된 속성에는 점(.)으로 접근할 수 없다.

## 표현언어의 내장 객체
### 기본 영역에 접근
- pageScope
- requestScope
- sessionScope
- applicationScope
- 각 내용에 저장된 값을 EL에 읽어올 때 같은 속성명이 여러 영역에 저장되어있다면 EL의 내장 객체명 생략 시 작은 영역부터 순차적으로 읽어온다.

### 파라미터 or 폼값 받기
- Java 코드로 요청 파라미터를 받는 경우 String 타입으로만 받을 수 있다. 따라서 산술 연산을 위해 반드시 숫자형으로 형변환해야 한다.
- EL을 통해서 요청 파라미터를 받는 경우에는 산술연산을 위한 별도의 형변환이 필요없다.
#### param
- 요청 파라미터의 <파라미터명, 값> 매핑을 저장한 Map Collection
- 파라미터값의 타입은 String이다.
- 파라미터로 전달되는 값은 항상 String 타입이므로 산술 연산을 위해서는 반드시 int 타입으로 변환해줘야 한다.
- ```request.getParameter(파라미터명)```와 동일하다.
    ```java
    //java
    String str = request.getParameter("firstNum");
    
    //EL
    ${param.firstNum}
    ```
- 쿼리 스트링으로 전달되는 파라미터도 동일하게 받을 수 있다.
   
#### paramValues
- 요청 파라미터의 <파라미터명, 값 배열> 매핑을 저장한 Map Collection
- 파라미터 값의 타입은 String[], 즉 배열 형태로 받는다.
- ```request.getParameterValues(파라미터명)```와 동일하다.
    ```java
    //java
    String[] inters = request.getParameterValues("inter");
    
    //EL
    ${paramValues.inter }
    ```

### 객체 받기
- 파라미터로는 String 타입의 데이터만 받아올 수 있다. 객체는 파라미터로 전달할 수 없기 때문에 객체를 영역에 저장해서 전달해야한다. (영역이 존재하는 이유?)
- JSP 코드를 통해 영역에 저장된 속성을 가져올 때
  - getAttribute()으로 속성을 얻어온다.
  - 형변환한다. (영역에 저장 시 Object 타입으로 저장되기 때문이다.)
  - **단, 기본 객체는 별도의 형변환 없이 바로 사용할 수 있다.**
  - getter로 저장된 값을 출력한다.
    ```java
    // 객체 생성
    <%
    MemberDTO member = (MemberDTO)request.getAttribute("dtoObj");
    %>

    // getter를 통해 호출
    member.getId()
    member.getPass()
    member.getName()
    ```
- EL에서 영역에 저장된 속성을 가져올 떄
  - 영역에 저장된 속성명이 중복되지 않는다면 Scope 내장객체를 사용하지 않고도 읽어올 수 있고, 별도의 형변환도 필요없다. (산술 연산을 위해 int형으로 변환할 필요도 없다.)
  - getter의 호출 없이 멤버 변수의 이름만으로 저장된 값을 바로 출력할 수 있다.
  - 단, 해당 클래스의 멤버 변수에 getter가 선언되어 있지 않다면 에러가 발생한다.
    ```java
    // 영역에 dtoObj 라는 객체를 저장한 후에
    // getter가 아닌 변수의 이름만으로 값을 출력한다.
    // 내부적으로는 getter 메소드가 활용된다. (해당 클래스에 해당 변수에 대한 getter 메소드가 없으면 에러가 발생)
    ${dtoObj.id }
    ${dtoObj.pass }
    ${dtoObj.name }
    ```

### 헤더값
#### header
- 요청정보의 <헤더이름, 값> 매핑을 저장한 Map Collection
- ```request.getHeader(헤더명)```과 동일하다.
- 값을 얻어올 때는 ```${header.헤더명}``` 형태로 사용한다.
- 단 user-agent의 경우 헤더명에 -(대쉬)가 들어가기 때문에 ```header["user-agent"]```로 얻어와야한다.
    ```java
    <li>${header.host }</li>
    <li>${header.cookie }</li>
    <li>${header.user-agent }</li>// '0' 출력됨
    <li>${header['user-agent'] } </li>// 정상 출력됨
    ```
#### headerValues
- 요청 정보의 <헤더이름, 값 배열> 매핑을 저장한 Map Collection
- ```request.getHeaders(헤더명)```와 동일하다.
- 값을 얻어올 때는 ```${headerValues.헤더명}``` 형태로 사용한다.

### 쿠키
#### cookie
- <쿠키명, 값> 매핑을 저장한 Map Collection
- 값을 얻어올 때는 ```${cookie.쿠키명.value}``` 형태로 사용한다.

### 컨텍스트 파라미터
#### initParam
- 초기화 파라미터의 ```application.getInitParameter(이름)``` 결과와 동일하다
- web.xml에 설정한 정보를 얻어온다.
- 값을 얻어올 떄는 ```${initParam.파라미터명} ```
```java
//Java
<%=application.getInitParameter("PAGE_SIZE") %>

//EL
${initParam.PAGE_SIZE}
```

### pageContext 객체
#### pageContext
- JSP의 pageContext 기본 객체와 동일
- JSP에서 컨텍스트 루트를 얻어올 때 ```<%=request.getContextPath() %>``` 를 사용하는 것과 동일하다.
```java
//Java
<%=request.getContextPath()%>

//EL
${pageContext.request.contextPath}
```

## EL의 연산자
### 기본 연산자
- 기본 산술연산자에 EL만의 새로운 연산자를 추가적으로 사용할 수 있다.
  - gt ('>')
  - ge ('>=')
  - lt ('<')
  - le ('<=')
  - eq ('==')
  - ne ('!=')
  - and ('&&')
  - or ('||')
  - div ('/')
    - EL에서는 '/'와 div 연산자가 결과 값을 double로 출력해준다.
  - mod ('%')
- EL에서 '+' 연산자는 단순 덧셈 연산에만 적용된다.
  - Java 에서는 문자열을 연결할 때 '+'를 사용했지만 EL에선 적용되지 않는다.
  - EL에서 문자열을 연결하기 위해서는 EL 표현식을 문자열의 갯수만큼 사용해주면 된다.
    ```java
    ${10 + "34" } // '44'
    ${10 } + ${"34" } // '1034'
    ${10 + "이십" } // 에러발생
    ${"삼십" + "사십" } // 에러발생
    ${"삼십" } ${"사십" } // '삼십 사십'
    ```
- EL에서의 문자열 비교에 '=='을 사용할 수 있다.
    ```java
    ${"hello"=="hEllo"}
    ```
    - 자바의 경우 String을 연산자로 비교시 해당 문자열의 주소값을 비교하지만 EL에서는 문자열 값을 비교한다. String 클래스의 compareTo() 메소드 방식으로 비교 후 true 아니면 false를 반환해준다.

### empty 연산자
- null이거나 ""(빈문자열)일 떄
- 혹은 배열의 길이가 0일 때
- 컬렉션의 size가 0일 때 true를 반환하는 연산자이다.
    ```java
    <% String nullStr = null; %>
    <c:set var="elNullStr" value="<%=nullStr %>" />
    elnullStr은 Null? : ${empty elNullStr }
    ```

## EL에서의 자바 클래스 메소드 호출
### 기존의 복잡한 방법
- 호출을 위한 작성 순서
  - 자바 클래스 파일을 작성한다.
    - EL에서 클래스의 메소드를 호출하려면 클래스 정의시 메소드를 반드시 public static으로 선언해야 한다.
  - TLD(Tag Library Descriptor) 파일을 작성한다.
    - 커스텀 태그의 동작을 정의하는 XML 형식의 파일을 말한다.
    - tld파일은 WEB-INF/ 아래에 바로 놓거나 하위 디렉토리에 두면 컨테이너가 자동으로 찾는다. (WEB-INF/tlds 디렉토리에 위치)
  - 참조1 : http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/
  - 참조2 : 참조1에서 jakarta-taglibs-standard-1.1.2.zip 다운로드 압축해제 후 tld 디렉토리 하위의 fn.tld 파일참조 (jstl-1.2.jar 파일을 WEB-INF의 lib 파일에 넣어준다.)
    ```xml
    <taglib xmlns="http://java.sun.com/xml/ns/j2ee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee web-jsptaglibrary_2_0.xsd"
        version="2.0">
	
        <!-- 
        TLD 파일
            Tag Library Description의 약자로 WEB-INF폴더 하위에 생성한다.
            웹 어플리케이션 실행 시 컨테이너가 해당 파일을 찾아 사용하게 된다.
            EL에서 호출 할 메소드를 등록하는 용도로 사용된다.
        -->
        <!-- 해당 TLD 파일에 대한 간단한 설명 -->
        <description>My Tag Library</description>	
        <!-- 해당 파일의 버전 -->
        <tlib-version>1.1</tlib-version>
        <!-- 접두어: 설정 후 EL에서 메소드 호출 시 사용할 접두어를 기술함. -->
        <short-name>my</short-name>
        
        <!-- 
        EL에서 호출 할 메소드를 등록한다. 
            name: EL에서 사용 할 메소드명
            function-class: 패키지를 포함한 클래스의 풀 경로
            function-signature: 
                호출할 메소드의 원형을 기술함. 리턴 타입과 매개변수는 해당 클래스의 패키지를
                포함한 풀 경로를 사용해야 한다. 단, 기본 자료형인 경우 자료형만 기술하면 된다.
        -->
        <function>
            <name>isNumber</name>
            <function-class>eltag.MyTagLib</function-class>
            <function-signature>boolean isNumber(java.lang.String)</function-signature>
        </function>	
	
    </taglib>
    ```
  - web.xml에 ```<jsp-config>``` 엘리먼트 추가 후 tld파일의 경로를 등록한다. 
    ```xml
    <jsp-config>
	<taglib>
		<taglib-uri>uri로 사용할 문자열(별칭)</taglib-uri>
		<taglib-location>tld파일의 경로</taglib-location>
  	</taglib>
    </jsp-config>
    ```
  - 자바 메소드에 접근하는 JSP 파일을 생성한 후 페이지 디렉티브로 taglib을 선언한다.
    ```xml
    <%@ taglib prefix="my" uri="uri로 사용할 문자열" %>
    ```
  - 메소드명은 tld 파일에서 정의한 ```<function-name>``` 엘리먼트의 텍스트를 사용하여 코드에 작성한다.   
    ```java
    ${태그라이브러리접두어:메서드명(인자1[,인자2,..])}
    ```

### 간단한 방법
- 클래스를 import한 후에 객체를 생성하고 생성된 객체를 통해 메소드를 호출한다.
- 클래스명.메소드명으로 정적 메소드를 호출한다.

## 부록
- 포워드 처리된 페이지에서는 page 영역은 공유되지 않고 request 영역이 공유된다. 따라서 forward된 페이지에서 가장 좁은 영역은 request 영역이 된다. (page 영역은 소멸된다.)
- JSTL의 forEach문 사용법
  - 사용전에 ```<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>```을 선언해줘야 한다.
    ```java 
    <c:forEach items = "${paramValues.inter }" var ="s">
        ${s }&nbsp;
    </c:forEach>
    ```
- EL의 내장객체에 존재하지 않는 속성으로 접근 시 에러가 발생한다.
    ```java
    // 에러가 발생하지 않는다.
    ${pageScope.nop} // 아무것도 출력되지 않음

    // pageContext는 JSP에서 사용하는 내장객체이므로 에러가 발생한다.   
    ${pageContext.nop} // 컴파일 오류 발생
    ```
- EL에서 숫자를 '0'으로 나누면 에러가 발생하지 않고, INFINITE가 출력된다.