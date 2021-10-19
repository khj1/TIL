# 리터럴
- 리터럴은 소스 코드 상에 고정된 값을 말하는 용어이다.
- 예를 들어 다음 코드에서 `"Hello"`는 **문자 리터럴**, 10, 20은 **숫자 리터럴**이다.
    ```java
    String a = "Hello"
    int a = 10 * 20
    ```
## 타임 리프의 리터럴
- 문자: `'hello'`
- 숫자: `10`
- Boolean: `true`, `false`
- null: `null`

<br>

***타임리프에서 문자 리터럴은 항상 '( 작은 따옴표 )로 감싸야 한다.***
- `<span th:text="'hello'">`

하지만 모든 문자를 항상 `'`로 감싸는 것은 번거로운 일이다. <br>
**문자열이 공백 없이 쭉 이어진다면 하나의 의미있는 토큰으로 인지해서 작은 따옴표를 생략할 수 있다.**
- `<span th:text="hello">`

### 주의 사항
문자 리터럴은 원칙상 `'` 로 감싸야 한다. **중간에 공백이 있으면 하나의 의미있는 토큰으로 인식되지 않는다.**
- `<span th:text="hello world!"></span>` : 오류 발생
- `<span th:text="'hello world!'"></span>` : 정상 동작

### 리터럴 대체( Literal Substitutions )
- `<span th:text="|hello ${data}|">`
    ```html
    <li>'hello' + ' world!' = <span th:text="'hello' + ' world!'"></span></li>
    <li>'hello world!' = <span th:text="'hello world!'"></span></li>
    <li>'hello ' + ${data} = <span th:text="'hello ' + ${data}"></span></li>
    <li>리터럴 대체 |hello ${data}| = <span th:text="|hello ${data}|"></span></li>
    ```

