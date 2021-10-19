# JSTL(JSP Standard Tag Library)
## JSTL이란?
- 표준화된 custom tag이다.
- 많이 사용되는 사용자 정의 태그를 모아서 JSTL이라는 규약으로 만들었다.
- JSP 내에 Java Source를 사용하지 않고, 태그만을 가지고 작성할 수 있기 때문에 가독성을 증가시킨다.
- 스크립트릿, 표현식을 사용하는 것보다 훨씬 간결한 문법 구조를 지원한다.
- JSTL을 사용하기 위해선 JSTL 관련 파일을 다운받아 WEB-INF 하위의 lib에 담아줘야한다.
- JSTL은 5가지 태그를 지원한다.
  - **Core**
    - 기능: 변수지원, 흐름제어, URL처리
    - 접두어: c
  - XML
    - 기능: XML코어, 흐름제어, XML 변환
    - 접두어: x
    - ~~많이 사용하진 않는다.~~
  - **국제화**
    - 기능: 지역, 메시지 형식, 숫자 및 날짜 형식
    - 접두어: fmt
  - 데이터베이스
    - 기능: SQL
    - 접두어: sql
    - ~~많이 사용하진 않는다.~~
  - **함수**
    - 컬렉션 처리, String 처리
    - 접두어: fn

## JSP에서 JSTL을 사용하는 법
- taglib 지시어를 사용하여 JSTL의 태그 라이브러리에 접근한다.
    ```java
    <%@ taglib prefix="접두어" uri="각 태그의 관련 URL 주소" %>
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    ```
- 사용 시에는 ```<접두어: 태그명 />``` 와 같은 식으로 작성한다.

## Core
### EL 지원 기능
<table>
    <tr>
        <th>기능</th>
        <th>태그명</th>
        <th>기능설명</th>
    </tr>
    <tr>
        <td rowspan="2">변수지원</td>
        <td>set</td>
        <td>EL에서 사용될 변수를 설정한다. (영역에 저장)</td>
    </tr>
    <tr>
        <td>remove</td>
        <td>설정한 변수를 제거한다. (영역에서 제거)</td>
    </tr>
    <tr>
        <td rowspan="4">흐름제어</td>
        <td>if</td>
        <td>조건에 따라 내부 코드를 수행한다. 별도의 else문이 없다.</td>
    </tr>
    <tr>
        <td>choose</td>
        <td>다중 조건을 처리할 때 사용된다. switch~case문과 비슷하다.</td>
    </tr>
    <tr>
        <td>forEach</td>
        <td>Collection의 각 항목을 처리할 때 사용한다.</td>
    </tr>
    <tr>
        <td>forTokens</td>
        <td>구분자로 분리된 각각의 토큰을 처리할 때 사용한다.</td>
    </tr>
    <tr>
        <td rowspan="3">URL처리</td>
        <td>import</td>
        <td>URL을 사용하여 다른 페이지의 결과를 삽입한다.</td>
    </tr>
    <tr>
        <td>redirect</td>
        <td>지정한 경로로 이동한다.</td>
    </tr>
    <tr>
        <td>url</td>
        <td>URL을 재작성한다.</td>
    </tr>
     <tr>
        <td rowspan="2">기타태그</td>
        <td>catch</td>
        <td>예외 처리에 사용한다.</td>
    </tr>
    <tr>
        <td>out</td>
        <td>jspWriter에 내용을 알맞게 처리한 후 출력한다.</td>
    </tr>
</table>

### set 태그
```<c:set/>```은 ```setAttribute("속성명", "값")```와 같은 역할을 한다. 즉, 영역에 변수를 저장한다.
#### 형식1
```java
    // 일반적인 값 설정 시
    <c:set var="EL 변수명" value="값" scope="영역" />

    // 객체 설정 시
    <c:set var="객체 변수명" value="<%=new Member() %>" scope ="영역">

    // 객체의 속성값 설정 시
    <c:set target="var로 설정한 객체 변수명" property="객체의 속성 값" value="지정한 속성의 값">
```
  - 영역의 기본값은 page
  - var 속성과 target 속성이 서로 연결된 상태이다. **target 속성에는 반드시 표현식이나 EL 변수를 지정해야한다.** 단, target에 표현식을 사용하는 경우는 객체가 자바코드로 생성된 경우에만 가능하다.
  - target이 자바빈 객체인 경우는 반드시 그 클래스 안에 setter 메소드가 정의되어있어야 한다.
  - target이 Map인 경우는 put(key, value)를 이용해서 처리한다.
    ```java
    // 자바 코드로 객체 생성(표현식으로 객체를 호출하기 위함)
    <%
	Map map = new HashMap();
    List list = new ArrayList();

	map.put("args", request.getAttribute("argsMember"));
    list.add(request.getAttribute("argsMember"));
	%>

    // map 컬렉션을 request 영역에 map이라는 변수명으로 저장
    <c:set var="map" value="<%=map %>" scope="request"/>
    // list 컬렉션을 request 영역에 list라는 변수명으로 저장
    <c:set var="list" value="<%=list %>" scope="request"/>

    // map 컬렉션의 args 키값에 해당하는 객체를 불러온 후 해당 객체의 id 속성값을 호출
    ${map.args.id }
    // list 컬렉션은 인덱스를 통해 객체에 접근하고, 해당 객체의 id 속성값을 호출한다.
    ${list[0].id }
    ```

#### 형식2
```java
    <c:set var="EL 변수명"scope="영역">
        value 속성에 들어갈 값
    </c>
```
- EL 변수를 생성할 때 주로 활용된다.
- 변수에 값을 할당한다. 자바빈(JavaBean)이나 컬렉션과 같은 객체의 속성에 값을 할당하기 위해서는 **target**과 **property** 속성을 이용한다.
- 단, target과 property 속성은 자바빈이나 Map 계열 객체에만 적용할 수 있다.
- target과 property를 사용해서 자바빈이나 컬렉션에 값을 설정할 때는 var속성을 지정할 수 없다.


### remove 태그
- removeAttribute와 같은 역할
- 해당 scope에 있는 변수를 제거하는 역할을 한다.
- 변수를 삭제하는 경우 scope(영역)이 맞지 않으면 삭제되지 않는다. 에러도 발생하지 않는다.
- remove 태그 사용 시 scope를 지정하지 않으면 동일한 이름으로 저장된 모든 영역의 변수가 같이 삭제된다.
    ```java
    <c:remove var="변수명" [scope ="공유범위 설정"] />
    ```

### if 태그
```<c:if>```는 조건문을 사용하는 경우에 사용한다.
```java
<c:if test="조건의 판별식" //test 속성에는 표현식, EL, 또는 정적 문자열이 올 수 있다.
      var="EL 변수명" //test 속성의 조건 결과가 저장된다
	  scope="변수의 공유범위를 지정">
		조건이 true일 경우 출력할 문장  
</c:if>
```
- else 구문이 없으므로 단순한 조건문을 작성할 때 주로 사용된다. (중첩 if문은 가능하다.)
- if 구문으로 else 구문 사용하는 법
    ```java
    <c:if test="판별식" var="result">
		조건이 true일 경우 출력할 문장  
    </c:if>
    <c:if test=${not result}> //위 판별식이 false를 반환하면 해당 조건문이 실행된다.
        조건이 false일 경우 출력할 문장
    </c>
    ```
- test 속성에 EL이 아닌 일반값이 들어가면 무조건 false를 반환한다.
- 단, 일반식이더라도 TRUE(true)인 경우에는 true를 반환한다.
- test 속성에 조건을 표시할 때 {} 앞뒤로 빈 공백이 들어가면 무조건 false 처리된다. 반드시 빈 공백은 제거해야한다.


## Choose ~ When ~ Otherwise 구문
- ```<c:if />``` 태그에 else 구문이 없기 때문에 이를 대체하기 위한 기능을 수행한다.
- 반드시 하나 이상의 ```<c:when>```, ```<c:otherwise>``` 와 같은 하위 태그를 가져야한다.
    ```java
    <c:choose>
        // choose 태그와 when 태그 사이에 주석을 달면 에러가 발생한다. (부록참고)
        <c:when test="조건1">조건1을 만족하는 경우</c:when>
        <c:when test="조건2">조건2을 만족하는 경우</c:when>
        <c:when test="조건3">조건3을 만족하는 경우</c:when>
        <c:otherwise>위 조건을 만족하지 않는경우</c:otherwise>
    </c:choose>
    ```

## forEach 태그
- 배열, Collection 또는 Map에 저장되어있는 값들을 순차적으로 처리할 때 사용한다.
- 일반 for문과 확장 for문을 결합한 형태이다.
```java
//일반 for문
<c:forEach
    [begin="시작값"] [end="마지막값"] [step="증가값"] 
     var="변수명" [varStatus="varStatus 변수명"] />

//확장 for문
<c: forEach
    items = "컬렉션 혹은 배열명" var="변수명" [varStatus="varStatus 변수명"]>
```
  - 확장 for문에서는 인덱스 없이 참조 변수를 바로 사용할 수 있다.
  - 확장 for문에서 맵 컬렉션을 사용했고, 해당 맵의 key 값을 모를떄는 확장 for문을 사용하면 된다.
    ```java
    // 확장 for문으로 하나의 객체를 꺼내왔을 떄 사용 가능
    <c:forEach items="${maps }" var="map" >
      Key값: ${map.key }
      Value값: ${map.value.id }
    </c:forEach>
    ```
### varStatus 속성
- count
  - 실제 반복 횟수
- index
  - 변수 i의 변화하는 값
  - 일반 for문에선 begin ~ end 값
  - 확장 for문에서 index는 0부터 시작
- first
  - loop의 처음일 때 true를 반환. 나머지는 false를 반환
- last
  - loop의 마지막일 때 true를 반환. 나머지는 false를 반환
- current
  - 현재 loop의 변수 값(var에 지정된 변수) 
  - 확장 for문에선 현재 반복문에 호출된 ***실제 값***을 불러온다.


## forTokens 태그
- StringTokenizer 클래스나 String 클래스의 split() 메소드를 사용하는 것과 동일한 기능을 제공한다.
```java
<c:forTokens 
  items="문자열" 
  delims="문자열을 구분할 구분자" 
  var="변수명" />
```
- items 에는 문자열이 와야한다. forEach 태그처럼 배열이나 Collection등이 올 수 없다.


## import 태그
동일한 웹 어플리케이션의 자원뿐만 아니라, FTP, HTTP와 같은 외부 자원도 가져와 현재 위치에 삽입하거나 EL 변수에 저장할 때 사용한다.
  ```java
  <c:import
    url="url"
    [var="읽어온 데이터를 저장할 변수명"]
    [scope="변수의 공유범위"]				
    [charEncoding="읽어온 데이터의 Character set을 지정"]  
    />
  ```
- var 속성을 사용하는 경우 읽어온 데이터를 EL 변수에 저장한다. 차후 원하는 위치에 변수를 출력하는 형태로 사용할 수 있다.
- var 속성을 사용하지 않는 경우는 ```<jsp:include>``` 액션 태그와 동일하다. (request 영역을 공유한다)
  - include 액션 태그는 미리 컴파일된 결과물이 넘어온다. 따라서 페이지 영역은 공유되지 않는다.
  - include 지시어를 사용하면 소스코드가 그대로 넘어오기 때문에 페이지 영역과 request 영역이 모두 공유된다.
- 요청 파라미터는 직접 url에 추가하거나, ```<c:param name="파라미터명" value="값">```을 이용해서 추가할 수 있다. ```<jsp:param>```도 같은 형태로 사용할 수 있다.
- url 속성과 charEncoding속성에는 표현식과 EL을 사용할 수 있다.

## redirect 태그
- response.sendRedirect 기능
- 새로운 페이지에 대한 요청이므로 request 영역을 공유하지 않는다. ***단, response.sendRedirect와는 다르게 이후의 코드는 실행하지 않는다.*** (부록참조)
- ````<c:param>``` 태그를 이용해서 파라미터를 url에 추가할 수 있다. (request 영역은 공유하지 않는다.)
- url 및 context 속성에 표현식/EL 표현이 가능하다.
- 절대 경로로 설정 시 url 속성에 컨텍스트 루트는 제외한다.
- 단, jsp 내장객체의 response의 sendRedirect()로 이동하는 경우엔 컨텍스트 루트를 포함해야한다.
```java
<c:redirect
   url="이동할 URL" [context="컨텍스트경로"] />
```

## url 태그
- Query parameter로부터 URL을 생성한다.
- ```<c:param>```태그를 이용해서 파라미터를 url에 추가 할 수 있다. param 태그로 지정한 값은 쿼리스트링으로 연결된다.
- var 속성을 지정하지 않은 경우 현재 위치에 작성한 url을 즉시 출력한다.
- var 속성을 지정한 경우에는 해당 EL 변수에 작성한 url이 저장된다.
- 절대경로로 생성시에는 컨텍스트 루트를 제외해야 한다.
```java
<c:url 
  value="생성할 URL"
  [var="EL 변수명"]
  [scope="변수간의 공유 범위"] />

// 출력결과 => /10JSTL/Inc/ImportPage.jsp?user_id=Gasan&user_pw=Digital
<c:url value="/10JSTL/Inc/ImportPage.jsp">
  <c:param name="user_id" value="Gasan" />
  <c:param name="user_pw" value="Digital" />
</c:url>
```

## out 태그
JSP의 표현식을 대체하는 것으로 사용함. 화면에 해당하는 변수의 값을 출력한다.
```xml
<c:out
  value="출력할 값" 
  [default="기본값을 설정"] 
  [escapeXml="특수문자 처리유무"] />
```
- value에 지정한 값이 없을 경우 (null) default에 지정한 값이 출력된다.
  - 빈문자열("")을 입력하면 값이 있다고 판단하여 default 값이 출력되지 않는다.
- value는 일반적으로 문자열이나 Reader개체가 들어오면 out태그는 Reader로부터 값을 읽어와 jspWriter로 값을 출력한다.
- ```escapeXml = true```(기본값)일 때는 value에 작성한 HTML이 해석되지 않고, 태그까지 그대로 출력된다. **JS의 innerText()와 동일하다.**
- ```escapeXml = false```일 떄는 value에 작성한 HTML태그까지 모두 해석되어져 화면에 출력된다. **JS의 innerHTML()과 동일하다.**

## catch 태그
body 위치에서 실행되는 코드의 예외를 catch 하는 역할
```xml
<c:catch var="EL변수명">
```
- 에러가 발생한 경우 EL 변수에 에러메세지가 저장된다.
- EL은 에러에 관대하기 때문에 주로 자바코드에서 발생하는 에러를 처리할 때 사용한다.

## fmt 태그
### formatNumber 속성
숫자를 양식에 맞춰 문자열로 변환해주는 태그
```xml
<fmt:formatNumber value ="숫자값" 
  [type="값 타입"]
  [currencySymbol="통화 심벌"]
  [groupingUsed="(true/false)"]
  [var="변수명"] [scope="영역"]>
```
- type
  - number: 숫자 형식으로 출력(기본값)
  - percent: % 단위로 변환하여 출력
  - currency: 통화 형식으로 출력
- pattern
  - 패턴을 지정하여 소수점까지 출력할 수 있다.
    ```java
    <fmt:formatNumber value ="12,345" pattern ="00,000.00" />
    // 출력 결과 12,345.00 (소수점까지 출력한다.)
    ```
- currencySymbol
  - 통화 출력시 사용할 기호를 표시한다.
  - 미 지정시 현지 통화 심벌로 출력된다.
- groupingUsed
  - 콤마(,)와 같이 구분할 때 사용되는 기호의 사용 여부를 결정한다.
  - 기본값 true
- var
  - 포맷팅한 결과를 저장할 변수명
  - var 속성을 지정하지 않는 경우 해당 위치에 즉시 출력된다.

### formatDate 속성
```xml
<fmt:formatDate value="날짜값"
  [type="값 타입"]
  [dateStyle ="날짜 형식"]
  [timeStyle ="시간 형식"]
  [pattern ="출력 패턴"] />
```
- type
  - date : 날짜만 표시
  - time : 시간만 표시
  - both : 둘 다 표시
- dateStyle / timeStyle
  - full
  - short
  - long
  - default

### timeZone 속성 : 타임 존 설정
```xml
<!-- GMT: 세계 표준시로 영국의 그리니치 천문대를 기준으로 한다. -->
<fmt:timeZone value="GMT">
  full: <fmt:formatDate value="${today }" type="both" dateStyle="full" timeStyle="full"/> <br />
</fmt:timeZone>

<fmt:timeZone value="America/Chicago">
  full: <fmt:formatDate value="${today }" type="both" dateStyle="full" timeStyle="full"/> <br />
</fmt:timeZone>

<!-- 표준시에 9를 더하면 KST(대한민국 표준시)가 된다. -->
<fmt:timeZone value="GMT+9">
  full: <fmt:formatDate value="${today }" type="both" dateStyle="full" timeStyle="full"/> <br />
</fmt:timeZone>

<!-- timeZone에서 사용할 수 있는 문자열 구하기 -->
<c:forEach var="ids" items = "<%=TimeZone.getAvailableIDs() %>">
  ${ids } <br />
</c:forEach>
```

### setLocale 속성 : 지역 설정
formatDate를 설정하기 전에 해당 지역을 지정해줄 수 있다.
```xml
<!-- 다음과 같이 로케일을 설정해주면 자동적으로 원화와 한국기준의 날짜가 출력된다. -->
<fmt:setLocale value="ko_KR"/>
<fmt:formatNumber value="10000" type="currency"/>
<fmt:formatDate value="${today }"/> <br />
```

## Xml 태그
- XML(eXtensible Markup Language)
  - 사전적 의미로는 확장이 가능한 마크업 언어라는 뜻
	- HTML과 비슷하게 태그로 표현하지만, XML은 개발자가 직접 태그를 정의할 수 있음
	- HTML과 같이 보여주는 목적이 아닌, 데이터를 전달하는 것이 주 목적이다.
	- 주로 서버간의 통신, API 제작에 사용됨 
	- 현재는 XML보다 JASON을 훨씬 더 많이 사용한다.
- 파싱(Parsing)
  - 주어진 데이터를 분석하여, 원하는 데이터의 특정 패턴이나 순서로 추출하는 것을 말한다.
	- 쉽게 말해 XML이나 HTML 등으로 구성된 데이터를 분석해 내가 원하는 부분만 추출하는 것을 의미한다.

- 파싱의 예
  ```xml
  <!-- 외부 자원을 받아와서 파싱하는 경우가 훨씬 빈번하다. -->
  <c:import url="./Inc/BookList.xml" var="xmldata" charEncoding="UTF-8"/>
	<x:parse xml="${xmldata }" var="xdata"/>
	<table border="1">
    <!-- 다음과 같은 경로를 XPath라고 한다. XPath의 인덱스는 1부터 시작한다. -->
		<x:forEach select="$xdata/booklist/book" var="item">
			<tr>
				<td> <x:out select="$item/name"/> </td>
				<td> <x:out select="$item/author"/></td>
				<td> <x:out select="$item/price"/></td>
				<td>
					<x:if select="$item/name='총,균,쇠'">
						구매함
					</x:if>
				</td>
			</tr>
		</x:forEach>
	</table>
  ```

## 부록
- Map 계열의 컬렉션은 Key를 통해 Value에 접근한다. Key를 통한 접근이 Index를 통한 접근보다 속도가 빠르다.
- choose와 when 사이에서는 HTML 주석을 사용하면 에러가 발생한다.
  - choose문만으로는 문법이 구성되지 않고 when과 함꼐 쓰여야하기 때문이다.
  - 해당 태그 사이에 text를 삽입할 수 있는지 생각해보면 쉽게 판단이 가능하다.
  - 따라서 choose문 내의 when태그 사이에는 주석을 사용할 수 있다.
- ***서블릿을 사용하지 않고 순수하게 JSP만 작성할 때는 EL과 JSTL이 오히려 불편하다.***
  - EL에서 변수를 활용하기 위해선 자바코드로 작성한 변수를 ```<c:set>``` 태그를 통해 영역에 저장해줘야하는 번거로움이 있기 때문이다.
- String vs StringBuffer vs StringBuilder
  - String
    - String 클래스는 불변성을 가진다.
    ```java
    String str = "hello";
    str = str + "world";
    ```
    - 위와 같은 경우에 str이 "hello world"으로 변한건 같지만 그렇지 않다. 기존에 "hello" 값이 들어가있던 String 클래스의 참조변수 str이 "hello world"라는 값을 가지고 있는 새로운 메모리영역을 가리키도록 변경되고 처음 선언했던 "hello"로 값이 할당되어 있던 메모리 영역은  Garbage로 남아있다가 GC(garbage collection)에 의해 사라지게 된다. String 클래스는 불변하기 때문에 문자열을 수정하는 시점에 새로운 String 인스턴스가 생성된다. 위와 같이 String은 불변성을 가지기 때문에 변하지 않는 문자열을 자주 읽어들이는 경우 String을 사용하면 좋은 성능을 기대할 수 있다. 그러나 문자열 추가,수정,삭제 등의 연산이 빈번하게 발생하는 알고리즘에 String 클래스를 사용하면 힙 메모리(Heap)에 많은 임시 가비지(Garbage)가 생성되어 힙메모리가 부족으로 어플리케이션 성능에 치명적인 영향을 끼치게 된다.
  - StringBuffer와 StringBuilder의 차이
    - StringBuffer 클래스는 동기화를 지원한다.
    - STringBuilder는 동기화를 지원하지 않지만 대신 단일 쓰레드에서 더 좋은 성능을 보여준다.
- StringTokenizer 클래스
  - 문자열을 구분자(delimiter)로 나눠서 출력할 때 사용한다.
  - ```countTokens()```: 토큰의 갯수를 반환한다.
  - ```hasMoreElements()```: 꺼내올 토큰이 있으면 true를 반환한다.
  - ```nextToken()```: 해당 토큰을 꺼내온다.

- JSTL에선 0으로 나누면 에러가 발생하지 않고 INFINITY가 출력된다. Java에선 에러가 발생한다.
- 자바 코드로 1,000단위 콤마 표시하기
```java
<%
	/* 
	NumberFormat 클래스는 new로 객체를 생성할 수 없고 
	getInstance() 메소드를 통해 객체를 생성한다.
	
	NumberFormat 클래스는 싱글턴 패턴으로 제작된 클래스이므로
	단 하나의 객체만 생성할 수 있고 생성자 메소드가 private으로 선언되어있다.
	해당 클래스 내에 static 타입의 멤버 변수로 객체가 생성되어있다.
	
	메모리의 낭비를 막기 위해 메모리에 딱 하나의 객체만을 할당하기 위한 방법이
	싱글턴 패턴 디자인이다.
	*/
	NumberFormat nf = NumberFormat.getInstance();
	String commaMoney = nf.format(money);
	out.println(commaMoney);
%>
```

## 궁금증
- ```<c:set>```은 자바빈의 ```<jsp:setProperty property="*" name = "객체명">``` 처럼 ```<c:set property="*">```라고 작성해서 한번에 폼값을 받아올 순 없는 걸까?
- ${클래스명.메소드(변수 + 문자열)} 처럼 EL문내의 메소드에서 변수와 문자열을 합쳐야 할때는 어떻게 해야할까? (구글링에선 '+='를 사용하면 된다고 하긴했음)