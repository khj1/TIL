# Request
## Request Parameter
요청 파라미터를 전달받는 방식에 대해 알아본다.
### Servlet에서 사용하던 기본적인 방식
```java
@PostMapping("/request-param-v1")
public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));

    log.info("username={}, age={}", username, age);

    response.getWriter().write("ok");
}
```

### ```@RequestParam``` 어노테이션을 사용하는 방법
#### 파라미터 이름으로 바인딩한다.
```java
@ResponseBody
@PostMapping("/request-param-v2")
public String requestParamV2(
        @RequestParam("username") String memberName,
        @RequestParam("age") int memberAge) {

    log.info("username= {}, age={}", memberName, memberAge);
    return "ok";
}
```
#### HTTP 파라미터 이름이 변수 이름과 같으면 `@RequestParam(name="xx")`을 생략할 수 있다.
```java
@ResponseBody
@PostMapping("/request-param-v3")
public String requestParamV3(
        @RequestParam String username,
        @RequestParam int age) {

    log.info("username= {}, age={}", username, age);
    return "ok";
}
```

#### 파라미터가 ```Primitive``` 타입이면 ```@RequestParam``` 도 생략 가능
```java
@ResponseBody
@PostMapping("/request-param-v4")
public String requestParamV4(String username, int age) {

    log.info("username= {}, age={}", username, age);
    return "ok";
}
```

#### ```@RequestParam.required```
```java
@ResponseBody
@PostMapping("/request-param-required")
public String requestParamRequired(
        @RequestParam(required = true) String username,
        @RequestParam(required = false) Integer age) {

    log.info("username= {}, age={}", username, age);
    return "ok";
}
```
- `true`가 기본값이다.
- `required` 속성은 `null` 값을 허용하지 않지만, 빈문자열("")은 가능하다.
- 또한 ```Primitive``` 타입은 (```String``` 제외) null값을 받을 수 없으므로 사용에 주의해야한다.

#### `defaultValue`
값이 입력되지 않았을 때 설정될 기본값을 지정할 수 있다.
```java
@ResponseBody
@PostMapping("/request-param-default")
public String requestParamDefault(
        @RequestParam(defaultValue = "guest") String username,
        @RequestParam(defaultValue = "-1") Integer age) {

    log.info("username={}, age={}", username, age);
    return "ok";
}
```
- 참고로 `defaultValue`는 파라미터가 빈 문자의 경우에도 설정된 기본값으로 변경시킨다.

#### Map, MultiValueMap
```java
@ResponseBody
@PostMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {

    log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
    return "ok";
}
```
- 단순 `Primitive` 타입의 파라미터를 여러개 받을 때는 Map으로 받을 수 있다.
- `MultiValueMap(key=[value1, value2, ...]`
  - ex) (key=userIds, value=[id1, id2])

### `@ModelAttribute`
스프링MVC는 @ModelAttribute 가 있으면 다음을 실행한다.
1. `HelloData` 객체를 생성한다.
2. 요청 파라미터의 이름으로 `HelloData` 객체의 프로퍼티를 찾는다. 
3. 해당 프로퍼티의 `setter`를 호출해서 파라미터의 값을 입력(바인딩) 한다.
4. 예를들어 파라미터 이름이 `username` 이면 `setUsername()` 메서드를 찾아서 호출하면서 값을 입력한다.
#### 기본형
```java
@ResponseBody
@RequestMapping("/model-attribute-v1")
public String modelAttributeV1(@ModelAttribute HelloData helloData) {
    log.info("helloData= {}", helloData);
    return "ok";
}
```

#### `@ModelAttribute`를 생략할 수 있다.
```java
@ResponseBody
@RequestMapping("/model-attribute-v2")
public String modelAttributeV2(HelloData helloData) {
    log.info("helloData= {}", helloData);
    return "ok";
}
```
- 다만 `@ModelAttribute`와 `@RequestParam` 모두 생략할 수 있으므로 혼란이 발생할 수 있다.

#### `@ModelAttribute`와 `@RequestParam`를 구분하는 기준
- `String`, `int`와 같은 단순 `Primitive` 타입은 `@RequestParam`을 사용한다.
- argument resolver로 지정해둔 타입을 제외한 나머지 타입들은 `@ModelAttribute`로 파라미터를 전달받는다.

***
## Request Body
HTTP 요청 메시지를 전달받는 방법에 대해 알아본다.
***주의할 점은 요청 메시지 바디를 전달받는 것과 요청 파라미터를 전달받는 것이 아무 관계 없다는 것이다.***

### 요청 메시지 바디의 단순 문자열을 전달받는 법
#### 기존 Servlet에서 사용하던 방식
```java
@PostMapping("/request-body-string-v1")
public void requestBodyStringV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
    ServletInputStream inputStream = request.getInputStream();
    String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

    log.info("messageBody={}", messageBody);

    response.getWriter().write("ok");
}
```
- `StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);`
  - **스트림**은 **바이트코드**이기 때문에 어떤 문자열 형태로 받을 것인지 반드시 명시해줘야한다.

#### `InputStream` 객체를 매개인자로 받는 방법
```java
@PostMapping("/request-body-string-v2")
public void requestBodyStringV2(InputStream inputStream, Writer responseWriter) throws IOException {
    String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

    log.info("messageBody={}", messageBody);

    responseWriter.write("ok");
}
```
- 스프링에서 제공하는 기본 argument resolver에는 `InputStream`, `Writer`이외에 `OutputStream`, `Reader`도 있다.

#### `HttpEntity<T>` 객체를 매개인자로 받는 방법
```java
@PostMapping("/request-body-string-v3")
public HttpEntity<String> requestBodyStringV3(HttpEntity<String> httpEntity) throws IOException {
    String messageBody = httpEntity.getBody();
    log.info("messageBody={}", messageBody);

    return new HttpEntity<>("ok");
}
```

#### ```@RequestBody``` 어노테이션을 사용하는 방법
**가장 많이 쓰이는 방식이다.**
```java
@ResponseBody
@PostMapping("/request-body-string-v4")
public String requestBodyStringV4(@RequestBody String messageBody) throws IOException {
    log.info("messageBody={}", messageBody);

    return"ok";
}
```