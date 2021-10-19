# 타임리프 스프링 통합
타임리프는 크게 2가지 메뉴얼을 제공한다.
- [기본 메뉴얼](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html)
- [스프링 통합 메뉴얼](https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html)

타임 리프는 스프링 없이도 동작하지만, **스프링과 통합을 위한 다양한 기능을 편리하게 제공한다.**

<br>

**스프링 통합으로 추가되는 기능들**
- 스프링의 SpringEL 문법 통합
- `${@myBean.doSomething()}` 처럼 스프링 빈 호출 지원
- 편리한 폼 관리를 위한 추가 속성
  - `th:object`
  - `th:field`, `th:error`, `th:errorclass`
- 폼 컴포넌트 기능
  - `checkbox`, `radio button`, `List` 등을 편리하게 사용할 수 있는 기능 지원
- 스프링의 메시지, 국제화 기능의 편리한 통합
- 스프링의 검증, 오류 처리 통합
- 스프링의 변환 서비스 통합( Conversion Service )

<br>

## 입력 폼 처리
- `th:object`: 커맨드 객체를 지정한다.
- `*{...}`: 선택 변수 식이라고 한다. `th:object`에서 선택한 객체에 접근한다.
- `th:field`
  - HTML 태그의 `id`, `name`, `value` 속성을 자동으로 처리해준다.
  - 렌더링 전: `<input type="text" th:field="*{itemName}" />`
  - 렌더링 후: `<input type="text" id="itemName" name="itemName" th:value="*{itemName}" />`

<br>

```html
<form action="item.html" th:action th:object="${item}" method="post">
<div>
    <label for="itemName">상품명</label>
    <input type="text" id="itemName" th:field="*{itemName}" class="formcontrol"
    placeholder="이름을 입력하세요">
</div>
<div>
    <label for="price">가격</label>
    <input type="text" id="price" th:field="*{price}" class="form-control"
    placeholder="가격을 입력하세요">
</div>
<div>
    <label for="quantity">수량</label>
    <input type="text" id="quantity" th:field="*{quantity}" class="formcontrol"
    placeholder="수량을 입력하세요">
</div>
```
- `th:object="${item}`: `<form>`에서 사용할 객체를 지정한다.` *{...}`을 적용할 수 있다.
- ``th:field="*{itemName}"`
  - `${item.itemName}` 과 같다. 앞서 `th:object`로 `item`을 선택했기 때문에 선택 변수 식을 적용할 수 있다.
  - `th:field` 는 `id`, `name`, `value` 속성을 모두 자동으로 만들어준다.
    - 렌더링 후: `<input type="text" id="itemName" name="itemName" th:value="*{itemName}" />`

<br>

***
## 체크박스
체크 박스를 체크하면 HTML Form에서 `데이터=on`이라는 값이 넘어간다.<br>
스프링은 `on`이라는 문자를 `true` 타입으로 변환해준다.<br>

```
FormItemController : item.open=true //체크 박스를 선택하는 경우
FormItemController : item.open=null //체크 박스를 선택하지 않는 경우
```

HTML에서 **체크 박스를 선택하지 않고 폼을 전송하면 데이터 자체가 서버로 전송되지 않는다.** 수정의 경우에는 상황에 따라서 이 방식이 문제가 될 수 있다. 사용자가 의도적으로 체크되어 있던 값을 체크를 해제해도 저장시 아무 값도 넘어가지 않기 때문에, 서버 구현에 따라서 값이 오지 않은 것으로 판단해서 값을 변경하지 않을 수도 있다.

<br>

이런 문제를 해결하기 위해서 스프링 MVC는 **히든 필드**를 사용한다.

<br>

```html
<input type="hidden" name="_open" value="on"/>
```

<br>

히든 필드는 항상 전송된다. 따라서 체크를 해제한 경우 여기에서 open 은 전송되지 않고, _open 만
전송되는데, 이 경우 스프링 MVC는 체크를 해제했다고 판단한다.

<br>

```
FormItemController : item.open=true //체크 박스를 선택하는 경우
FormItemController : item.open=false //체크 박스를 선택하지 않는 경우
```

### 타임리프와 체크박스
타임리프의 `th:field`를 활용하면 체크 해제 시 `false` 값을 넘겨 줄 수 있는 히든 필드를 자동으로 생성해준다.

<br>

#### 렌더링 전
```html
<input type="checkbox" id="open" th:field="*{open}" class="form-checkinput">
```

<br>

#### 렌더링 후
```html
<input type="checkbox" id="open" class="form-check-input" name="open" value="true">
<input type="hidden" name="_open" value="on"/>
```

<br>

#### 타임리프의 체크 확인
`checked="checked"`
- 체크 박스에서 판매 여부를 선택해서 저장하면, 조회시에 `checked` 속성이 추가된 것을 확인할 수 있다.
- 이런 부분은 개발자가 직접 처리했어야 했다.
- 타임리프의 `th:field`를 사용하면 값이 `true`인 경우 체크를 자동으로 처리해준다.

#### 멀티 체크박스 사용 시 `id` 값 설정
`th:for="${#ids.prev('regions')}"`
- 멀티 체크박스는 같은 이름의 여러 체크박스를 만들 수 있다.
- 이렇게 반복해서 HTML 태그를 생성할 때, 생성된 HTML 태그 속성에서 `name`은 같아도 되지만, `id`는 모두 달라야한다.
- 따라서 타임리프는 체크박스를 `each` 루프 안에서 반복해서 만들 때 `id` 값에 임의로 넘버링 해준다.
- HTML의 `id`가 타임리프에 의해 동적으로 만들어지기 때문에 `<label for="id 값">`으로 `label`의 대상이 되는 `id` 값을 임의로 지정하는 것은 곤란하다.
- 타임리프는 `ids.prev(...)`, `ids.next(...)`을 제공해서 동적으로 생성되는 `id` 값을 사용할 수 있도록 한다.

***
## 부록
### `ENUM`에서 `description`을 설정하는 법
```java
public enum ItemType {
    BOOK("도서"), FOOD("식품"), ETC("기타");
    
    private final String description;
    
    ItemType(String description) {
        this.description = description;
    }
    
    public String getDescription() {
        return description;
    }
}
```
- `.name()`으로 `ENUM`의 `name`을 호출할 수 있다.( BOOK, FOOD, ETC )
- `.description`으로 `ENUM`의 `description`을 호출할 수 있다.( "도서", "식품", "기타" )

<br>

### 타임리프에서 ENUM 직접 사용하기
```html
<div th:each="type : ${T(hello.itemservice.domain.item.ItemType).values()}">
```
- 해당 `ENUM`의 모든 정보가 배열로 반환된다.
- 하지만 이렇게 사용하면 `ENUM`의 패키지의 위치가 변경될 때 컴파일 오류가 잡히지 않으므로 **권장하지 않는다.**

<br>

### `@ModelAttribute`의 특별한 사용법
컨트롤러 안에 `@ModelAttribute`가 붙은 별도의 메서드를 설정하고, 데이터를 반환해주면 해당 컨트롤러가 시작될 때 모델( `model` )에 담기게 된다.
