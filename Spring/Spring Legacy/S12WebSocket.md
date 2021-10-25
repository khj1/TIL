# Spring - WebSocket
**웹소켓( WebSocket )**은 하나의 TCP 접속에 전이중 통신 채널을 제공하는 컴퓨터 통신 프로토콜이다. 

**웹소켓은 HTTP와 구별된다.** 두 프로토콜 모두 OSI 모델의 제7계층에 위치해 있으며 제4계층의 TCP에 의존한다. 

## 설정( Spring Legacy )
> ### pom.xml
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-websocket</artifactId>
    <version>${org.springframework-version}</version>
</dependency>        
<dependency>
    <groupId>javax.websocket</groupId>
    <artifactId>javax.websocket-api</artifactId>
    <version>1.1</version>
</dependency>        
</dependencies>
```
> ### web.xml( servlet 부분에 추가 )
```xml
<!-- 웹 소켓을 위한 설정( 비동기 지원 ) -->
<async-supported>true</async-supported>
```

***
## 알림기능 구현
> ### 자바스크립트
- HTML 문서가 로드되었을 때 Notification을 통해 권한을 확인한다.
  ```java
  window.onload=function(){
    if(window.Notification){
      // 권한 요청 알림창을 띄워준다.
      Notification.requestPermission();
    }
    else{
      alert("웹 노티를 지원하지 않습니다.");
    }
  }
  ```
- 알림 기능 관련 메서드 구현
  ```java
  function notify() {
		// 실행 전 권한 확인
		if(Notification.permission != "granted"){
			alert("웹 노티를 지원하지 않습니다.");
		}
		else{
			// Notification 객체를 생성하여 제목, 내용, 아이콘 설정
			var notification = new Notification(
				'제목 입니다.',
				{
					icon: '아이콘 URL',
					body: "여긴 내용입니다."
				}
			);
			// 알림창을 클릭했을 때 이동할 URL을 이벤트 핸들러에 등록한다.
			notification.onclick = function() {
				window.open("경로");
			}
		}
	}
  ```

***
## 채팅기능 구현
> ### 컨트롤러( 서버 )
```java
@Controller
@ServerEndpoint("요청명")
public class WebSocketChat {
    // 클라이언트가 접속할 때 마다 세션 ID를 저장하는 용도
    private static final List<Session> sessionList = new ArrayList<Session>();

    // 로그를 남기기위한 객체
    private static final Logger logger = LoggerFactory.getLogger(WebSocketChat.class);

    // 클라이언트가 접속했을 때
    @OnOpen
    public void onOpen(Session session) { 실행부; }

    // 클라이언트가 보낸 메시지가 도착했을 때
    @OnMessage
    public void onMessage(String message, Session session) { 실행부; }

    // 채팅 중 오류가 발생했을 때 호출
	  @OnError
  	public void OnError(Throwable e, Session session) { 실행부; }

    // 클라이언트가 접속을 종료했을 때 호출
	  @OnClose
	  public void onClose(Session session) { 실행부; }
}
```
- ```Session```은 우리가 기존에 알던 ```HttpSession```과 구별된다.
  - ```Session```은 웹소켓의 클라이언트를 구분하기위한 객체이다.
- ```Basic basic = session.getBasicRemote()``` : 서버에서 클라이언트로 메시지를 전송하기 위한 객체
  - ```basic.sendText(메시지)``` : 서버에서 클라이언트로 메시지를 보내기 위한 메서드

> ### 클라이언트( 자바스크립트로 구현 )
```java
let webSocket;

// 웹소켓 연결
webSocket = new WebSocket( "ws://" + @ServerEndpoint에 지정된 요청명을 포함한 URI );

// 웹소켓 관련 메서드( 서버와 비슷하다 )
webSocket.onopen = (event) => { wsOpen(event) };
webSocket.onmessage = (event) => { wsMessage(event) };
webSocket.onclose = (event) => { wsClose(event) };
webSocket.onerror = (event) => { wsError(event) };

// 대화내용이 리스트업 될 때 스크롤바를 자동으로 내려가게 해준다.
메시지박스.scrollTop = 메시지박스.scrollHeight;
```