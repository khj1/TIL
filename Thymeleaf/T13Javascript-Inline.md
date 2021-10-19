# 자바스크립트 인라인
타임리프는 자바스크립트에서 편리하게 사용할 수 있는 **자바스크립트 인라인**기능을 제공한다. <br><br>

## 자바스크립트 인라인 특징
### 텍스트 렌더링
- `let username = [[${user.username}]];`
  - 인라인 사용 전 = `let username = userA;`
  - 인라인 사용 후 = `let username = "userA";`
- 인라인을 사용하지 않으면 `userA`라는 결과물이 변수명으로 처리되어 오류가 발생된다.
- 인라인을 사용하면 렌더링 결과를 데이터 타입에 맞게 변환해준다.
  - 문자 타입인 경우 `"`를 포함해 준다.
  - 자바스크립트에서 문제가 될 수 있는 문자가 포함되어 있으면 **이스케이프 처리**도 자동으로 해준다.
    - 예) `"` -> `\"`

<br>

### 자바스크립트 네추럴 템플릿
타임리프는 서버 없이 HTML 파일을 직접 열어도 동작하는 네추럴 템플릿 기능을 제공한다.<br>
**자바스크립트 인라인 기능을 사용하면 주석을 활용해서 이 기능을 사용할 수 있다.**<br>

- `let username2 = /*[[${user.username}]]*/ "test username";`
  - 인라인 사용 전 = `let username2 = /*userA*/ "test username";`
  - 인라인 사용 후 = `let username2 = "userA";`
- 인라인 사용 후 결과를 보면 주석 부분이 제거되고, 기대한 `userA`가 정확하게 적용된다.

<br>

### 객체
타임리프의 자바스크립트 인라인 기능을 사용하면 객체를 JSON으로 자동 변환해준다.<br>

- `let user = [[${user}]];`
  - 인라인 사용 전 = `let user = BasicController.User(username=userA, age=10);`
  - 인라인 사용 후 = `let user = {"username":"userA","age":10};`
- 인라인 사용 전엔 객체의 `toString()`이 호출된 값이다.
- 인라인 사용 후는 객체를 JSON으로 변환해준다.

<br><br>

***
## 자바스크립트 인라인 `each`
### 인라인 `each` 예시
```javascript
<script th:inline="javascript">
    [# th:each="user, stat : ${users}"]
        let user[[${stat.count}]] = [[${user}]];
    [/]
</script>
```

<br>

### 실행 결과
```javascript
<script>
    let user1 = {"username":"userA","age":10};
    let user2 = {"username":"userB","age":20};
    let user3 = {"username":"userC","age":30};
</script>
```
