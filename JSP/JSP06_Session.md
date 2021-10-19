# 세션 (Session)
## 세션이란?
클라이언트가 서버에 접속해있는 상태를 말하는 것으로 방문자가 웹 브라우저를 열어 서버에 접속하고, 웹 브라우저를 닫아 서버와의 연결을 종료하는 하나의 단위를 세션이라 한다. 즉 접속한 상태의 유지기간을 의미한다.
  - session 객체는 웹 브라우저당 1개의 개체가 할당되어 사용자 인증에 관련된 작업을 수행 시 주로 사용된다.
  - session 객체는 일정 시간동안 아무런 요청도 하지 않으면 자동적으로 삭제된다. (기본: 30분)
  - 세션 설정 시 내부적으로 JSESSIONID라는 쿠키명으로 세션 아이디 값이 쿠키 값으로 설정된다.

## 주요 메소드
- ```String getId()```
- ```long getCreationTime() ```
  - 세션이 생성된 시간을 1970년 1월 1일 이후 흘러온 시간으로 반환한다. (1/1000초 단위)
- ```long getLastAccessedTime()```
- ```void setMaxInactiveInterval(time)```
  - 세션 유지 시간을 내장 객체로 설정할 땐 '초' 단위를 사용한다.
  - 세션 유지시간은 web.xml에서 ```<session-config>``` 하위의 ```<session-timeout>```으로 설정해주면된다. 
    ```jsp
    <session-config>
    <session-timeout>20</session-timeout>
    </session-config>
    ```
  - 배포 서술자에서 세션 유지 시간을 설정할 때는 '분'단위로 설정해준다. 
  - web.xml에서 세션 유지시간을 변경했다면 세션을 종료했다가 다시 열어줘야 변경값으로 정상 수정된다.
- ```session.setAttribute(String key, Object value)```
  - 세션 개체에 값 저장 시 사용하는 메소드
- ```Object value=session.getAttribute(String key);```
  - 세션 개체에서 값을 읽어올 때
- ```session.removeAttribute(속성명);```
  - 세션 개체의 특정 속성을 삭제할 때 사용
- ```void invalidate()```
  - 세션의 기본개체가 삭제됨. 즉 할당된 세션의 속성값을 모두 제거한다. 주로 세션을 종료할 때 사용한다.
- ```Enumeration<String> sess=session.getAttributeNames();```
  - 세션 개체에 저장된 모든 속성 이름을 읽어오기


## 부록
- 세션과 쿠키 비교
  - 세션과 쿠키는 모두 사용자의 상태 정보를 저장하는 기능을 수행한다.
  - 세션 정보는 웹 서버에 저장되기 때문에 쿠키보다 보안성이 높다. 쿠키는 사용자의 로컬PC에 저장되기 때문에 임의로 조작될 가능성이 있다. 따라서 세션은 각종 인증과 같은 중요한 데이터를 저장하는 용도로 사용한다. 반면 쿠키는 팝업창, 아이디 저장과 같은 다소 보안이 중요하지 않은 작업에 많이 사용된다.

- request.getParameter("name"); VS request.getAttribute("name");
  - String name1 = request.getParameter("name"); 가능
  - String name2 = request.getAttribute("name"); 불가능
  - getParameter는 도메인 뒤에 get방식의 쿼리스트링으로 전달되는 값을 받아온다. (예를 들어 폼값을 전송할 때!)
  - getAttribute은 기본적으로 attribute(속성)들은 Object 타입으로 저장이 되기 떄문에 형변환이 반드시 이뤄져야한다. attribute을 설정할 때는 setAttribute(key, value)로 설정하고 오브젝트 타입으로 저장한다.
  - jsp의 4가지 영역에 모두 동일한 key값의 4가지 attribute을 저장한다고 해도 영역별로 구분되어 저장되기 때문에 문제되지 않는다.