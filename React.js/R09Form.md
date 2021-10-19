# React.js - Form
## React에서 Form 사용하기
폼값 전송 시 이벤트 객체를 통해 입력한 태그의 여러가지 속성값을 다음과 같은 패턴으로 얻어올 수 있다.
- ```type``` 속성값 : ```event.target.type```
- ```value``` 속성값 : ```event.target.value```
- ```name``` 속성값 : ```event.target.name```
- ... 이런 방식으로 모든 속성값을 불러올 수 있다. (이벤트 객체를 넘겨줘야함)
    ```java
    render() {
        return (
            <form>
                <p>
                    {/* JSX에서 주석은 이와같이 사용한다. */}
                    {/* 
                        이벤트 핸들러에서 사용하는 함수는 {} 안에 무기명 함수
                        혹은 화살표 함수 둘 다 사용 가능하다.
                    */}
                    아이디: <input type="text"
                        onKeyUp={function(){console.log(event)}}/>
                    <br />

                    비밀번호: <input type="password"
                        onKeyUp={() => {console.log(event.target.value)}}/>
                </p>
            </form>
        );
    }
    ```

## 폼에서 발생한 이벤트 처리
```java
class MyForm2 extends React.Component {
    constructor(props){
        super(props);
        this.state = { username : "초기화됨" };
    }

    myChangeHandler = (event) => {
        this.setState({ username : event.target.value });
    }

    // React에서는 input 태그에 chagne 이벤트를 사용할 수 있다.
    render() {
        return (
            <form>
                <h1>입력한 이름은 : {this.state.username}</h1>
                <input type="text" onChange = { this.myChangeHandler } />
            </form>
        );
    }
}
```

## 폼값 젅송
- ```event.preventDefault()```
  - 폼값이 전송되는 것을 막기 위해 사용하는 메소드
  - React는 철저히 프론트 엔드용 라이브러리이므로 모든 작업이 끝난 후에 폼값이 전송되어야 하므로 잠시 막아주는 역할을 해준다.
  - 화면의 깜빡임 없이 데이터를 처리해야하므로 주로 사용한다.

## 여러개의 input 태그가 있는 폼
- 대괄호 표기법
    ```java
    <div id="myDiv5"></div>
        <script type="text/babel">
            class MyForm5 extends React.Component {
                constructor(props){
                    super(props);
                    this.state = {
                    movie : null,
                    actor : null,
                    content : null  
                    };
                }

                myChangeHandler = (event) => {
                    // event 객체를 통해서 입력 상자의 name, value 속성을 얻어온다.
                    let _name = event.target.name;
                    let _value = event.target.value;

                    /* 
                    변화된 입력 값을 감지하여 그와 동일한 이름의 state를 자동으로 찾아
                    값을 변경한다.

                    즉 대괄호 표기법을 통해 여러개의 state 값을 하나의 문장으로 처리할 수 있다.
                    */
                    this.setState({[_name] : _value});
                    console.log("myChangeHandler", this.state);
                }

                render() {
                    return (
                        <form>
                            <h3>
                                {this.state.movie} / {this.state.actor}
                            </h3>
                            <h4>{this.state.content}</h4>
                            영화 : <input type="text" name="movie" onChange={this.myChangeHandler} /> <br/>
                            주연배우 : <input type="text" name="actor" onChange={this.myChangeHandler} /> <br />   
                            줄거리 : <input type="text" name="content" onChange={this.myChangeHandler} />    
                        </form>
                    );
                }
            }
            ReactDOM.render(<MyForm5/>, document.getElementById("myDiv5"));
        </script>
    ```