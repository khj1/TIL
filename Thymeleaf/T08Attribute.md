# 속성 값 설정
## 타임 리프 태그 속성( Attribute )
타임리프는 주로 HTML 태그에 `th:*` 속성을 지정하는 방식으로 동작한다. <br>
`th:*` 속성을 적용하면 기존 속성을 대체한다. 기존 속성이 없으면 새로 만든다.<br>

### 속성 설정
- `<input type="text" name="mock" th:name="userA" />`
  - 타임리프 렌더링 후 `<input type="text" name="userA" />`

### 속성 추가
#### `th:attrappend`
속성 값의 뒤에 값을 추가한다.
- `<input type="text" class="text" th:attrappend="class=' large'" /><br/>`
  - 렌더링 결과: `<input type="text" class="text large" /><br/>`

#### `th:attrprepend`
속성 값의 앞에 값을 추가한다.
- `<input type="text" class="text" th:attrprepend="class='large '" /><br/>`
  - 렌더링 결과: `<input type="text" class="large text" /><br/>`

#### `th:classappend`
class 속성에 자연스럽게 추가한다.
- `<input type="text" class="text" th:classappend="large" /><br/>`
  - 렌더링 결과: `<input type="text" class="text large" /><br/>`

### `checked` 처리
- HTML 에서는 `<input type="checkbox" name="active" checked="false" />` 이 경우 에도 `checked` 속성이 있기 때문에 `checked` 처리가 되어버린다.
- 이런 부분이 `true`, `false` 값을 주로 사용하는 개발자 입장에서는 불편하다.
- 타임리프의 `th:checked`는 값이 `false`인 경우 `checked`속성 자체를 제거한다.
  - `<input type="checkbox" name="active" th:checked="false" />`
    - 렌더링 후: `<input type="checkbox" name="active" />`