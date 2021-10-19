# Vue - Event
## v-on
- DOM 요소에 이벤트를 적용할 때 사용하는 디렉티브
    ```javascript
    // vue에서 이벤트를 사용하는 두가지 방법
    <태그 v-on:click="함수"></태그>
    <태그 @click="함수"></태그>
    ```
***
## Prevent
- ```v-on:contextmenu.prevent="함수명"```
  - 전체 화면에서 마우스 우클릭 시 호출할 함수를 정의한다.
  - 오른쪽 마우스 클릭 방지 처리 시 활용한다.
- ```event.preventDefault()```
  - 요소 객체가 가지고있는 내장된 기능을 방지 처리한다.
    - 예를 들어 \<a> 태그의 경우 페이지 이동 처리가 되는 것을 방지한다.

    ```javascript
    // script 부분
    vue = new Vue({
        el:"#app",
        methods: {
            ctxStop(e){
                alert("마우스 우클릭 금지");
            },
            ctxGo(e){
                if(confirm("페이스북으로 이동할까요?")){
                    e.preventDefault();
                }
            }
        }
    });
    // body 부분
    <div id="app" v-on:contextmenu.prevent="ctxStop">
        <a href="https://facebook.com" @click="ctxGo">페이스북 바로가기</a>
    </div>
    ```

***
## Propagation
- ```event.stopPropagation()```
  - tag 계층 간의 이벤트 전달 및 전달 방지를 위해 사용된다.
  - 현재 이벤트가 발생된 대상 요소에만 해당 함수를 처리한다.
  - 이벤트가 발생한 요소를 포함하고 있는 상위 객체에는 이벤트가 전달되지 않게 처리한다.

> ### event 객체의 속성
- ```eventPhase```: 이벤트 흐름의 단계를 나타낸다.
- ```currentTarget```: 이벤트가 발생하고있는 HTML요소를 리턴한다
- ```target```: 이벤트가 발생한 HTML 요소를 리턴한다.

    ```javascript
    innerClick(e){
        console.error("### INNER CLICK");
        console.log("Event Phase", e.eventPhase);
        console.log("Current Target", e.currentTarget);
        console.log("Target", e.target);

        /* 
        상위에 outer 엘리먼트가 있으므로 해당 함수가 없으면
        상위요소로 이벤트 버블링이 발생된다. 아래와 같이 함수를 호출하는 경우 inner 엘리먼트에서만 이벤트가 발생된다.
        */
        e.stopPropagation();
    }
    ```

***
## 부록
> ### 이벤트에서 ```methods``` 속성의 각 함수를 호출할 때는 소괄호 생략이 가능하다.
> ### 이벤트에서는 ```methods( 함수 )```만 호출할 수 있다. ```computed```는 사용할 수 없다.
> ### event 객체를 인자로 전달할 때는 ```$event``` 형태로 전달한다.