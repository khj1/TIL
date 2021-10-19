# Spring EL
타임리프에서 변수를 사용할 때는 변수 표현식을 사용한다.
- 변수 표현식 : `${...}`

그리고 이 변수 표현식에는 스프링 EL이라는 스프링이 제공하는 표현식을 사용할 수 있다.

## Spring EL 다양한 표현식 사용
### Object
- `user.username`
- `user['username']`
- `user.getUsername()`

### List
- `users[0].username`
- `users[0]['username']`
- `users[0].getUsername()`

### Map
- `userMap['userA'].username`
- `userMap['userA']['username']`
- `userMap['userA'].getUsername()`

<br>

***
## 지역 변수 선언
- `th:with` 를 사용하면 지역 변수를 선언해서 사용할 수 있다. 
- **지역 변수는 선언한 태그 안에서만 사용할 수 있다.**
```html
<div th:with="first=${users[0]}">
    <span th:text="${first.username}"></span>
</div>
```