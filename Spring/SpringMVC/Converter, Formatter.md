# Converter, Formatter
## Converter
### 원시적인 타입 컨버팅
```java
@GetMapping("/hello-v1")
public String helloV1(HttpServletRequest request) {
    String data = request.getParameter("data"); // 문자 타입 조회
    Integer intValue = Integer.valueOf(data); // 숫자 타입으로 변경
    
    return "ok";
}
```

<br>

***HTTP 요청 파라미터는 모두 문자로 처리된다.***

<br>

### `@RequestParam`
```java
@GetMapping("/hello-v2")
public String helloV2(@RequestParam Integer data) {
    System.out.println("data = " + data);
    return "ok";
}
```
- 쿼리 스트링 `data`는 문자 10을 전달 받지만
- `@RequestParam`의 데이터 타입을 `Integer`로 선언하면 문자 데이터를 `Integer` 타입으로 변환해준다.
- **이것은 스프링이 중간에 타입을 변환해주었기 때문이다.**
- 이러한 예는 `@ModelAttribute`, `@PathVariable` 에서도 확인할 수 있다.

<br>

### 스프링 타입 변환 적용 예
- 스프링 MVC 요청 파라미터
  - `@RequestParam`, `@ModelAttribute`, `@PathVariable`
- `@Value` 등으로 YML 정보 읽기
- `XML`에 넣은 스프링 빈 정보를 변환
- 뷰를 렌더링할 때

<br>

### 컨버터 인터페이스 활용
스프링은 확장 가능한 컨버터 인터페이스를 제공한다. 개발자는 스프링에 추가적인 타입 변환이 필요하면 이 컨버터 인터페이스를 구현해서 등록하면된다. 이 컨버터 인터페이스는 모든 타입에 적용할 수 있다. 주의할 점은 X->Y 타입으로 변환하는 컨버터 인터페이스를 구현하고, 또 Y->X 타입으로 변환하는 컨버터 인터페이스를 만들어서 등록해야한다는 것이다.<br>

```java
package org.springframework.core.convert.converter;
public interface Converter<S, T> {
    T convert(S source);
}
```
- `Converter` 라는 이름의 인터페이스가 많으므로 패키지 경로에 주의해야한다.

### 예제
#### IpPort
```java
@Getter
@EqualsAndHashCode
public class IpPort {

    private String ip;
    private int port;

    public IpPort(String ip, int port) {
        this.ip = ip;
        this.port = port;
    }
}
```
- `@EqualsAndHashCode` 를 넣으면 모든 필드를 사용해서 `equals()` , `hashcode()` 를 생성한다.
- 이로 인해 해당 객체의 모든 필드 값이 같다면 `equals()` 메소드가 참을 반환한다.

<br>

#### `IpPortToStringConverter`
```java
@Slf4j
public class IpPortToStringConverter implements Converter<IpPort, String> {

    @Override
    public String convert(IpPort source) {
        log.info("convert source={}", source);
        return source.getIp() + ":" + source.getPort();
    }
}
```
<br>

#### `StringToIpPortConverter`
```java
@Slf4j
public class StringToIpPortConverter implements Converter<String, IpPort> {

    @Override
    public IpPort convert(String source) {
        log.info("convert source={}", source);

        //"127.0.0.1:8080"
        String[] split = source.split(":");
        String ip = split[0];
        int port = Integer.parseInt(split[1]);

        return new IpPort(ip,port);
    }
}
```

<br>

#### 문제점
- 타입 컨버터를 하나하나 직접 사용하면 개발자가 직접 컨버팅하는 것과 큰 차이가 없다.
- 타입 컨버터를 등록하고 관리하면서 편리하게 변환 기능을 제공하는 역할을 하는 무언가가 필요하다.

> **참고**<br>
> 스프링은 용도에 따라 다양한 방식의 컨버터를 제공한다.
>
> `Converter`: 기본 타입 컨버터 <br>
> `ConverterFactory`: 전체 클래스 계층 구조가 필요할 때 <br>
> `GenericConverter`: 정교한 구현, 대상 필드의 애노테이션 정보 사용 가능 <br>
> `ConditionalGenericConverter`: 특정 조건이 참인 경우에만 실행 <br>
> <br>
> 자세한 내용은 [공식문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#coreconvert)를 참고하자.

<br>

### `ConversionService`
스프링은 **개별 컨버터를 모아두고 이를 묶어서 편리하게 사용할 수 있는** ConversionService를 제공한다.

<br>

#### `ConversionService` 인터페이스
```java
package org.springframework.core.convert;
import org.springframework.lang.Nullable;
public interface ConversionService {
    boolean canConvert(@Nullable Class<?> sourceType, Class<?> targetType);
    boolean canConvert(@Nullable TypeDescriptor sourceType, TypeDescriptor targetType);
    
    <T> T convert(@Nullable Object source, Class<T> targetType);
    Object convert(@Nullable Object source, @Nullable TypeDescriptor sourceType, TypeDescriptor targetType);
}
```
- 컨버팅이 가능한지 판단한 후에 컨버팅 진행

<br>

#### `ConversionService` 관련 테스트 코드
```java
@Test
void formattingConversionService() {
    DefaultFormattingConversionService conversionService = new DefaultFormattingConversionService();

    // 컨버터 등록
    conversionService.addConverter(new StringToIpPortConverter());
    conversionService.addConverter(new IpPortToStringConverter());

    // 포멧터 등록
    conversionService.addFormatter(new MyNumberFormatter());

    // 컨버터 사용
    IpPort ipPort = conversionService.convert("127.0.0.1:9090", IpPort.class);
    assertThat(ipPort).isEqualTo(new IpPort("127.0.0.1", 9090));

    String convert = conversionService.convert(1000, String.class);
    Integer convert1 = conversionService.convert("1,000", Integer.class);

    assertThat(convert).isEqualTo("1,000");
    assertThat(convert1).isEqualTo(1000);
}
```
- `DefaultFormattingConversionService`는 `ConversionService` 인터페이스를 구현했다. 추가로 컨버터를 등록하는 기능도 제공한다.

<br>

#### 등록과 사용을 분리
컨버터를 등록할 때는 StringToIntegerConverter 같은 타입 컨버터를 명확하게 알아야 한다. 반면에
컨버터를 사용하는 입장에서는 타입 컨버터를 전혀 몰라도 된다. <br>

타입 컨버터들은 모두 컨버전 서비스 내부에 숨어서 제공된다. 따라서 타입을 변환을 원하는 사용자는 컨버전 서비스 인터페이스에만 의존하면 된다. 물론 컨버전 서비스를 등록하는 부분과 사용하는 부분을 분리하고 의존관계 주입을 사용해야 한다. <br>

#### 컨버전 서비스 사용
```java
Integer value = conversionService.convert("10", Integer.class)
```

<br>

#### 인터페이스 분리 원칙 ( ISP )
**인터페이스 분리 원칙은 클라이언트가 자신이 이용하지 않는 메서드에 의존하지 않아야한다.**

<br>

`DefaultFormattingConversionService`는 다음 두 인터페이스를 구현했다.
- `ConversionService` : 컨버터 사용에 초점
- `ConverterRegistry` : 컨버터 등록에 초점

<br>

이를 통해 컨버터를 사용하는 클라이언트와 컨버터를 등록하고 관리하는 클라이언트의 관심사가 명확하게 분리할 수 있다. 컨버터를 사용하는 클라이언트는 `ConversionService`만 의존하면 된다.

<br>

[ISP 참고 문서](https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4_%EB%B6%84%EB%A6%AC_%EC%9B%90%EC%B9%99)

<br>

### `Converter` 등록
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new StringToIntegerConverter());
        registry.addConverter(new IntegerToStringConverter());
        registry.addConverter(new StringToIpPortConverter());
        registry.addConverter(new IpPortToStringConverter());
    }
}
```
- `WebMvcConfigurer`가 제공하는 `addFormatters()`를 사용한다.

<br>

#### 컨버터 처리 과정
`@RequestParam`은 `@RequestParam`을 처리하는 `ArgumentResolver`인 `RequestParamMethodArgumentResolver`에서 `ConversionService`를 사용해서 타입을 변환한다.

<br>

> **참고**<br>
> [View에 컨버터 적용하기](../../Thymeleaf/T17Converter.md)

<br>

***
## Formatter
`Converter`는 입력과 출력 타입에 제한이 없는, 범용 타입 변환 기능을 제공한다.<br>
개발자 입장에서는 범용 기능 보다는 문자를 다른 타입으로 변환하거나, 다른 타입을 문자로 변환하는 기능이 대부분이다.

<br>

### `Converter` vs `Formatter`
- `Converter`: 범용( 객체 -> 객체 )
- `Formatter`: 문자에 특화( 객체 -> 문자, 문자 -> 객체 ) + 현지화( `Locale` )

### `Formatter` 인터페이스
```java
public interface Printer<T> {
    String print(T object, Locale locale);
}

public interface Parser<T> {
    T parse(String text, Locale locale) throws ParseException;
}

public interface Formatter<T> extends Printer<T>, Parser<T> {
}
```

<br>

### `Formatter` 인터페이스 구현 예제
#### `MyNumberFormatter`
```java
public class MyNumberFormatter implements Formatter<Number> {
    @Override
    public Number parse(String text, Locale locale) throws ParseException {
        // 1,000 -> 1000
        return NumberFormat.getInstance(locale).parse(text);
    }

    @Override
    public String print(Number object, Locale locale) {
        return NumberFormat.getInstance(locale).format(object);
    }
}
```
- `Locale` 정보를 활용해서 나라별로 다른 숫자 포맷을 만들어준다.
- `parse()`를 사용해서 문자를 숫자로 변환한다.
  - ***주의*** `parse()` 의 결과가 `Long` 이기 때문에 테스트 코드 작성 시 `isEqualTo(1000L)` 을 통해 비교할 때 마지막에 `L` 을 넣어주어야 한다.
- `print()`를 사용해서 숫자를 문자로 변환한다.

<br>

> **참고**<br>
> `Formatter`: 기본 포맷터 <br>
> `AnnotationFormatterFactory`: 필드의 타입이나 애노테이션 정보를 활용할 수 있는 포맷터 <br>
>
> [공식문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#format)

<br>

### 포멧터를 지원하는 컨버전 서비스
컨버전 서비스에는 컨버터만 등록할 수 있고 포멧터는 등록할 수 없다. 포맷터를 지원하는 컨버전 서비스를 사용하면 컨버전 서비스에 포맷터를 추가할 수 있다. 내부에서 어댑터 패턴을 사용해서 `Formatter`가 `Converter`처럼 동작하도록 지원한다.

<br>

`FormattingConversionService`는 포맷터를 지원하는 컨버전 서비스이다.<br>
`DefaultFormattingConversionService`는 `FormattingConversionService`에 기본적인 통화, 숫자
관련 몇가지 기본 포맷터를 추가해서 제공한다.

<br>

#### `DefaultFormattingConversionService` 상속 관계
`FormattingConversionService`는 `ConversionService` 관련 기능을 상속받기 때문에 결과적으로
**컨버터도 포맷터도 모두 등록할 수 있다.** 그리고 사용할 때는 `ConversionService` 가 제공하는 `convert` 를 사용하면 된다.

> **참고**<br>
> 스프링 부트는 `DefaultFormattingConversionService` 를 상속 받은 `WebConversionService`
> 를 내부에서 사용한다.

<br>

### 포맷터 적용
#### `WebConfig`
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addFormatters(FormatterRegistry registry) {
    //주석처리 우선순위
    //registry.addConverter(new StringToIntegerConverter());
    //registry.addConverter(new IntegerToStringConverter());
    registry.addConverter(new StringToIpPortConverter());
    registry.addConverter(new IpPortToStringConverter());
    
    //추가
    registry.addFormatter(new MyNumberFormatter());
}
}
```
- 기존에 작성한 컨버터를 주석처리 하는 이유
  - 컨버터와 추가한 포맷터의 기능이 중복된다.( 숫자 -> 문자, 문자-> 숫자)
  - 기능이 겹치는 경우 컨버터가 포맷터보다 우선적으로 호출되고 포맷터는 적용되지 않는다.

<br>

### 스프링이 제공하는 기본 포맷터
스프링은 자바에서 기본으로 제공하는 타입들에 대해 수 많은 포맷터를 기본으로 제공한다.<br>
그런데 포맷터는 기본형식이 지정되어 있기 때문에, 객체의 각 필드마다 다른 형식으로 포맷을 지정하기가 어렵다. <br>
스프링은 이런 문제를 해결하기 위해 어노테이션 기반의 포맷터 두가 지를 기본으로 제공한다.
- `@NumberFormat` : 숫자 관련 형식 지정 포맷터 사용
- `@DateTimeFormat` : 날짜 관련 형식 지정 포맷터 사용

#### 어노테이션 기반의 포맷터 활용 예제
**포맷터 지정**
```java
@Data
static class Form {
    @NumberFormat(pattern = "###,###")
    private Integer number;

    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime localDateTime;
}
```
<br>

**포맷터 활용**
```java
@GetMapping("/formatter/edit")
public String formatterForm(Model model) {
    Form form = new Form();
    form.setNumber(10000);
    form.setLocalDateTime(LocalDateTime.now());

    model.addAttribute("form", form);
    return "formatter-form";
}

@PostMapping("/formatter/edit")
public String formatterEdit(@ModelAttribute Form form) {
    return "formatter-view";
}
```

<br>

### 주의사항
메세지 컨버터( `HttpMessageConverter`에는 컨버전 서비스가 적용되지 않는다.)
    
`HttpMessageConverter`의 역할은 HTTP 메시지 바디의 내용을 객체로 변환하거나, 객체를 HTTP 메시지 바디에 JSON 형태로 입력하는 것이다.<br>

예를 들어서 JSON을 객체로 변환하는 메시지 컨버터는 내부에서 Jackson 같은 라이브러리를 사용한다.
객체를 JSON으로 변환한다면 그 결과는 이 라이브러리에 달린 것이다. 따라서 **JSON 결과로 만들어지는 숫자나 날짜 포맷을 변경하고 싶으면 해당 라이브러리가 제공하는 설정을 통해서 포맷을 지정해야 한다.** 결과적으로 이것은 **컨버전 서비스와 전혀 관계가 없다.**<br><br>
***컨버전 서비스는 @RequestParam , @ModelAttribute , @PathVariable , 뷰 템플릿 등에서 사용할 수 있다.***
