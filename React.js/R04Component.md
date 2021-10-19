# React.js - Component
## 컴포넌트(Component)
- UI 개발이 레고라면 컴포넌트는 하나의 블럭과도 같다.
- HTML 요소를 반환하는 '함수'와 같은 역할을 한다.
- 독립적이며 재사용이 가능한 코드이다.
- JS 함수와 동일한 용도로 사용되지만, 렌더링을 통해 HTML을 반환한다.
- class 형태와 function 형태로 나뉜다.

## 컴포넌트 제작 시 규칙
- 이름은 대문자로 시작해야한다.
- ```React.Component```를 상속받아서 접근 권한을 부여받아야 한다.
- ```render()``` 메소드에서 HTML 코드를 반환한다.
- 함수형 컴포넌트는 ```render()``` 함수 내에서 ```return```하지 않는다. 함수 자체가 ```render()``` 역할을 하게된다.
- 클래스명이 ```Car```라면 ```render()``` 함수에서는 ```<Car />```로 기술한다.

## Component의 Constructor
- Java의 생성자와 기본적으로 동일한 역할을 하는 메소드로 변수 초기화에 주로 이용된다.
- 생성자 내에서 ```state``` 값을 지정해줄 수 있다.
- JSP의 ```include```와 동일하게 컴포넌트가 다른 컴포넌트를 포함하는 형태로 만들어질 수 있다.
    ```java
    <div id="myCiC"></div>
    <script type="text/babel">
        class Computer extends React.Component {
            render() {
                return <h4>최신 사양 컴퓨터</h4>;
            }
        }
        class Notebook extends React.Component {
            render() {
                return (
                    <div>
                        <Computer />
                        <h4>디자인이 좋은 노트북</h4>   
                    </div>  
                );
            }
        }
        ReactDOM.render(<Notebook/>, document.getElementById("myCiC"));
    </script>
    ```
- 단 이경우에도 취상위 엘리먼트는 하나만 존재해야한다.

