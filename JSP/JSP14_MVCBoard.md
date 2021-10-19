## 부록
- input은 type 값이 빈값이거나 잘못된 값이면 text로 자동 지정된다. ```hidden form``` 값을 확인하는 작업은 굉장히 중요하므로 디버깅 과정에 해당 내용을 사용하면 유용하다.
  - hidden 입력 상자는 화면상에 보이지 않으므로 값이 입력되었는지 확인하기 위해 소스 보기 혹은 개발자 모드를 사용해야 한다.
  - 특히 EL을 사용하는 경우에는 에러가 발생하지 않으므로 값이 있는지 반드시 확인해야 한다.

- MultipartRequest 객체가 생성되지 않는 경우
  - 폼의 ```enctype```이 ```multipart/form-data```가 아닌 경우
    - ```enctype```은 요청헤더의 ```Content-type``` 에 저장되어 전달된다.
  - 첨부파일이 있을 때 파일이 용량을 초과하는 경우 
  - 물리적 경로가 잘못 된 경우
  - ***폼값은 어떻게 전달되는 걸까?***
    - [웹의 동작 원리1(HTTP 프로토콜의 이해)](https://smartshk.tistory.com/2)
    - [웹의 동작 원리2(HTTP 프로토콜의 이해)](https://velog.io/@haileeyu21/Session-HTTP-%EC%9B%B9-%EB%8F%99%EC%9E%91%EC%9D%98-%EC%9B%90%EB%A6%AC)

- ```forward```와 ```redirect``` 비교

    글쓰기 수행을 하는 CGI(Common Gateway Interface)가 있다면, 이 CGI의 응답 페이지는 forward와 redirect 중에 redirect를 사용해야한다. 

    사용자가 실수 혹은 고의로 글쓰기 CGI 응답 페이지에서 새로 고침을 눌렀다고 가정해보자. forward의 경우 요청 정보가 그대로 살아있기 때문에 똑같은 글이 여러번 등록될 수 있다. 하지만 redirect의 경우 처음 글을 작성할 때 보냈던 요청 정보는 존재하지 않는다. (HTTP의 Stateless 속성) 또한 forward는 URL이 변하지 않지만 redirect는 URL이 변한다는 점에서 페이지 새로고침 시 어떤 변화가 있을지 생각해보면 쉽게 이해할 수 있다.

    결론적으로 시스템(session, DB)에 변화가 생기는 요청 (로그인, 회원가입, 글쓰기 등)의 경우 redirect 방식으로 응답하는 것이 바람직하며, 시스템에 변화가 생기지 않는 단순 조회 (리스트 보기, 검색 등)의 경우 forward 방식으로 응답하는 것이 바람직하다.

- Servlet의 ```service()``` 메소드
  - 서블릿의 수명 주기 메소드 중 하나이다.
  - 사용자의 요청을 받아서 get 혹은 post를 판단하고 이를 분기하는 역할을 한다. 즉, get과 post 두가지 요청을 모두 받을 수 있다.