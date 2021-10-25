# Response

## Response View
HTTP 응답 메시지에는 단순 메시지 뿐만 아니라 정적 리소스 또는 동적 뷰 템플릿을 담아서 보낼 수 도 있다. HTTP 응답 메시지를 통해 뷰를 반환하는 방법에 대해 알아본다.

### 기존 `ModelAndView` 방식
```java
@RequestMapping("/response-view-v1")
public ModelAndView responseViewV1() {
    ModelAndView mv = new ModelAndView("response/hello");
    mv.addObject("data", "hello!");

    return mv;
}
```
1. `ModelAndView` 를 생성할 때 View의 경로를 넘겨준다.
2. `ModelAndView` 객체를 반환해준다.

### View의 경로를 반환해주는 방식
가장 많이 사용되는 방식이다.
```java
@RequestMapping("/response-view-v2")
public String responseViewV2(Model model) {
    model.addAttribute("data", "hello!");

    return "response/hello";
}
```
- **`String`으로 반환할 때** 
  - `@ResponseBody` 가 없으면 `response/hello` 로 **뷰 리졸버**가 실행되어서 뷰를 찾고, 렌더링 한다.
  - `@ResponseBody` 가 있으면 **뷰 리졸버**를 실행하지 않고, HTTP 메시지 바디에 직접 `response/hello` 라는 문자가 입력된다.

### Mapping 경로가 반환 경로와 같다면 void 메소드를 활용할 수 있다.
**권장하지 않는 방법이다.**
```java
@RequestMapping("/response/hello")
public void responseViewV3(Model model) {
    model.addAttribute("data", "hello!");
}
```
- **`void`를 반환할 떄**
  - `@Controller` 를 사용하고, `HttpServletResponse` , `OutputStream(Writer)` **같은 HTTP 메시지 바디를 처리하는 파라미터가 없으면 요청 URL을 참고해서 논리 뷰 이름으로 사용한다.**

***
## Response Body
HTTP 응답 메시지 바디에 문자열 또는 JSON을 반환하는 방법을 알아본다.
### 문자열
#### Servlet 종속적인 방법
```java
@GetMapping("/response-body-string-v1")
public void responseBodyV1(HttpServletResponse response) throws IOException {
    response.getWriter().write("ok");

}
```
#### `ResponseEntity<T>` 혹은 `HttpEntity<T>`를 활용하는 방법
```java
@GetMapping("/response-body-string-v2")
public ResponseEntity<String> responseBodyV2(){
    return new ResponseEntity<>("ok", HttpStatus.OK);
}
```

#### `@ResponseBody` 어노테이션을 활용하는 방법
**가장 많이 쓰이는 방식이다.**
```java
@ResponseBody
@GetMapping("/response-body-string-v3")
public String responseBodyV3(){
    return "ok";
}
```

### JSON
#### `ResponseEntity<T>`를 사용하는 방식
```java
@GetMapping("/response-body-json-v1")
public ResponseEntity<HelloData> responseBodyJsonV1(){
    HelloData helloData = new HelloData();
    helloData.setUsername("김한준");
    helloData.setAge(28);

    return new ResponseEntity<HelloData>(helloData, HttpStatus.OK);
}
```

#### `@ResponseBody` 어노테이션을 활용하는 방식
`@ResponseBody` 어노테이션을 활용하고 객체를 반환해주면, HTTP 메시지 컨버터에 의해 해당 객체가 JSON 형태로 반환된다.
```java
@ResponseBody
@ResponseStatus(HttpStatus.OK)
@GetMapping("/response-body-json-2")
public HelloData responseBodyJsonV2(){
    HelloData helloData = new HelloData();
    helloData.setUsername("김한준");
    helloData.setAge(28);

    return helloData;
}
```
- `@ResponseBody` 어노테이션을 사용하면 `ResponseEntity<T>`를 반환할 때 처럼 반환값에 HTTP 상태를 지정해주기 위해` @ResponseStatus` 어노테이션을 추가로 사용해줘야한다.

