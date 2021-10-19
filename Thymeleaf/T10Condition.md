# 조건부 평가
## `if`, `unless`
- 타임리프는 해당 조건이 맞지 않으면 태그 자체를 렌더링하지 않는다.
- `<span th:text="'미성년자'" th:if="${user.age < 20}"></span>`
  - 조건이 `false`인 경우 `<span>` 부분 자체가 렌더링 되지 않고 사라진다.
- `<span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>`

## `switch`
```html
<td th:switch="${user.age}">
    <span th:case="10">10살</span>
    <span th:case="20">20살</span>
    <span th:case="*">기타</span>
</td>
```
- `*`은 만족하는 조건이 없을 때 사용하는 디폴트이다.