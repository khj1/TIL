# 타임리프 오류 처리 기능
**타임리프는 스프링의 `BindingResult` 를 활용해서 편리하게 검증 오류를 표현하는 기능을 제공한다.**
- [검증과 오류 메시지 공식 메뉴얼](https://www.thymeleaf.org/doc/tutorials/3.0/thymeleafspring.html#validation-anderror-messages)
## 오류 처리
### 글로벌 오류 처리
```html
<div th:if="${#fields.hasGlobalErrors()}">
<p class="field-error" th:each="err : ${#fields.globalErrors()}" th:text="${err}">
    전체 오류 메시지
</p>
</div>
```

<br>

### 필드 오류 처리
```html
<input type="text" id="itemName" th:field="*{itemName}" th:errorclass="field-error" class="form-control">
<div class="field-error" th:errors="*{itemName}">
    상품명 오류
</div>
```

<br>

### `#fields` 
`#fields` 로 `BindingResult` 가 제공하는 검증 오류에 접근할 수 있다.

<br>

### `th:errors`
해당 필드에 오류가 있는 경우에 태그를 출력한다. `th:if` 의 편의 버전이다.

<br>

### `th:errorclass`
`th:field` 에서 지정한 필드에 오류가 있으면 class 정보를 추가한다.
