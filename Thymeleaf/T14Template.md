# 템플릿
- 웹 페이지를 개발할 때는 공통 영역이 많이 있다.
- 예를 들어 상단 영역이나 하단 영역, 좌측 카테고리 등등 여러 페이지에서 함께 사용하는 영역들이 있다.
- 타임리프는 반복적으로 필요한 영역을 간편하게 사용할 수 있게끔 템플릿 조각( `fragment` )과 레이아웃 기능을 지원한다.

<br><br>

## fragment
### 템플릿 페이지
```html
<body>
<footer th:fragment="copy">
    푸터 자리 입니다.
</footer>

<footer th:fragment="copyParam (param1, param2)">
    <p>파라미터 자리 입니다.</p>
    <p th:text="${param1}"></p>
    <p th:text="${param2}"></p>
</footer>
</body>
```
- `th:fragment`가 있는 태그는 다른곳에 포함되는 코드 조각 역할을 한다.
  - `th:fragment="fragment name"`

<br>

### 메인 페이지
- `~{}` 태그 안에 템플릿 조각이 있는 페이지의 경로를 입력해준다.
- `::` 뒤에 해당 페이지 내에서 사용하고 싶은 템플릿 조각의 이름을 적어준다.
#### `insert`( 부분 포함 )
`th:insert`가 사용된 태그 하위에 삽입된다.
```html
<div th:insert="~{template/fragment/footer :: copy}"></div>
```
#### `replace`( 부분 포함 )
`th:replace`가 사용된 태그를 템플릿 조각이 대체한다.
```html
<div th:replace="~{template/fragment/footer :: copy}"></div>
```
#### 단순 표현식( 부분 포함 )
다음과 같이 `~{}` 형식을 사용하지 않고 단순하게 사용할 수 있다.
```html
<div th:replace="template/fragment/footer :: copy"></div>
```
#### 파라미터 사용
```html
<div th:replace="~{template/fragment/footer :: copyParam ('데이터1', '데이터
2')}"></div>
```

***
## layout
개념을 좀 더 확장해서 코드 조각을 레이아웃에 넘겨서 사용하는 방법이다.<br>

예를 들어 `<head>`에 공통으로 사용하는 `css`, `javascript` 같은 정보들이 있는데 이러한 **공통 정보들을 한 곳에 모아두고**, 공통으로 사용하지만 각 **페이지마다 필요한 정보를 더 추가해서 사용하고 싶다면** 다음과 같이 사용하면 된다.
<br><br>

### 템플릿 페이지
```html
<head th:fragment="common_header(title,links)">
    <title th:replace="${title}">레이아웃 타이틀</title>
    
    <!-- 공통 -->
    <link rel="stylesheet" type="text/css" media="all" th:href="@{/css/awesomeapp.css}">
    <link rel="shortcut icon" th:href="@{/images/favicon.ico}">
    <script type="text/javascript" th:src="@{/sh/scripts/codebase.js}"></script>
    
    <!-- 추가 -->
    <th:block th:replace="${links}" />
</head>
```
<br><br>

### 메인 페이지
```html
<head th:replace="template/layout/base :: common_header(~{::title},~{::link})">
    <title>메인 타이틀</title>
    <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
    <link rel="stylesheet" th:href="@{/themes/smoothness/jquery-ui.css}">
</head>
```
- `common_header(~{::title},~{::link})` **이 부분이 핵심이다.**
  - `::title` 은 현재 페이지의 title 태그들을 전달한다.
  - `::link` 는 현재 페이지의 link 태그들을 전달한다.

<br><br>

**`layout` 방식은 `head` 태그 뿐만 아니라 `html` 태그에도 적용할 수 있다.**


