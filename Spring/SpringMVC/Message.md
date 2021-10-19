# 메시지와 국제화
**여러 화면에서 반복적으로 사용되는 단어들을 한 곳에서 효과적으로 관리하기 위한 방법**<br>

## 메시지와 국제화란?
### 메시지
예를 들어 `messages.properteis`라는 메시지 관리용 파일을 만들고
```properties
item=상품
item.id=상품 ID
item.itemName=상품명
item.price=가격
item.quantity=수량
```
각 HTML 들은 다음과 같이 해당 데이터를 key 값으로 불러서 사용하는 것이다.<br>

**addForm.html**
```html
<label for="itemName" th:text="#{item.itemName}"></label>
```
**editForm.html**
```html
<label for="itemName" th:text="#{item.itemName}"></label>
```

<br>

#### 파라미터 사용
- 메시지: `hello.name=안녕 {0}`<br>
- 사용방법: `<p th:text="#{hello.name(${item.itemName})}"></p>`

<br>

### 국제화
**메시지에서 설정한 파일( `messages.properteis` )을 각 나라별로 관리하면 서비스를 국제화할 수 있다.**<br>

`messages_en.propertis`
```properties
item=Item
item.id=Item ID
item.itemName=Item Name
item.price=price
item.quantity=quantity
```

`messages_ko.propertis`
```properties
item=상품
item.id=상품 ID
item.itemName=상품명
item.price=가격
item.quantity=수량
```

<br>

#### 접근 지역 인식 방법 
- HTTP **`accept-language` 헤더 값**을 사용
- 사용자가 **직접 언어를 선택**하도록 하고, **쿠키** 등을 사용해서 처리한다.

***
## 스프링 메시지 소스 설정
스프링은 기본적으로 메시지 관리 기능을 제공한다.<br>

- 메시지 관리 기능을 사용하려면 스프링이 제공하는 `MessageSource` 를 스프링 빈으로 등록하면 된다.
- `MessageSource` 는 인터페이스이다. 
- 따라서 구현체인 `ResourceBundleMessageSource` 를 스프링 빈으로 등록하면 된다.

```java
@Bean
public MessageSource messageSource() {
    ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
    
    messageSource.setBasenames("messages", "errors");
    messageSource.setDefaultEncoding("utf-8");
    
    return messageSource;
}
```
- 위 예제에서는 `message`와 `errors` 파일 두 개를 메시지 소스로 지정했다.
- 스프링 부트를 사용하면 `MessageSource`를 자동으로 스프링 빈으로 등록해준다.
  - `spring.messages.basename=messages,config.i18n.messages`
  - `spring.messages.basename=messages`이 기본값이다.
  - `application.properties`에서 설정한다.

<br>

***주의! 파일명은 massage가 아니라 messages다! 마지막 s에 주의하자***

##

***
## 스프링의 국제화 메시지 선택
`MessageSource`의 메시지 기능은 `Locale` 정보를 알아야 언어를 선택할 수 있다.<br>
스프링은 언어 선택시 기본으로 `Accept-Language` 헤더의 값을 사용한다.<br>

### `LocaleResolver`
- 스프링은 `Locale` 선택 방식을 변경할 수 있도록 `LocaleResolver` 라는 인터페이스를 제공한다.
- 스프링 부트는 기본으로 `Accept-Language` 를 활용하는 `AcceptHeaderLocaleResolver` 를 사용한다.

<br>

#### `LocaleResolver` 인터페이스
```java
public interface LocaleResolver {
    Locale resolveLocale(HttpServletRequest request);
    
    void setLocale(HttpServletRequest request, 
                   @Nullable HttpServletResponse response, 
                   @Nullable Locale locale);
}
```

<br>

#### `LocaleResolver` 변경
- 만약 `Locale` 선택 방식을 변경하려면 `LocaleResolver` 의 구현체를 변경해서 쿠키나 세션 기반의
`Locale` 선택 기능을 사용할 수 있다.
- 고객이 직접 `Locale` 을 선택하도록 하는 것이다.


***
## 부록
### `assertThatThrownBy`
```java
void notFoundMessageCode() {
    assertThatThrownBy(() -> ms.getMessage("no_code", null, null))
            .isInstanceOf(NoSuchMessageException.class);
}
```
- `MessageSource`의 `getMessage()` 메서드
  - 첫번째 인자: 메시지 소스 내에 설정된 `key`값
  - 두번째 인자: 파라미터
  - 세번째 인자: 기본값
    - 메시지가 없어도 기본 메시지( `defaultMessage` )를 사용하면 기본 메시지가 반환된다.
  - 네번째 인자: `Locale`





