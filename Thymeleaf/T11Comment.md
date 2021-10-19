# 주석
## 표준 HTML 주석
자바스크립트의 표준 HTML 주석은 타임리프가 렌더링하지 않고 그대로 둔다.<br>
```html
<!--
    <span th:text="${data}">html data</span>
-->
```

## Thymeleaf parser 주석
타임리프 파서 주석은 타임리프의 진짜 주석이다. **렌더링에서 주석 부분을 제거한다.**<br>
```html
<!--/* [[${data}]] */-->

<!--/*-->
    <span th:text="${data}">html data</span>
<!--*/-->
```

## Thymeleaf prototype 주석
- HTML 주석에 약간의 구문을 더한 형태를 띈다.
- HTML 파일을 웹 브라우저에서 그대로 열어보면 베이스는 HTML 주석이기 때문에 웹 브라우저가 렌더링 하지 않는다.
- 다만 **타임리프 렌더링을 거친 경우 주석 부분이 정상적으로 렌더링된다.**
- **일반 HTML 파일로 열었을 땐 보이지 않게하고, 타임리프를 렌더링 한 경우에만 출력값을 보여주는 기능이다.**<br>
```html
<!--/*/
    <span th:text="${data}">html data</span>
/*/-->
```
