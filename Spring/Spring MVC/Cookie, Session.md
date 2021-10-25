# Cookie, Session
스프링 MVC에서 로그인 기능을 구현하며 `Cookie`, `Session`에 대해 학습한다.<br>

## 로그인 처리
로그인, 로그아웃 기능을 구현해본다.<br>
### 쿠키로 로그인 처리하기
로그인 성공 시 클라이언트의 정보를 세션 쿠키에 저장한다.<br>

#### LoginController
```java
@PostMapping("/login")
public String loginV1(@Validated @ModelAttribute LoginForm form, BindingResult bindingResult, HttpServletResponse response) {
    if (bindingResult.hasErrors()) {
        return "login/loginForm";
    }

    Member loginMember = loginService.login(form.getLoginId(), form.getPassword());

    if (loginMember == null) {
        bindingResult.reject("loginFail", "아이디 또는 비밀번호가 맞지 않습니다.");
        return "login/loginForm";
    }

    // 로그인 성공 시 처리
    Cookie idCookie = new Cookie("memberId", String.valueOf(loginMember.getId()));
    response.addCookie(idCookie);

    return "redirect:/";
}
```
- 쿠키에 시간 정보를 주지 않으면 [세션 쿠키](#쿠키의-종류)가 된다.
- `id`는 `Long` 타입이므로 `String.valueOf()` 메서드를 통해 `String`으로 변환해준다.
- `response.addCookie(idCookie);` 메서드를 통해 응답 시 `Set-Cookie`를 통해 클라이언트에게 쿠키 값을 전송해준다.
- 클라이언트에서 쿠키 값이 저장되면 웹 브라우저가 종료되기 전까지 유지된다.
- 클라이언트가 요청할 때마다 회원 `id` 정보가 담긴 쿠키를 전달한다.

<br>

#### HomeController
로그인 성공 여부에 따라 메인화면으로 출력되는 페이지를 변경하도록 구현한다.<br>
```java
@GetMapping("/")
public String homeLoginV1(@CookieValue(name = "memberId", required = false) Long memberId, Model model) {

    if (memberId == null) {
        return "home";
    }

    // 로그인
    Member loginMember = memberRepository.findById(memberId);
    if (loginMember == null) {
        return "home";
    }

    model.addAttribute("member", loginMember);
    return "loginHome";
}
```
- `@CookieValue` 어노테이션을 활용하면 편리하게 쿠키를 조회할 수 있다.
- 로그인하지 않은 사용자도 메인화면에 접근할 수 있기 때문에 `required = false`를 사용한다.

<br>

#### 쿠키를 사용한 로그아웃
- 세션 쿠키는 웹 브라우저 종료 시 소멸된다.
- 서버에서 해당 쿠키의 종료 날짜를 0으로 지정해준다.
```java
@PostMapping("/logout")
public String logout(HttpServletResponse response) {
    expireCookie(response, "memberId");
    return "redirect:/";
}

private void expireCookie(HttpServletResponse response, String cookieName) {
    Cookie cookie = new Cookie(cookieName, null);
    cookie.setMaxAge(0);
    response.addCookie(cookie);
}
```

<br>

#### 쿠키 활용의 문제점
**보안 문제**
- 쿠키의 값은 임의로 변경할 수 있다.
  - 클라이언트가 쿠키를 강제로 변경하면 다른 사용자로 인식될 수 있다.
  - 실제 웹 브라우저 개발자모드 -> Application -> Cookie 변경이 가능하다
- 쿠키에 보관된 정보는 훔쳐갈 수 있다.
  - 쿠키에 중요한 데이터가 담겨있다면
  - 이 정보가 웹 브라우저에도 보관되고, 네트워크 요청마다 계속 클라이언트에서 서버로 전달된다.
- 해커카 쿠키를 한번 훔쳐가면 평생 사용할 수 있다.
  - 해커가 쿠키를 훔쳐가서 그 쿠키로 악의적인 요청을 계속 시도할 수 있다.
  
<br>

#### 해결 방안
- 쿠키에 중요한 값을 노출하지 않는다.
- 쿠키에 사용자 별로 예측 불가능한 임의의 토큰( 랜덤 값 )을 노출하고, 서버에서 토큰과 사용자 id를 매핑해서 인식한다.
- 토큰은 서버에서 관리한다.
- 토큰은 해커가 임의의 값을 넣어도 찾을 수 없도록 예상 불가능해야 한다.
- 해커가 토큰을 털어가도 시간이 지나면 사용할 수 없도록 서버에서 토큰의 만료시간을 짧게 유지한다.
- 해킹이 의심되는 경우 서버에서 해당 토큰을 강제로 제거한다.

<br>

### 세션을 직접 구현해서 로그인 처리하기
**서버에 중요한 정보를 보관**하고 예상 불가능한 임의의 **토큰을 통해 클라이언트와의 연결을 유지**하는 방법을 **세션**이라고 한다.

<br>

#### 세션 동작 방식
- 사용자가 로그인에 필요한 데이터를 전달하면 서버에서 해당 사용자가 맞는지 확인한다.
- 로그인에 성공하면 세션 ID를 생성한다.( **UUID**를 활용한다. )
- 생성된 세션 ID와 세션에 보관할 값( 클라이언트의 데이터 )을 서버의 세션 저장소에 보관한다.
- 서버는 클라이언트에 `sessionId`라는 이름으로 세션 ID만 쿠키에 담아서 전달한다.
  - ***클라이언트와 서버의 연결을 유지시켜주는 건 결국 쿠키다.***
  - **핵심은 회원과 관련된 정보는 전혀 클라이언트에 전달하지 않는 것이다.**
- 클라이언트는 쿠키 저장소에 전달된 `sessionId` 쿠키를 보관한다.
- 클라이언트는 요청 시 항상 `sessionId` 쿠키를 전달한다.
- 서버에서는 클라이언트가 전달한 `sessiodId` 쿠키 정보로 세션 저장소를 조회해서 로그인 시 보관한 세션 정보를 사용한다.
  - 즉 `sessionId`를 매핑해서 클라이언트의 연결, 상태를 유지하는 것이다.

<br>

#### 세션 직접 구현
```java
@Component
public class SessionManager {

    public static final String SESSION_COOKIE_NAME = "mySessionId";
    private Map<String, Object> sessionStore = new ConcurrentHashMap<>();

    //세션 생성
    public void createSession(Object value, HttpServletResponse response) {

        // 세션 id를 생성하고, 값을 세션에 저장
        String sessionId = UUID.randomUUID().toString();
        sessionStore.put(sessionId, value);

        // 쿠키 생성
        Cookie mySessionCookie = new Cookie(SESSION_COOKIE_NAME, sessionId);
        response.addCookie(mySessionCookie);
    }

    //세션 조회
    public Object getSession(HttpServletRequest request) {
        Cookie sessionCookie = findCookie(request, SESSION_COOKIE_NAME);
        if (sessionCookie == null) {
            return null;
        }
        return sessionStore.get(sessionCookie.getValue());
    }

    private Cookie findCookie(HttpServletRequest request, String cookieName) {
        if (request.getCookies() == null) {
            return null;
        }
        return Arrays.stream(request.getCookies())
                .filter(cookie -> cookie.getName().equals(cookieName))
                .findAny()
                .orElse(null);
    }

    //세션 만료
    public void expire(HttpServletRequest request) {
        Cookie sessionCookie = findCookie(request, SESSION_COOKIE_NAME);
        if (sessionCookie != null) {
            sessionStore.remove(sessionCookie.getValue());
        }
    }
}
```

<br>

#### 문제점
- 프로젝트마다 세션 개념을 직접 개발하는 것은 상당히 번거로운 작업이다.
- 서블릿에서 공식 지원하는 세션을 사용하면 해당 문제를 보완할 수 있다.

<br>

### 서블릿 HTTP 세션으로 로그인 처리하기
서블릿은 세션을 위해 HttpSession이라는 기능을 제공한다.<br>
HttpSession은 위에서 직접 구현한 세션과 거의 비슷하지만, 추가로 일정시간 사용하지 않으면 세션을 삭제하는 기능을 제공한다.<br>

#### LoginController
```java
@PostMapping("/login")
public String loginV3(@Validated @ModelAttribute LoginForm form, BindingResult bindingResult, HttpServletRequest request) {
    if (bindingResult.hasErrors()) {
        return "login/loginForm";
    }

    Member loginMember = loginService.login(form.getLoginId(), form.getPassword());

    if (loginMember == null) {
        bindingResult.reject("loginFail", "아이디 또는 비밀번호가 맞지 않습니다.");
        return "login/loginForm";
    }

    // 세션이 있으면 있는 세션을 반환하고 없으면 신규 세션을 생성해서 반환한다.
    HttpSession session = request.getSession();
    session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember);

    return "redirect:/";
}
```

<br>

#### 세션의 생성과 조회
- 세션 생성: `request.getSession()`
  - `request.getSession(true)`: **기본값**
    - `JSESSIONID` 쿠키 값에 매핑되는 세션 객체가 있다면 해당 세션 객체를 가져온다.
    - 없다면 새로운 세션 객체를 생성해서 반환해준다.
  - `request.getSession(false)`
    - `JSESSIONID` 쿠키 값에 매핑되는 세션 객체를 호출할 뿐 새롭게 생성하진 않는다.
    - 없다면 `null`을 반환한다.
- 세션 객체는 꼭 필요한 경우가 아니라면 새롭게 생성해주지 않는 것이 좋다.
  - 세션 객체는 요청이나 응답 객체와 다르게 만료 시점까지 유지되는 특성이 있다.
  - 무의미하게 만들어진 세션 객체가 누적되면 메모리 과부하가 생길 수 있다.
  - 따라서 세션 객체를 조회하는 경우에는 `request.getSession(false)`를 사용해야한다.

<br>

#### 세션에 클라이언트 정보 보관
`session.setAttribute(key, value);`<br>
- 하나의 세션에 여러 값을 저장할 수 있다.
- 호출하는 방법은 `.getAttribute(key)`

<br>

#### HTTP 세션을 통한 로그아웃 구현
```java
@PostMapping("/logout")
public String logoutV2(HttpServletRequest request) {
    HttpSession session = request.getSession(false);
    if (session != null) {
        session.invalidate();
    }

    return "redirect:/";
}
```
- [`session.invalidate()`](#sessioninvalidate): 세션을 제거한다.

<br>

### `@SessionAttribute` 어노테이션을 통한 세션 조회
스프링은 세션을 더 편리하게 사용할 수 있도록 `@SessionAttribute`을 지원한다.<br>

```java
@SessionAttribute(name = "loginMember", required = false) Member loginMember
```
- `Member loginMember = session.getAttribute("loginMember")`를 내장하고 있다.

<br>

### TrackingModes
로그인을 최초로 시도하면 URL이 다음과 같이 `JSESSIONID`를 포함하게 된다.<br>
```
http://localhost:8080/;jsessionid=F59911518B921DF62D09F0DF8F83F872
```
<br>

이것은 **웹 브라우저가 쿠키를 지원하지 않을 때 쿠키 대신 URL을 통해서 세션을 유지하는 방법이다.** 이 방법을 사용하려면 URL에 이 값을 계속 포함해서 전달해야 한다. (타임리프 같은 템플릿은 엔진을 통해서 링크를 걸면 jsessionid 를 URL에 자동으로 포함해준다.) **서버 입장에서 웹 브라우저가 쿠키를 지원하는지 하지 않는지 최초에는 판단하지 못하므로**, 쿠키 값도 전달하고, URL에 `JSESSIONID` 도 함께 전달한다.

<br>

**URL 전달 방식을 끄고 항상 쿠키를 통해서만 세션을 유지하고 싶을 떄**<br>
- `application.properties`
    ```
    server.servlet.session.tracking-modes=cookie
    ```

<br>

### 세션 정보 확인
```java
HttpSession session = request.getSession(false);

log.info("sessionId={}", session.getId());
log.info("getMaxInactiveInterval={}", session.getMaxInactiveInterval());
log.info("creationTime={}", new Date(session.getCreationTime()));
log.info("lastAccessedTime={}", new Date(session.getLastAccessedTime()));
log.info("isNew={}", session.isNew());
```
- `sessionId` : 세션Id, `JSESSIONID` 의 값이다. 예) 34B14F008AA3527C9F8ED620EFD7A4E1
- `maxInactiveInterval` : 세션의 유효 시간, 예) 1800초, (30분)
- `creationTime` : 세션 생성일시
- `lastAccessedTime` : 세션과 연결된 사용자가 최근에 서버에 접근한 시간, 클라이언트에서 서버로 `sessionId` ( `JSESSIONID` )를 요청한 경우에 갱신된다.
- `isNew` : 새로 생성된 세션인지, 아니면 이미 과거에 만들어졌고, 클라이언트에서 서버로 `sessionId` ( `JSESSIONID` )를 요청해서 조회된 세션인지 여부

<br>

### 세션 타임아웃 설정
세션은 사용자가 로그아웃을 직접 호출해서 `session.invalidate()` 가 호출 되는 경우에 삭제된다. *그런데 대부분의 사용자는 로그아웃을 선택하지 않고, 그냥 웹 브라우저를 종료한다.* 문제는 HTTP가 **비 연결성(ConnectionLess)**이므로 서버 입장에서는 해당 사용자가 웹 브라우저를 종료한 것인지 아닌지를 인식할 수 없다. 따라서 서버에서 세션 데이터를 언제 삭제해야 하는지 판단하기가 어렵다. 이 경우 남아있는 **세션을 무한정 보관하면 다음과 같은 문제가 발생할 수 있다.**<br>
- 비 연결성이란 클라이언트가 요청을 보내고 서버에서 이에 해당하는 응답을 보내면 서로의 TCP/IP 연결이 끊어지는 것을 의미한다.( 연결이 유지되지 않음 )

<br>

#### 세션을 무한정 보관하면 발생하는 문제점
- 세션과 관련된 쿠키를 탈취당했을 경우 오랜 시간이 지나도 해당 쿠키로 악의적인 요청을 할 수 있다.
- 세션은 기본적으로 메모리에 생성된다. 메모리의 크키가 무한하지 않기 때문에 반드시 필요한 경우에만 생성해서 사용해야한다.

<br>

#### 세션의 종료시점
세션의 종료 시점은 보통 30분 정도로 설정한다. 그런데 30분이 지나면 세션이 삭제되기 때문에 30분 마다 반복적으로 로그인을 해줘야하는 번거로움이 생긴다.<br><br>
대안은 세션 생성 시점이 아니라 사용자가 최근에 요청한 시간( `lastAccessedTime` )을 기준으로 30분 정도를 유지해주는 것이다. 이렇게하면 사용자가 서비스를 사용할 때마다 세션의 만료 시간이 새롭게 갱신될 수 있다 <br><br>
`HttpSession`은 기본적으로 이러한 방식을 지원한다. 만약 `lastAccessedTime`이후로 timeout 시간이 지나면 WAS가 내부에서 해당 세션을 제거한다.

<br>

#### 세션 타임아웃 설정
**스프링 부트로 글로벌 설정**: `application.properties`<br>
```
server.servlet.session.timeout=1800
```
- 1800초( 30분 )으로 설정했다.

<br>

### `@Login` 어노테이션 만들기
`@SessionAttribute(name = "loginMember", required = false) Member loginMember`을 간소화하기 위해 `@Login` 어노테이션을 직접 만든다.
<br>

#### `@Login`
```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface Login {
}
```
<br>

#### `LoginMemberArgumentResolver`
@Slf4j
```java
public class LoginMemberArgumentResolver implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        log.info("supportsParameter 실행");

        boolean hasLoginAnnotation = parameter.hasParameterAnnotation(Login.class);
        boolean hasMemberType = Member.class.isAssignableFrom(parameter.getParameterType());

        return hasLoginAnnotation && hasMemberType;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        log.info("resolveArgument 실행");

        HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
        HttpSession session = request.getSession(false);

        if (session == null) {
            return null;
        }

        return session.getAttribute(SessionConst.LOGIN_MEMBER);
    }
}
```

<br>

#### 활용
- 활용 이전: `@SessionAttribute(name = "loginMember", required = false) Member loginMember`
- 활용 이후: `@Login Member loginMember`

<br>

***
## 부록
### 도메인 설정
**도메인이 가장 중요하다.** 도메인은 화면, UI, 기술 인프라 등등의 영역은 제외하고 시스템이 구현해야하는 **핵심 비즈니스 업무 영역을 의미한다.** 웹과 관련된 로직( `Controller` )과 도메인 로직( `Entity`, `Repository` )은 철저히 분리하는 것이 중요하다. 따라서 도메인과 웹 관련 로직을 서로 다른 패키지로 구성하는 것이 좋다. <br>

- domain
  - item
  - member
  - login
- web
  - item
  - member
  - login

다음과 같이 패키지가 구성되었다고 했을 때, `domain`은 `web`에 의존하지 않도록 설계해야한다. 예를 들어 web 패키지를 모두 삭제해도 `domain`에는 전혀 영향이 없도록 의존 관계를 설계하는 것이 중요하다.

<br>

### 쿠키의 종류
- 영속 쿠키: 만료 날짜를 입력하면 해당 날짜까지 유지한다.
- 세션 쿠키: 만료 날짜를 생략하면 브라우저 종료시 까지만 유지된다.

<br>

### `ConcurrentHashMap`
- HashMap은 동시 요청에 안전하지 않다.
- 로그인 처리에는 동시 요청에 안전한 `ConcurrentHashMap`을 사용한다.

<br>

### JUnit에서 가상의 HTTP 요청, 응답 객체를 만들어주는 방법.
`HttpServletResponse response = new MockHttpServletResponse();`<br>
- `HttpServletResponse`는 인터페이스이므로 테스트 코드에서 직접 사용하기가 힘들다. 
- 이를 보완해주기 위해 `MockHttpServletResponse()`라는 테스트용 구현체가 존재한다.

<br>

### `getAttributeNames()`
`getAttributeNames()`는 `Enumeration` 타입을 반환한다. `Enumeration` 타입은 `asIterator().forEachRemaining()`으로 간편하게 반복시킬 수 있다.

<br>

### `session.invalidate();`
`session.invalidate();`을 사용하면 세션이 무효화된다. 세션에 저장된 모든 값이 unbound되고 세션 자체도 사라진다. 다만 클라이언트 쿠키에 남아있는 sessionId 값은 제거되지 않는다. 제거되지 않아도 서버에는 아무런 영향을 주지 않는다. 그렇기 때문에 `session.invalidate();` 호출 후 다시 `request.getSession()`을 진행하면, 세션이 없는 것으로 판단하여 새로운 세션이 생성되고 이에 따라 새로운 sessionId 값이 다시 쿠키에 갱신된다.