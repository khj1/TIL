# Validation
***컨트롤러의 중요한 역할 중 하나는 HTTP 요청이 정상인지 검증하는 것이다.***<br>
> **클라이언트 검증, 서버 검증**
> - 클라이언트 검증은 조작할 수 있으므로 보안에 취약하다.
> - 서버만으로 검증하면 즉각적인 고객 사용성이 부족해진다.
> - 둘을 적절히 섞어서 사용하되, 최종적으로 **서버에서의 검증은 필수적이다.**
> - API 방식을 사용하면 API 스펙을 잘 정의해서 검증 오류를 API 응답 결과에 잘 남겨줘야한다.

## Basic Validation
원초적인 방식에서 순차적으로 `Validation`의 발전과정을 살펴본다.<br>
### 1단계 : 가장 원초적인 방식
```java
@PostMapping("/add")
public String addItem(Model model, Item item, RedirectAttributes redirectAttributes) {

    //검증 오류 결과를 보관
    Map<String, String> errors = new HashMap<>();

    //검증 로직
    if (!StringUtils.hasText(item.getItemName())) {
        errors.put("itemName", "상품 이름은 필수 입니다.");
    }

    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        errors.put("price", "가격은 1,000원 ~ 1,000,000원 까지 허용합니다.");
    }

    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
        errors.put("quantity", "수량은 최대 9,999개 까지 허용합니다.");
    }

    //특정 필드가 아닌 복합 룰 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            errors.put("globalError", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값= " + resultPrice);
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    //이중 부정문은 최대한 배제하는 방향으로 설계하는 것을 권장한다.( 가독성 )
    if (!errors.isEmpty()) {
        log.info("errors= {}", errors);
        model.addAttribute("errors", errors);
        return "validation/v1/addForm";
    }

    // 성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    
    return "redirect:/validation/v1/items/{itemId}";
}
```
#### 문제점
**타입 오류 처리가 안된다.**
- 타입 오류는 스프링 MVC에서 컨트롤러에 진입하기도 전에 예외가 발생한다.
- 타입 오류가 발생하더라도 고객이 잘못 입력한 문자를 화면에 남겨주는 것이 고객 사용성에 좋다.

<br>

### 2단계 : BindingResult 사용
```java
@PostMapping("/add")
public String addItemV1(Model model, Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {

    //검증 로직
    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.addError(new FieldError("item", "itemName", item.getItemName(), false, null, null, "상품 이름은 필수 입니다"));
    }

    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        bindingResult.addError(new FieldError("item", "price", item.getPrice(), false, null, null, "가격은 1,000원 ~ 1,000,000원 까지 허용합니다."));
    }

    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
        bindingResult.addError(new FieldError("item", "quantity", item.getQuantity(), false, null, null, "수량은 최대 9,999개 까지 허용합니다."));
    }

    //특정 필드가 아닌 복합 룰 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.addError(new ObjectError("item", null, null, "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값= " + resultPrice));
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    if (bindingResult.hasErrors()) {
        log.info("bindingResult= {}", bindingResult);
        return "validation/v2/addForm";
    }

    // 성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```
<br>

#### `BindingResult`란?
- 스프링이 제공하는 **검증 오류를 보관하는 객체**이다.
- `BindingResult` 는 인터페이스이고, `Errors` 인터페이스를 상속받고 있다.
  - 실제 넘어오는 구현체는 `BeanPropertyBindingResult` 라는 것인데, 둘다 구현하고 있으므로 `BindingResult` 대신에 `Errors` 를 사용해도 된다.
  - 주로 관례상 `BindingResult` 를 많이 사용한다.
- `@ModelAttribute`에 바인딩 시 타입 오류가 발생하면?
  - `BindingResult` 가 없으면 400 오류가 발생하면서 컨트롤러가 호출되지 않고, 오류 페이지로 이동한다.
  - `BindingResult` 가 있으면 오류 정보( FieldError )를 BindingResult 에 담아서 컨트롤러를 정상 호출한다.

- **`BindingResult` 는 검증할 대상 바로 다음에 와야한다**<br>
- `BindingResult` 는 `Model`에 자동으로 포함된다.

#### `BindingResult`에 검증 오류를 적용하는 3가지 방법
- `@ModelAttribute` 의 객체에 타입 오류 등으로 바인딩이 실패하는 경우 스프링이 `FieldError` 생성해서 `BindingResult` 에 넣어준다.
  - 참고: [`BindingResult`의 타입에러 관리](#bindingresult의-타입에러-관리)
- 개발자가 직접 넣어준다.
- `Validator` 사용

<br>
  
#### `FieldError`( 필드 오류 )
**필드에 오류가 있으면 `FieldError` 객체를 생성해서 `bindingResult`에 담아두면 된다.**
```java
bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니다."));
```

<br>

**`FieldError` 생성자**
```java
//첫번째 생성자
public FieldError(String objectName, String field, String defaultMessage);
//두번째 생성자
public FieldError(String objectName, 
                  String field,
                  @Nullable Object rejectedValue, 
                  boolean bindingFailure, 
                  @Nullable String[] codes, 
                  @NullableObject[] arguments, 
                  @Nullable String defaultMessage)
```
- `objectName`: `@ModelAttribute` 이름
- `field`: 오류가 발생한 필드 이름
- `rejectedValue`: 사용자가 잘못 입력한 값( 거절된 값)
- `bindingFailure`: 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값
  - `bindingFailure` 는 타입 오류 같은 바인딩이 실패했는지 여부를 적어주면 된다. 여기서는 바인딩이
실패한 것은 아니기 때문에 false 를 사용한다.
- `codes` : 메시지 코드
- `arguments` : 메시지에서 사용하는 인자
- `defaultMessage` : 기본 오류 메시지

<br>

#### `ObejctError`( 글로벌 오류 )
**여러 필드의 값을 복합적으로 검증해야 할 경우 `ObejctError` 객체를 활용한다.**
- `ObejctError` 객체도 `FieldError` 객체와 유사하게 2가지 생성자를 제공한다.

<br>

### 3단계: 오류 코드와 메시지 처리
`FieldError` , `ObjectError` 의 생성자는 `errorCode` , `arguments` 를 제공한다. 이것은 **오류 발생시 오류 코드로 메시지를 찾기 위해 사용된다.**<br>

```java
@PostMapping("/add")
public String addItemV2(Model model, Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {

    //검증 로직
    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.addError(new FieldError("item", "itemName", item.getItemName(), false, new String[]{"required.item.itemName"}, null, null));
    }

    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        bindingResult.addError(new FieldError("item", "price", item.getPrice(), false, new String[]{"range.item.price"}, new Object[]{1000, 10000000}, null));
    }

    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
        bindingResult.addError(new FieldError("item", "quantity", item.getQuantity(), false, new String[]{"max.item.quantity"}, new Object[]{9999}, null));
    }

    //특정 필드가 아닌 복합 룰 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.addError(new ObjectError("item", new String[]{"totalPriceMin"}, new Object[]{10000, resultPrice}, null));
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    if (bindingResult.hasErrors()) {
        log.info("bindingResult= {}", bindingResult);
        return "validation/v2/addForm";
    }

    // 성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```
- 이전 방식과 다르게 `FieldError`, `ObejctError` 생성자 인자에 오류 코드 값이 전달되었다.
- 에러 메시지에 전달될 파라미터는 `new Object[]{param1, param2}`와 같은 형태로 전달된다.

#### `errors` 메시지 파일 생성
- 스프링 부트가 해당 메시지 파일을 인식할 수 있게 메시지 설정을 추가한다.

    `application.properties`
    ```properties
    spring.messages.basename=messages,errors
    ```

- `errors.properties`에 오류 메시지를 추가해준다.
    ```properties
    required.item.itemName= {0}, {1}, {2}
    required.itemName
    required.java.lang.String
    required

    # 타입 에러 처리
    typeMismatch.java.lang.Integer=숫자를 입력해주세요.
    typeMismatch=타입 오류입니다.
    ```
  - 에러 메시지 관리 또한 국제화 처리가 가능하다.

<br>

#### 문제점
- `FieldError` , `ObjectError` 는 다루기 너무 번거롭다.
- 오류 코드를 전달해주는 방식도 복잡하다. 오류 코드를 자동화 하는 방법이 필요하다.

<br>

### 4단계: `BindingResult` 가 제공하는 `rejectValue()` , `reject()` 를 사용한다.
```java
@PostMapping("/add")
public String addItemV3(Model model, Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
    //검증 로직
    if (!StringUtils.hasText(item.getItemName())) {
        bindingResult.rejectValue("itemName", "required");
    }

    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        bindingResult.rejectValue("price", "range", new Object[]{1000, 100000}, null);
    }

    if (item.getQuantity() == null || item.getQuantity() >= 9999) {
        bindingResult.rejectValue("quantity", "max", new Object[]{9999}, null);
    }

    //특정 필드가 아닌 복합 룰 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    if (bindingResult.hasErrors()) {
        return "validation/v2/addForm";
    }

    // 성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```
- 오류 메시지가 정상 출력된다. 
- 그런데 `errors.properties` 에 있는 코드를 직접 입력하지 않아도 아무런 문제가 없다. 어떤 원리일까?

<br>

#### `rejectValue()`
`BindResult` 객체는 검증을 위한 `ModelAttribute` 객체의 값을 이미 알고 있다. 따라서 `rejectValue()` 메서드를 통해 전달되는 검증 필드 값, 오류 코드 값, 파라미터 값을 적절히 조합해 에러 메시지를 생성할 수 있다.<br>
```java
bindingResult.rejectValue("price", "range", new Object[]{1000, 10000}, null)
```
```java
void rejectValue(@Nullable String field, 
                 String errorCode,
                 @Nullable Object[] errorArgs, 
                 @Nullable String defaultMessage);
```
- `field` : 오류 필드명
- `errorCode` : 오류 코드( `messageResolver`를 위한 오류 코드 )
- `errorArgs` : 오류 메시지에서 {0}와 같은 파리미터 값을 치환하기 위한 값
- `defaultMessage` : 오류 메시지를 찾을 수 없을 때 사용하는 기본 메시지

<br>

#### `reject()`
`reject()` 메서드는 글로벌 에러( 복합 에러 )처리를 위해 사용하는 메서드이다.<br>
```java
void reject(String errorCode, 
            @Nullable Object[] errorArgs, 
            @Nullable String defaultMessage);
```

<br>

#### 오류 메시지 설정
`BindingResult` 객체의 `rejectValue` 메소드에서 오류 코드에 `"required"`만 적어주면 내부적으로 스프링이 에러 코드를 `String[]{"required.item.itemName", "required" }`로 만들어준다. 이후 `required.item.itemName`를 우선적으로 호출한 뒤에 값이 없으면 `"required"`가 호출되는 방식이다. 이러한 방식을 위해 스프링에선 `MessageCodeResolver`가 사용된다.

<br>

오류 메시지를 단순하게 만들면 범용성이 좋아서 여러곳에서 사용할 수 있지만, 메시지를 세밀하게 작성하기 어렵다. 반대로 너무 자세하게 만들면 범용성이 떨어진다. **가장 좋은 방법은 범용성으로 사용하다가, 세밀하게 작성해야 하는 경우에는 세밀한 내용이 적용되도록 메시지에 단계를 두는 방법이다.**

#### `MessageCodesResolver`
- 검증 오류 코드로 메시지 코드들을 생성한다.
- `MessageCodesResolver` 인터페이스이고 `DefaultMessageCodesResolver` 는 기본 구현체이다.<br>
- 
**`FieldError`** `rejectValue("itemName", "required")` <br>
다음 4가지 오류 코드를 자동으로 생성한다.
- `required.item.itemName`
- `required.itemName`
- `required.java.lang.String`
- `required`

<br>

**`ObjectError`** `reject("totalPriceMin")` <br>
다음 2가지 오류 코드를 자동으로 생성
- `totalPriceMin.item`
- `totalPriceMin`

<br>

**정리**
1. `rejectValue()` 호출
2. `MessageCodesResolver` 를 사용해서 검증 오류 코드로 메시지 코드들을 생성
3. `new FieldError()` 를 생성하면서 메시지 코드들을 보관
4. `th:erros` 에서 메시지 코드들로 메시지를 순서대로 메시지에서 찾고, 노출

<br>

#### 문제점
- 컨트롤러에 지나치게 많은 검증 로직이 존재한다.
- 검증 관련된 로직을 분리해줄 필요가 있다.

<br>

### 5단계: `Validator` 분리
기존 컨트롤러에 작성된 검증 로직을 분리한다.<br>
#### Validator 객체 생성
```java
@Component
public class ItemValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return Item.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        Item item = (Item) target;

        //검증 로직
        if (!StringUtils.hasText(item.getItemName())) {
            errors.rejectValue("itemName", "required");
        }

        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
            errors.rejectValue("price", "range", new Object[]{1000, 100000}, null);
        }

        if (item.getQuantity() == null || item.getQuantity() >= 9999) {
            errors.rejectValue("quantity", "max", new Object[]{9999}, null);
        }

        //특정 필드가 아닌 복합 룰 검증
        if (item.getPrice() != null && item.getQuantity() != null) {
            int resultPrice = item.getPrice() * item.getQuantity();
            if (resultPrice < 10000) {
                errors.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }
    }
}
```
<br>

#### Validator 인터페이스
```java
public interface Validator {
    boolean supports(Class<?> clazz);
    void validate(Object target, Errors errors);
}
```
- `supports`: 클래스로 넘어오는 타입을 검증하는 로직
- `validate`: 검증 로직
  - `target`: 검증 재상 객체
  - `errors`: `BindingResult` 객체

<br>

#### 컨트롤러
```java
 @PostMapping("/add")
public String addItemV4(Model model, Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {

    private final ItemValidator itemValidator;

    if (itemValidator.supports(Item.class)) {
        itemValidator.validate(item, bindingResult);
    }

    //검증에 실패하면 다시 입력 폼으로
    if (bindingResult.hasErrors()) {
        return "validation/v2/addForm";
    }

    // 성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```
- `Validator` 구현체를 스프링 빈으로 주입받아서 직접 호출한다.

<br>

#### `WebDataBinder` 사용
`WebDataBinder` 는 스프링의 파라미터 바인딩의 역할을 해주고 검증 기능도 내부에 포함시켜준다. <br>
```java
@InitBinder
public void init(WebDataBinder dataBinder) {
    dataBinder.addValidators(itemValidator);
}
```
- 이렇게 `WebDataBinder` 에 검증기를 추가하면 해당 컨트롤러에서는 검증기를 자동으로 적용할 수 있다.
- `@InitBinder` 해당 컨트롤러에만 영향을 준다. 글로벌 설정은 별도로 해야한다.

<br>

### 6단계: `@Validated` 적용
#### 컨트롤러
```java
@PostMapping("/add")
public String addItemV5(Model model, @Validated Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
    //검증에 실패하면 다시 입력 폼으로
    if (bindingResult.hasErrors()) {
        return "validation/v2/addForm";
    }

    // 성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```
- `validator`를 직접 호출하는 부분이 사라지고, 대신에 검증 대상 앞에 `@Validated` 가 붙었다.

<br>

#### `@Validated`
- Spring에서 제공하는 검증 어노테이션이다.( javax의 `@Valid`와 구분 )
- 이 애노테이션이 붙으면 앞서 `WebDataBinder` 에 등록한 검증기를 찾아서 실행한다.
- 여러 검증기를 `WebDataBinder`에 등록한다면 그 중에 어떤 검증기가 실행되어야 할지 구분이 필요하다.
  - 이런 상황에서 `Validater` 인터페이스의 `supports()` 가 사용된다.

<br>

***
## Bean Validation
검증 기능을 지금처럼 **매번 코드로 작성하는 것은 상당히 번거롭다.** 특히 특정 필드에 대한 검증 로직은 대부분 빈 값인지 아닌지, 특정 크기를 넘는지 아닌지와 같이 매우 일반적인 로직이다.<br>

```java
public class Item {
    private Long id;
    
    @NotBlank
    private String itemName;
    
    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;
    
    @NotNull
    @Max(9999)
    private Integer quantity;

    //...
}
```

**이런 필드 검증 로직을 모든 프로젝트에 적용할 수 있게 공통화하고, 표준화 한것이 바로 Bean Validation 이다.**<br>
( 복합 검증 로직은 기존 방식대로 컨트롤러에 작성해주는 것이 좋다. )<br>

<br>

### Bean Validation 이란?
- Bean Validation 2.0(JSR-380)이라는 **기술 표준**이다.( 특정한 구현체가 아니다. )
- 검증 어노테이션과 여러 인터페이스의 모임이다.
  - 검증 어노테이션
    - `@NotBlank` : 빈값 + 공백만 있는 경우를 허용하지 않는다.
    - `@NotNull` : null 을 허용하지 않는다.
    - `@Range(min = 1, max = 10)` : 범위 안의 값이어야 한다.
    - `@Max(10)` : 최대 10까지만 허용한다.
    - 등등 굉장히 많은 종류가 있다.
- Bean Validation을 구현한 기술중에 일반적으로 사용하는 구현체는 하이버네이트 Validator이다. 
- 이름이 하이버네이트가 붙어서 그렇지 ORM과는 관련이 없다.

<br>

#### 하이버네이트 Validator 관련 링크
- [공식 사이트](http://hibernate.org/validator/)
- [공식 메뉴얼](https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/)
- [검증 애노테이션 모음](https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/#validator-defineconstraints-spec)

<br>

### Bean Validation 사용
Bean Validation을 사용하면 `@InitBinder`와 `WebDataBinder` 관련 로직을 제거해줘야한다.<br>
- 오류 검증기가 중복 적용되는 문제가 발생한다.

#### 컨트롤러
```java
@PostMapping("/add")
public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
        }
    }

    if (bindingResult.hasErrors()) {
        return "validation/v3/addForm";
    }
    
    //성공 로직
}
```
- 복합 검증을 위해 `@ScriptAssert` 어노테이션을 사용할 수도 있지만, 제약이 많고 복잡하다. 
- 또한 검증 기능이 해당 객체의 범위를 넘어서는 경우엔 대응하기 어렵다는 단점이 있다.
- 복합 검증은 컨트롤러에서 수행해주는 것이 좋은 방법이다.( 메서드로 설정해서 사용하는 것을 권장 )

<br>

#### BeanValidation 동작 방식
- 스프링 부트가 `spring-boot-starter-validation` 라이브러리를 넣으면 자동으로 Bean Validator를
인지하고 스프링에 통합한다.
- **스프링 부트는 자동으로 글로벌 Validator로 등록한다.**
  - `LocalValidatorFactoryBean`을 글로벌 Validator로 등록한다.
  - 이 Validator는 `@NotNull` 같은 애노테이션을 보고 검증을 수행한다. 
  - 이렇게 글로벌 `Validator`가 적용되어 있기 때문에, `@Valid`, `@Validated` 만 검증 객체에 적용하면 된다.
- Bean Validator는 바인딩에 성공한 필드에만 Bean Validation을 적용한다.

<br>

### Bean Validation 에러 코드
Bean Validation이 기본으로 제공하는 오류 메시지를 좀 더 자세히 변경하고 싶으면 어떻게 하면 될까?<br> `typeMismatch`와 비슷한 방식으로 구성된다.
#### `@NotBlank`
- `NotBlank.item.itemName`
- `NotBlank.itemName`
- `NotBlank.java.lang.String`
- `NotBlank`
<br>

#### `@Range`
- `Range.item.price`
- `Range.price`
- `Range.java.lang.Integer`
- `Range`

<br>

#### BeanValidation이 메시지를 찾는 순서
1. 생성된 메시지 코드 순서대로 `messageSource`에서 메시지를 찾는다.
2. 어노테이션의 `message` 속성을 사용한다. -> `@NotBlank(message = "공백! {0}")`
3. 라이브러리가 제공하는 기본 값을 사용한다.

<br>

### Bean Validation의 한계
데이터 처리 과정마다 검증 요구사항이 다를 수 있다.( 등록 처리, 수정 처리의 검증이 서로 다를 수 있다. ) 이런 경우 원하는 대로 검증이 이루어지지 않거나 검증 조건의 충돌이 발생하는 문제가 발생한다.<br>

**해결 방법**
- BeanValidation의 `groups` 기능을 사용한다.
- 엔티티를 직접 사용하지 않고, `ItemSaveForm`, `ItemUpdateForm` 같은 폼 전송을 위한 별도의 모델
객체를 만들어서 사용한다.

<br>

#### 방법1: Bean Validation의 `groups` 속성을 사용한다.
> **참고**<br>
> `groups` 기능은 `@Valid`에는 없고 `@Validated`에만 존재한다.

<br>

**저장용 `groups` 생성**
```java
public interface SaveCheck {}
```
**수정용 `groups` 생성**
```java
public interface UpdateCheck {}
```

<br>

**gropus 적용: 엔티티**
```java
@Data
public class Item {
    @NotNull(groups = UpdateCheck.class) //수정시에만 적용
    private Long id;

    @NotBlank(groups = {SaveCheck.class, UpdateCheck.class})
    private String itemName;

    @NotNull(groups = {SaveCheck.class, UpdateCheck.class})
    @Range(min = 1000, max = 1000000, groups = {SaveCheck.class,
    UpdateCheck.class})
    private Integer price;

    @NotNull(groups = {SaveCheck.class, UpdateCheck.class})
    @Max(value = 9999, groups = SaveCheck.class) //등록시에만 적용
    private Integer quantity;

    public Item() {
    }

    public Item(String itemName, Integer price, Integer quantity) {
        this.itemName = itemName;
        this.price = price;
        this.quantity = quantity;
    }
}
```

<br>

**groups 적용: 저장 로직**
```java
@PostMapping("/add")
public String addItemV2(@Validated(SaveCheck.class) @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
    // 실행부
}
```
```java
@PostMapping("/{itemId}/edit")
public String editV2(@PathVariable Long itemId, @Validated(UpdateCheck.class) @ModelAttribute Item item, BindingResult bindingResult) {
    // 실행부
}
```

<br>

**문제점**
`groups` 기능을 사용하면 도메인 객체와 컨트롤러 로직의 **전반적인 복잡도가 올라가는 문제가 발생한다.**<br>

<br>

#### 방법2: Form 전송 객체 분리
- 실무에서는 `groups` 를 잘 사용하지 않는다.
- 등록시 폼에서 전달하는 데이터가 도메인 객체와 딱 맞지 않기 때문이다.
  - 실무에서는 회원 등록시 회원과 관련된 데이터만 전달받는 것이 아니라, 약관 정보도 추가로 받는 등 도메인객체와 관계없는 수 많은 부가 데이터가 넘어온다.
- 그래서 보통 도메인 객체를 직접 전달받는 것이 아니라, 복잡한 폼의 데이터를 컨트롤러까지 전달할 별도의 객체를 만들어서 전달한다. 
- 예를 들면 `ItemSaveForm` 이라는 폼을 전달받는 전용 객체를 만들어서 `@ModelAttribute` 로 사용한다.
- 이후 컨트롤러에서 필요한 데이터를 사용해서 도메인 객체를 생성한다.
  - `HTML Form -> ItemSaveForm -> Controller -> Item 생성 -> Repository`

<br>

**폼 객체 분리 예시**
```java
@Data
public class ItemSaveForm {

    @NotBlank
    private String itemName;

    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull
    @Max(value = 9999)
    private Integer quantity;
}
```

```java
@Data
public class ItemUpdateForm {
    @NotNull
    private Long id;
    
    @NotBlank
    private String itemName;
    
    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;
    
    //수정에서는 수량은 자유롭게 변경할 수 있다.
    private Integer quantity;
}
```

<br>

**컨트롤러 수정**
```java
@PostMapping("/add")
public String addItem(@Validated @ModelAttribute("item") ItemSaveForm form, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
    // 실행부;
}
```
- 폼 전송 객체를 `@ModelAttribute`로 받아오도록 수정했다.
- View 부분을 수정하지 않아도 되도록 `@ModelAttribute("item")`를 통해 `Model`에 저장될 이름을 기존의 값으로 유지시켜줬다.

<br>

**이렇게 폼 데이터 전달을 위한 별도의 객체를 사용하고, 로직에 맞게 폼 객체를 나누면 검증이 완전히 분리되기 떄문에 `groups`를 적용할 일은 드물다.**

<br>

### Bean Validation - HTTP 컨버터
`@Valid` , `@Validated` 는 `HttpMessageConverter` ( `@RequestBody` )에도 적용할 수 있다.

<br>

#### 컨트롤러
```java
@RestController
@RequestMapping("/validation/api/items")
public class ValidationItemApiController {
    @PostMapping("/add")
    public Object addItem(@RequestBody @Validated ItemSaveForm form, BindingResult bindingResult){
        log.info("API 컨트롤러 호출");
        
        if (bindingResult.hasErrors()) {
            log.info("검증 오류 발생 errors={}", bindingResult);
            return bindingResult.getAllErrors();
        }
        
        log.info("성공 로직 실행");
        return form;
    }
}
```
- `return bindingResult.getAllErrors();` 는 `ObjectError` 와 `FieldError` 를 모두 반환한다.
- 실제 개발할 때는 이 객체들을 그대로 사용하지 말고, **필요한 데이터만 뽑아서 별도의 API 스펙을 정의하고 그에 맞는 객체를 만들어서 반환해야 한다.**

<br>

#### API의 경우 3가지 경우를 나누어 생각해야 한다.
- **성공 요청: 성공**
  - 결과: API 컨트롤러 호출, 성공 로직 실행
- **실패 요청( 타입 )**
  - JSON을 객체로 생성하는 것 자체를 실패함( 컨트롤러에 접근하지 못한다. )
  - 결과
    ```json
    {
        "timestamp": "2021-04-20T00:00:00.000+00:00",
        "status": 400,
        "error": "Bad Request",
        "message": "",
        "path": "/validation/api/items/add"
    }
    ```
- **검증 오류 요청**
  - JSON을 객체로 생성하는 것은 성공했고, 검증에서 실패한 케이스
  - 결과
    ```json
    [
        {
            "codes": 
            [
                "Max.itemSaveForm.quantity",
                "Max.quantity",
                "Max.java.lang.Integer",
                "Max"
            ],

            "arguments": 
            [
                {
                    "codes": [
                    "itemSaveForm.quantity",
                    "quantity"
                    ],
                    "arguments": null,
                    "defaultMessage": "quantity",
                    "code": "quantity"
                },
                9999
            ],

            "defaultMessage": "9999 이하여야 합니다",
            "objectName": "itemSaveForm",
            "field": "quantity",
            "rejectedValue": 10000,
            "bindingFailure": false,
            "code": "Max"
        }
    ]
    ```

<br>

### `@ModelAttribute` vs `@RequestBody`
#### `@ModelAttribute`
- HTTP 요청 파리미터를 처리하는 `@ModelAttribute` 는 각각의 **필드 단위로 세밀하게 적용**된다.
- 특정 필드에 타입이 맞지 않는 오류가 발생해도 나머지 필드는 정상 바인딩 되고, Validator를 사용한 검증도 적용할 수 있다.

<br>

#### `@RequestBody`
- `HttpMessageConverter` 는 `@ModelAttribute` 와 다르게 각각의 필드 단위로 적용되는 것이 아니라, **전체 객체 단위로 적용된다.**
- 따라서 메시지 컨버터의 작동이 성공해서 Item( 도메인 객체 ) 객체를 만들어야 `@Valid` , `@Validated` 가 적용된다.
- `@RequestBody` 는 `HttpMessageConverter` 단계에서 JSON 데이터를 객체로 변경하지 못하면 이후
단계 자체가 진행되지 않고 예외가 발생한다. 컨트롤러도 호출되지 않고, Validator도 적용할 수 없다.

<br>

***
## 부록
### `BindingResult`의 타입에러 관리
`BindingResult` 객체는 데이터가 넘어오기 전에 타입관련 에러가 생기면, `request.getParameter`로 넘어온 값을 데이터 객체에 저장할 수 없으므로 `rejected value`를 `FieldError` 객체에 저장해준다. 컨트롤러의 해당 요청 메서드가 실행되기 전에 `BindingResult`가 타입 에러와 관련된 사항들을 미리 `Field Error`에 저장해놓은 후 요청 메서드가 실행되는 방식이다. 타입 에러 관련 처리를 스프링에서 미리 자동으로 해준 뒤에 개발자가 `addError` 메서드를 통해 에러 사항을 추가해주는 방식으로 이루어지는 것이다.

<br>

### 엔티티의 ID값 검증
보통 id 값은 굉장히 중요한 식별자이므로 Null이 될 수 없도록 로직이 구성되어 있다. 그래서 검증하지 않아도 된다고 생각할 수 있다. 그런데 HTTP 요청은 언제든지 악의적으로 변경해서 요청할 수 있으므로 서버에서 항상 검증해야 한다. 예를 들어서 HTTP 요청을 변경해서 해당 엔티티의 id 값을 삭제하고 요청할 수도 있다. 따라서 **최종 검증은 서버에서 진행하는 것이 안전한다.**



