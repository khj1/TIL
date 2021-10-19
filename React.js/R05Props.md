# React.js Props
## props()
- React의 구성요소에 전달되는 일종의 매개변수(파라미터)를 의미한다.
- 주로 HTML 속성을 통해 컴포넌트로 전달된다.
- 개발에서 많이 사용되는 용어인 Properties의 줄임말이다.
- 사용자가 컴포넌트로 전달해서 변경되지 않고 보관되는 읽기 전용 데이터이다.
- 만약 변경해야 한다면 컴포넌트 내부가 아닌, 부모 컴포넌트에서 변경해야 한다.
    ```java
    <div id="myDiv"></div>
    <script type="text/babel">
        class Colorful extends React.Component {
            render() {
                //전달받은 Props는 표현식을 통해 접근한다.
                return <h4>난 {this.props.color}이 좋더라</h4>;
            }
        }
        ReactDOM.render(<Colorful color ="빨간색"/>, document.getElementById('myDiv'));
    </script>
    ```
- ```props```는 JSON을 통해 여러개의 변수를 받을 수도 있다.
    ```java
    <div id="myDiv4"></div>
    <script type="text/babel">
        class Car extends React.Component {
            render() {
                return (
                    <div>
                        <h4>제조사 : {this.props.brand.name}</h4>    
                        <h4>모델명 : {this.props.brand.model}</h4>    
                    </div>
                );
            }
        }
        class ShowRoom extends React.Component {
            render() {
                const carinfo = {name : "KIA", model : "K7"};
                return (
                    <div>
                        <h4>당신이 선호하는 자동차는?</h4>    
                        <Car brand={carinfo}/>   
                    </div>
                );
            }
        }
        ReactDOM.render(<ShowRoom/>, document.getElementById("myDiv4"));
    </script>
    ```