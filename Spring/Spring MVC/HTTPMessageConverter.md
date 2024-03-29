# HTTP Message Converter
뷰 템플릿으로 HTML을 생성해서 응답하는 것이 아니라, HTTP API처럼 **JSON 데이터를 HTTP 메시지 바디에서 직접 읽거나 쓰는 경우 HTTP 메시지 컨버터를 사용하면 편리하다.**

## `@ResponseBody` 사용 원리
[!@ResponseBody](../../img/@ResponseBody.png)
- `@ResponseBody` 를 사용
-  HTTP body에 문자 내용을 직접 반환한다.
-  `viewResolver` 대신 `HttpMessageConverter`가 동작한다.
-  기본 문자처리: `StringHttpMessageConverter`
-  기본 객체처리: `MappingJackson2HttpMessageConverter`
-  byte 처리 등등 기타 여러 `HttpMessageConverter`가 기본으로 등록되어 있음

### 스프링 MVC는 다음의 경우에 HTTP 메시지 컨버터를 적용한다.
- HTTP 요청: `@RequestBody` , `HttpEntity(RequestEntity)`
- HTTP 응답: `@ResponseBody` , `HttpEntity(ResponseEntity)`

***
## HTTP 메시지 컨버터 인터페이스
`org.springframework.http.converter.HttpMessageConverter`
```java
package org.springframework.http.converter;
public interface HttpMessageConverter<T> {
    boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);
    boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);
    List<MediaType> getSupportedMediaTypes();

    T read(Class<? extends T> clazz, HttpInputMessage inputMessage) throws IOException, HttpMessageNotReadableException;

    void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage) throws IOException, HttpMessageNotWritableException;
}
```
### HTTP 메시지 컨버터는 HTTP 요청, HTTP 응답 둘 다 사용된다.
- `canRead()` , `canWrite()` : 메시지 컨버터가 해당 클래스, 미디어타입을 지원하는지 체크
- `read()` , `write()` : 메시지 컨버터를 통해서 메시지를 읽고 쓰는 기능

***
## 스프링 부트의 기본 메시지 컨버터( 구현체 )
```java
0 = ByteArrayHttpMessageConverter
1 = StringHttpMessageConverter
2 = MappingJackson2HttpMessageConverter
```
스프링 부트는 다양한 메시지 컨버터를 제공하는데, 대상 클래스 타입과 미디어 타입 둘을 체크해서 사용여부를 결정한다. 만약 만족하지 않으면 다음 메시지 컨버터로 우선순위가 넘어간다.

### `ByteArrayHttpMessageConverter`
`byte[]` 데이터를 처리한다.
- 클래스 타입: `byte[]`
  - 읽기 미디어타입: `*/*`
  - 모든 미디어 타입을 허용한다.
- 요청 예) `@RequestBody byte[] data`
- 응답 예) `@ResponseBody return byte[]` 
  - 쓰기 미디어타입 `application/octet-stream`

### `StringHttpMessageConverter`
`String` 문자로 데이터를 처리한다.
- 클래스 타입: `String`
  - 읽기 미디어타입: `*/*`
- 요청 예)` @RequestBody String data`
- 응답 예) `@ResponseBody return "ok"` 
  - 쓰기 미디어타입 `text/plain`
```java
// content-type: application/json
@RequestMapping
void hello(@RequetsBody String data) {}
```
- 미디어 타입을 가리지 않기 때문에 정상적으로 작동한다.

### `MappingJackson2HttpMessageConverter`
`application/json`
- 클래스 타입: 객체 또는 `HashMap`
  - 읽기 미디어타입 `application/json` 관련
- 요청 예) `@RequestBody HelloData data`
- 응답 예) `@ResponseBody return helloData` 
  - 쓰기 미디어타입 `application/json` 관련
```java
//content-type: application/json
@RequestMapping
void hello(@RequetsBody HelloData data) {}
```
- JSON 관련 메시지 컨버터는 읽기 시 미디어 타입을 가리므로 에러가 발생한다.

***
## HTTP 요청 데이터를 읽는 과정
- HTTP 요청이 오고, 컨트롤러에서 `@RequestBody`, `HttpEntity` 파라미터를 사용한다.
- 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 `canRead()`를 호출한다.
  - 대상 클래스 타입을 지원하는가.
    - 예) `@RequestBody` 의 대상 클래스 ( `byte[]` , `String` , `HelloData` )
  - HTTP 요청의 `Content-Type` 미디어 타입을 지원하는가.
    - 예) `text/plain` , `application/json`, `*/*`
- `canRead()` 조건을 만족하면 `read()` 를 호출해서 객체 생성하고, 반환한다.

***
## HTTP 응답 데이터를 생성하는 과정
- 컨트롤러에서 `@ResponseBody` , `HttpEntity`로 값이 반환된다.
- 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 `canWrite()` 를 호출한다.
  - 대상 클래스 타입을 지원하는가.
    - 예) `return`의 대상 클래스 ( `byte[]` , `String` , `HelloData` )
  - HTTP 요청의 `Accept` 미디어 타입을 지원하는가.( 더 정확히는 `@RequestMapping` 의 `produces` )
    - 예) `text/plain` , `application/json` , `*/*`
- `canWrite()` 조건을 만족하면 `write()` 를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성한다.
