# React.js - 컴포넌트의 Life Cycle
## 초기화 단계 (Mounting)
- DOM 요소를 삽입할 때, 즉 컴포넌트 객체가 생성될 때 한번만 수행된다.
- 컴포넌트를 마운트할 때 순서대로 호출되는 수명주기 함수는 다음과 같다.
  - ```constructor()```
  - ```getDerivedStateFromProps()```
  - ```render()```
  - ```componentDidMount()```

### constructor()
- 수명주기 함수에서 가장 먼저 호출되며, 내부에서는 반드시 ```super()```를 호출해야 한다. ```props```의 경우 필요없다면 기술하지 않아도 된다.
- ```props``` 매개변수는 컴포넌트의 기본 속성값이 적용된 상태로 호출된다.

### getDerivedStateFromProps()
- ```state```의 속성값을 새로운 값으로 변경할 때 주로 사용하는 수명주기 함수이다.
- ```constructor()```로 전달된 props를 통해 ```state```를 변경하게 된다.
- ```render()``` 메소드보다 먼저 호출된다.
    ```java
    <div id="myMounting2"></div>
    <script type="text/babel">
        class MyClass2 extends React.Component {
            /* 
            생성자와 부모 생성자에 props를 매개인자로 넘겨주는 이유:
                1. render()에서 전달되는 props를 매개변수로 받는 것이다.
                2. 전달된 props가 있다면 super()에서도 매개변수로 사용해야 한다.
                3. 생성자로 넘어온 props를 this.state에 넘겨줘서 초기화 시킨다.
            */ 
            constructor(props){
                super(props);
                this.state = {favoriteMovie : "알라딘 : 지니"};
            }
            // 전달받은 props를 통해 state 값을 변경한 후 반환한다.
            static getDerivedStateFromProps(props, state){
                return {favoriteMovie : props.favMovie};
            }
            render() {
                return (
                    <h5>내가 좋아하는 영화는 {this.state.favoriteMovie}</h5>
                );
            }
        }
        ReactDOM.render(<MyClass2 favMovie="어벤져스 : EndGame"/>, document.getElementById("myMounting2"));
    </script>
    ```
### componentDidMount()
- ```render()``` 메소드의 반환값이 실제 DOM에 반영된 직후 호출된다.
- ```setTimeout()``` 메소드나 ```setInterval()``` 메소드를 삽입하기에 적합하다.
  
## 업데이트 단계(Updating)
- 구성요소의 상태나 컴포넌트가 변경될 때 마다 업데이트가 진행된다.
- 이때 호출되는 수명함수는 다음과 같다.
  - ```shouldComponentUpdate()```
  - ```render()```
  - ```getSnapshotBeforeUpdate()```
  - ```componentDidUpdate()```

### shouldComponentUpdate()
- 업데이트를 계속할 지 여부를 지정한다. ```boolean``` 값을 반환한다.
- 기본값은 ```true```이다. 만약 ```false```를 반환하면 렌더링된 상태가 업데이트되지 않는다.

### getSnapshotBeforeUpdate()
- 렌더링 결과가 실제 DOM에 반영되기 전에 호출된다.
- 따라서 업데이트 전의 ```state``` 값을 확인할 수 있다.
    ```java
    getSnapshotBeforeUpdate(prevProps, prevState){
        document.getElementById("div1").innerHTML =
        "업데이트 이전: " + prevState.hotPlace;
    }
    ```

### componentDidUpdate()
- DOM의 구성요소가 업데이트 된 이후에 호출된다.
- ```getSnapshotBeforeUpdate()``` 함수를 사용하는 경우 반드시 해당 함수를 포함해야 한다.
- ```getSnapshotBeforeUpdate()``` 함수의 반환값이 ```componentDidUpdate()``` 함수로 전달되기 때문이다.

## 소멸단계(Unmounting)
- 컴포넌트가 DOM에서 제거될 때 수행되는 메소드로, 1가지만 존재한다.
- 요청, 취소, 타이머 해제, 구독 해제등의 작업을 처리하기에 적합하다.
  - ```componentWillUnmount()```

### componentWillUnmount()
```java
<div id="myUnmount1"></div>
    <script type="text/babel">
        class Container extends React.Component {
            constructor(props){
                super(props);
                this.state = {showFlag : true};
            }

            deleteGNB = () => {
                this.setState({showFlag : false});
            }

            render() {
                let myheader;
                if (this.state.showFlag){
                    myheader = <GNBComp />;
                };
                return (
                    <div>
                        {myheader}
                        <button type ="button" onClick={this.deleteGNB}>
                            GNB 삭제
                        </button>
                    </div>
                );
            }
        }
        // GNB(Global Navigation Bar) : 홈페이지의 공통 상단 부분을 의미한다.
        // LNB(Local Navgiation Bar) : GNB에 따라 변하는 공통 좌측 부분을 의미한다.
        class GNBComp extends React.Component {
            componentWillUnmount(){
                alert("GNBComp 컴포넌트는 해제되었습니다.");
            }

            render() {
                return (
                    <div>
                        <h2>GNB</h2>
                        <table border="1">
                            <tr>
                                <td>메뉴1</td>
                                <td>메뉴2</td>
                                <td>메뉴3</td>
                            </tr>
                        </table>
                    </div>
                );
            }
        }
        ReactDOM.render(<Container/>, document.getElementById("myUnmount1"));
    </script>
```