# React.js - Event
## React에서 이벤트 핸들러 사용 시 주의 사항
- 이벤트 핸들러는 반드시 Camel Case 형식으로 기술해야한다.
  - ```onclick``` : 에러 발생
  - ```onClick``` : 정상 작동
- DOM 요소에만 이벤트를 지정할 수 있다.

## 이벤트 사용방법
- 렌더링 시점에 함수를 만들어 전달하는 방식
- 함수를 미리 만들어 놓고 호출하는 방식
- 기능적 차이는 없으나 두번째 방식이 가독성도 높고 유지보수도 편리하다.
    ```java
    <div id="myEvent1"></div>
    <script type="text/babel">
        function myAlert(){
            alert("click 이벤트가 발생되었습니다.");
        }
        const myElement = (
            <div>
                <button onClick={function(){console.log("click 이벤트가 발생됨")}}>
                    Click 이벤트1 - console
                </button>    

                <button onClick={myAlert}>
                    Click 이벤트2 - myAlert()
                </button>
            </div>
        );
        ReactDOM.render(myElement, document.getElementById("myEvent1"));
    </script>
    ```
## React에서 this의 활용
- ES6의 **화살표 함수**에서 사용되는 ```this```는 호출되는 대상에 관계없이 항상 정의된 객체를 가리킨다.
- 하지만 JavaScript의 일반적인 함수는 ```this```가 해당 함수를 호출하는 DOM 객체를 가리킨다.
- 일반적인 함수에서 ```this```가 정의된 객체를 의미하게하려면 ```bind()``` 메소드를 사용해야 한다.
    ```java
    <div id="myEvent3"></div>
    <script type="text/babel">
        class ThisComp2 extends React.Component {
            constructor(props){
                super(props);
                //생성자에서 this가 해당 객체를 가리키도록 바인딩 한다.
                //기존의 함수에서 this는 호출한 DOM객체를 의미한다.
                //this가 bind() 메소드의 매개변수로 전달된 객체를 가리키도록 만든다.
                this.thisFunc = this.thisFunc.bind(this);
            }
            thisFunc(){
                alert("ThisComp2 나는 누구? " + this);
            }
            render() {
                return (
                    <button onClick={this.thisFunc}>
                        this는 누구?
                    </button>   
                );
            }
        }
        ReactDOM.render(<ThisComp2/>, document.getElementById("myEvent3"));
    </script>
    ```

## 이벤트 핸들러내에 함수가 매개변수를 받아야할 때
- 화살표 함수
  - 화살표 함수 원형을 그대로 기술해야한다.
    ```java
    <div id="myDiv4"></div>
    <script type="text/babel">
        class Baseball extends React.Component {
            hitBall = (str) => {
                alert(str);
            }
            
            render() {
                return (
                    <button onClick={() => this.hitBall("홈런!")}>
                        매개변수 전달
                    </button>
                );
            }
        }
        ReactDOM.render(<Baseball/>, document.getElementById("myDiv4"));
    </script>
    ```
- 일반 함수
  - this(해당객체)와 함수를 바인딩한 후 호출해야한다.
  - 단, 이때 ```bind()``` 함수의 첫번째 파라미터는 ```this```가 되고
  - 두번째 파라미터를 통해 전달하고자 하는 값을 기술한다.
    ```java
    <div id="myDiv5"></div>
    <script type="text/babel">
        class Baseball extends React.Component {
            hitBall = function(str){
                alert(str);
            }
            
            render() {
                return (
                    <button onClick = {this.hitBall.bind(this, "홈런!")}>
                        매개변수 전달
                    </button>
                );
            }
        }
        ReactDOM.render(<Baseball/>, document.getElementById("myDiv5"));
    </script>
    ```

## 이벤트 객체를 전달해야 할 때
- 화살표 함수
  - 전달되는 이벤트를 매개변수로 받아 type을 출력하면 해당 이벤트 명이 출력된다.
    ```java
    // 다음과 같이 이벤트를 매개변수로 받아서 해당 타입을 호출한다.
    sendEvent = (msg, evt) => {
        console.log("화살표 함수", msg, evt.type);
    }

    // 이를 위해 이벤트 핸들러 내에서 event 객체를 인수로 직접 기술해줘야 한다.
    render() {
        return (
            <div>
                <button onClick = {(event) => 
                    this.sendEvent("onClick 이벤트", event)}>
                    눌러보세요1
                </button>
            </div>
        );
    }
    ```
- 일반 함수
  - 일반 함수 사용 시 이벤트 객체를 전달할 때 ```this``` 사용을 위해 함수와 객체를 바인딩하면 ```bind()``` 함수의 마지막 인자로 ```event```객체가 자동으로 전송된다.
  - 즉 ```event``` 객체를 인수로 기술하지 않아도 된다.
    ```java
    sendEvent(msg, evt) {
        console.log("화살표 함수", msg, evt.type);
    }

    render() {
        return (
            <div>
                <button onClick = { 
                    this.sendEvent.bind(this, "onClick 이벤트")}>
                    눌러보세요1
                </button>
            </div>
        );
    }
    ```