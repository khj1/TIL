# React.js - render() 메소드
## ReactDOM.render() 메소드
- HTML 코드를 웹 브라우저에 저장된 DOM 엘리먼트로 [렌더링](https://velog.io/@ru_bryunak/%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%B4%EB%9E%80) 해주는 함수를 의미한다.
    ```java
    //형식
    ReactDOM.render(HTML코드(JSX) 혹은 컴포넌트, 표시할 DOM 엘리먼트);
    ```
- 첫번째 인자로 사용되는 HTML 코드는 반드시 하나의 최상위 태그만으로 표현되어야 한다.
    ```xml
    <div></div>
    <div></div> <!-- 에러가 발생한다. (최상위 태그가 2개) -->

    <div>
        <div></div>
        <div></div>
    </div> <!-- 정상적으로 출력된다. (최상위 태그가 1개) -->
    ```