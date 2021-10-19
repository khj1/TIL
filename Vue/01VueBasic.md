# Vue - basic
- **Vue**는 **MVVM** 패턴의 방식을 사용한다.
  - Model
    - Vue에서 데이터 처리를 담당한다.
  - View Model
    - Model과 View의 중재자 역할을 한다.
    - Vue 객체를 인스턴스화 하여 처리하는 부분이다.
  - View
    - body 하위나 component 화면에 대한 대상을 지정한다.
    - 주로 selector( ```id```, ```class```, ```tag``` )로 선언한다.

> ### CDN 설정
- jQuery와 Vue를 CDN으로 처리했다.
```xml
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

## script 부분
- Vue의 기본 형식
  - ```new Vue({ 속성 : 속성값, ... })```
    - 속성
      - ```el``` : View에 대한 선택자 선언
      - ```data``` : Model에 대한 선언
```javascript
$(()=>{
    // 모델 데이터를 JSON 객체로 선언
    let model = {
        title : "처음 만나는 Vue.js",
        name : "김한준",
        age : "28",
        addr : "경기도 안양시"
    };

    let VM = new Vue({
        el: "#app",
        data : model
    });
});
```

***
## View
- Mustach( 콧수염 괄호 )
  - 뷰 인스턴스의 데이터를 HTML 태그에 연결하는 가장 기본적인 방식이다.
  - 뷰 인스턴스에 ```data``` 속성으로 지정된 key 값들을 콧수염 괄호로 호출할 수 있다.
```xml
<div id="app"> <!-- Vue의 el 속성으로 설정된 View -->
    <h2>{{title}}</h2>
    <table>
        <tr>
            <td>이름</td>
            <td>{{name}}</td>
        </tr>
        <tr>
            <td>나이</td>
            <td>{{age}}</td>
        </tr>
        <tr>
            <td>주소</td>
            <td>{{addr}}</td>
        </tr>
    </table>  
</div>
```
