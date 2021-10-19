# Vue - Computed
## Computed
Vue 생성자의 함수형 속성으로 "계산 프로퍼티"라고 한다. 연산되거나 프로세스가 처리된 결과 데이터를 ```return``` 받고 화면에 함수형으로 호출해서 출력할 떄 사용한다.

- [***주의! Computed에서 메서드를 할당할 떄 화살표 함수를 사용할 수 없다.***](https://joooing.tistory.com/entry/%EC%9C%A0%EC%9A%A9%ED%95%98%EC%A7%80%EB%A7%8C-%EC%9C%84%ED%97%98%ED%95%9C-%ED%99%94%EC%82%B4%ED%91%9C%ED%95%A8%EC%88%98)
> ### 예제
- script
    ```javascript
    $(()=>{
        let vue = new Vue({
            el:"#app",
            data:{
                title: "computed 함수형 속성",
                num: 0
            },
            computed: {
                sum : function(){
                    let n = Number(this.num);
                    if(Number.isNaN(n) || n<1){
                        return 0;
                    } else {
                        return ((1 + n) * n) / 2;
                    }
                }
            }
        });
    });
    ```
- View
    ```xml
    <div style="font-size: 2em; color: brown;">
        결과 : {{sum}}
    </div>
    ```

***
## Methods
- 기존 사용법은 computed와 동일하다.
- 다만 함수 호출 시 '()'를 붙여줘야한다.
  - 이벤트로 호출 시에는 생략할 수 있다.
    ```xml
    <span>{{sum()}}</span>
    ```


***
## Watch
- ```model``` 데이터의 변경에 따른 특정한 프로세스가 필요한 경우에 사용하는 속성이다.
    ```javascript
    new Vue({
        el:"#app",
        data:{
            x:0, y:0
        },
        watch: {
            // 각 해당 모델 데이터의 변경 값이 삽입된다.
            x(param){
                // 실행부;
            }
            y(param){
                // 실행부;
            }
        }
    });
    ```

***
## ```computed``` vs ```methods``` vs ```watch```
> ### computed vs methods
- 선언과 호출 방법은 거의 동일하나 **```computed```의 경우 자신이 참조하고 있는 ```model``` 데이터가 변경될 때만 재 실행된다.**
  - 종속 데이터 변경 시에만 계산된다.
- ```computed```는 캐시된다.
- **```methods```의 경우엔 자신과 무관한 모델 데이터가 변경될 때도 재실행된다.**
- ```computed```는 인자를 전달할 수 없다.
- ```computed```는 반드시 ```return``` 값을 가져야한다.
- 즉 모든 상황에서 재 실행이 필요하다면 ```methods```를 사용하고, 참조하는 변수의 변경에만 재실행되게 하려면 ```computed```가 효율적이다.
- ```computed```는 ```methods```와 달리 **이벤트로 호출되는 상황에서 사용할 수 없다.**
