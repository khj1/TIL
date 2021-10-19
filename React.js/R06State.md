# React.js - State
## state
- React 컴포넌트의 상태를 저장할 때 주로 사용된다.
- ```props```와 비슷하지만, ```state```는 컴포넌트 내부에 존재하기 때문에 상대 값을 변경할 수 있다.
- 값 변경시에는 ```setState()``` 함수를 사용한다.
    ```java
    <div id="myArea1"></div>
    <script type="text/babel">
        class Person extends React.Component {
            /* 
            자바의 class와 다르게 생성자 내부에서 멤버 변수를 선언하고
            동시에 초기화 해준다고 생각하면 된다.
            */ 
            constructor(){
                super();
                this.state = {
                    name : "홍길동",
                    age : 28,
                    hobby : "자전거",
                    birth : 2000
                };
            }
            render() {
                return (
                    <div>
                        <h1>내 이름은 {this.state.name}</h1>
                        <p>
                            나이 : {this.state.age} <br/>
                            취미 : {this.state.hobby} <br/>
                            태어난 해 : {this.state.birth} <br/>
                        </p>
                    </div>
                );
            }
        }
        ReactDOM.render(<Person />, document.getElementById("myArea1"));
    </script>
    ```