# 블록
`<th:block>`은 HTML 태그가 아닌 타임리프의 유일한 자체 태그이다.<br>

```html
<th:block th:each="user : ${users}">
    <div>
        사용자 이름1 <span th:text="${user.username}"></span>
        사용자 나이1 <span th:text="${user.age}"></span>
    </div>
    <div>
        요약 <span th:text="${user.username} + ' / ' + ${user.age}"></span>
    </div>
</th:block>
```
- 여러 태그를 묶어서 반복해주거나 다른 기능을 사용하고 싶을 때 유용하다. <br><br>

**실행 결과**
```html
<div>
    사용자 이름1 <span>userA</span>
    사용자 나이1 <span>10</span>
</div>
<div>
    요약 <span>userA / 10</span>
</div>
<div>
    사용자 이름1 <span>userB</span>
    사용자 나이1 <span>20</span>
</div>
<div>
    요약 <span>userB / 20</span>
</div>
<div>
    사용자 이름1 <span>userC</span>
    사용자 나이1 <span>30</span>
</div>
<div>
    요약 <span>userC / 30</span>
</div>
```
