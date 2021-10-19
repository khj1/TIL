# 연산
타임리프의 연산은 자바와 크게 다르지 않다.<br>
HTML 안에서 사용하기 때문에 HTML 엔티티를 사용하는 부분만 주의하면 된다.

## 연산 예시
### 산술 연산
```html
<li>10 + 2 = <span th:text="10 + 2"></span></li>
<li>10 % 2 == 0 = <span th:text="10 % 2 == 0"></span></li>
```

### 비교 연산
```html
<li>1 > 10 = <span th:text="1 &gt; 10"></span></li>
<li>1 gt 10 = <span th:text="1 gt 10"></span></li>
<li>1 >= 10 = <span th:text="1 >= 10"></span></li>
<li>1 ge 10 = <span th:text="1 ge 10"></span></li>
<li>1 == 10 = <span th:text="1 == 10"></span></li>
<li>1 != 10 = <span th:text="1 != 10"></span></li>
```

### 조건식
```html
<li>(10 % 2 == 0)? '짝수':'홀수' = <span th:text="(10 % 2 == 0)? '짝수':'홀수'"></span></li>
```

### Elvis 연산자
넘겨 받은 데이터가 있으면 해당 데이터를 그대로 출력하고, 없으면 설정한 값을 출력한다.<br>
```html
<li>${data}?: '데이터가 없습니다.' = <span th:text="${data}?: '데이터가 없습니다.'"></span></li>
<li>${nullData}?: '데이터가 없습니다.' = <span th:text="${nullData}?: '데이터가 없습니다.'"></span></li>
```

### No-Operation
`_` 인 경우 마치 **타임리프가 실행되지 않는 것처럼 동작한다.**<br>
HTML의 내용을 그대로 활용할 수 있다.<br>
```html
<li>${data}?: _ = <span th:text="${data}?: _">데이터가 없습니다.</span></li>
<li>${nullData}?: _ = <span th:text="${nullData}?: _">데이터가 없습니다.</span></li>
```