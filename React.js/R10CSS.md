# React.js - CSS
JSX에서 Javascript 표현식은 중괄호 안에 쓰여지고, JSON 객체도 중괄호를 사용한다. 따라서 스타일을 지정하기 위해서 중괄호 두 세트 ```{{}}```로 작성한다.

## 인라인 스타일
```java
render() {
    return  (
        <div>
            <h4 style = {{color : "red"}}>Hello Style</h4>
        </div>
    );
}
```

## JSON을 사용하는 방법
```java
render() {
    // 2개 이상의 속성을 지정할 때 JSON 객체를 사용하면 된다.
    const myStyle = {
        color : "white",
        backgroundColor : "DodgerBlue",
        padding : "10px",
        fontFamily : "Verdana"
    };
    // JSON으로 설정한 스타일을 표현식을 통해 적용한다.
    return  (
        <div>
            <h4 style = {myStyle}>JSON으로 여러 속성 한번에 지정하기</h4>
        </div>
    );
}
```

## 외부 CSS를 사용하는 방법
```java
//우선 외부 CSS 파일을 link 태그를 통해 적용한다.
<link rel="stylesheet" href="./css/my_style.css">

// 이후 HTML에서와 동일한 방법으로 적용시키면 된다.
render() {
    return  (
        <div>
            <h4 id="my_style">외부 CSS 파일 사용</h4>
        </div>
    );
}
```