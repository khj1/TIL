# React.js - Functional Component
## 함수형 컴포넌트의 props
- 함수형 컴포넌트는 출력할 내용을 즉시 return한다.
- 함수 안에 또다른 함수를 정의할 수 없으므로 render() 함수가 별도로 존재하지 않고, 함수의 ```return```이 ```render()``` 역할을 한다.
- 부모 컴포넌트에서 ```props```로 전달한 데이터를 받을 때 매개 변수를 사용한다.
  - 매개변수 형태이므로 ```props```가 아닌 다른 이름을 사용해도 된다.
  ```javascript
  function FuncComponent( props ){
    return (
        <div className="container">
            <h2>function style component</h2>
            <p>initNumber : { props.initNumber }</p>
        </div>
    );
  } 
  ```
***
## Hook
- 리액트 기존 버전에서는 간단한 기능을 제작할 때만 함수를 사용했다. 
- ```state```를 변경하는 것은 클래스형 컴포넌트만 가능했었다. 
- 하지만 16.8 버전 부터 **Hook을** 통해 함수형 컴포넌트에서도 ```state``` 값을 변경할 수 있게 되었다. 
- **React Hook**은 ```useXXX()```과 같은 이름을 가진다.
- **Hook**을 사용하기 위해서는 반드시 import 처리해야한다.
    ```javascript
    import { useState, useEffect, useParams } from "react-router-dom";
    ```
> ### useState( 값 )
- ```useState()```로 얻어온 값을 출력하면 **크기가 2인 배열**이 출력된다.
- 첫번째 요소는 인자로 전달한 값( ```state``` 값 )이고
- 두번째 요소는 ```state``` 값을 변경할 수 있는 함수이다.
  ```javascript
  function FuncComponent( props ){
    let numberState = useState(props.initNumber);
    console.log("numberState", numberState);

    let number = numberState[0]; // 값 반환
    let setNumber = numberState[1]; // 함수 반환

    // 다음과 같이 간소하게 표현할 수 도 있다.
    // let [ number, setNumber ] = useState(props.initNumber);

    return (
        <div className="container">
            <h2>function style component</h2>
            <p>initNumber : { number }</p>

            {/* 
            setState()로 전달된 2번째 인자를 setNumber라는 함수명으로
            받았으므로 튼 클릭 시 number 값을 생성된 난수로 변경하게 된다.해당 버튼의 이벤트 처리에서 사용할 수 있다.
            버
            */}
            <input type="button" value="난수 생성" onClick={ () => {
                setNumber( Math.random() );
            }} />
        </div>
      );
  }
  ```

> ### useEffect()
- **life cycle**을 사용하기 위한 **Hook**
- ```useEffect()```는 ```componentDidMount()```와 ```componentDidUpdate()```의 기능을 모두 수행한다.
- ```useEffect()```는 두개의 인자를 가진다.
  - 첫번째 인자는 실행시킬 메서드
  - 두번째 인자는 의존성 배열
  - 의존성 배열에 어떤 인자가 들어간다면, Rerendering 될 때 해당 인자가 변할 때만 ```useEffect()```의 ```componentDidUpdate()``` 기능이 실행된다.
```javascript
function FuncComponent( props ){
    // return 전에 특정 함수를 호출하는 것으로 전처리를 할 수 있다.
    console.log("#Life#", "FuncComponent==>함수 실행");

    // 반환 값이 배열이므로 다음과 같이 간소하게 표현할 수 도 있다.
    let [ nowDate, setDate ] = useState((new Date()).toString());

    /* 
    useEffect()는 렌더링 된 이후 자동으로 호출되는 메서드이다.
    useEffect()의 첫번째 인자는 함수가 와야한다.
    */
    useEffect(() => {
        console.log("#Life#", "FuncComponent==>useEffect");
    });

    // 함수형 컴포넌트에서는 return이 render()의 역할을 하게된다.
    console.log("#Life#", "FuncComponent==>return 실행( render와 동일 )");
    return (
        <div className="container">
            <p>날짜 : { nowDate }</p>
            <input type="button" value="현재 날짜" onClick={ () => {
                setDate( (new Date()).toString() );
            }} />
        </div>
    );
}
```

> ### 클래스형 컴포넌트의 생명주기
- ```UNSAFE_componentWillMount()```
  - 컴포넌트가 마운트되기 전에 호출된다.
- ```componentDidMount()```
  - 컴포넌트가 마운트된 후에 호출된다.
- ```shouldComponentUpdate()```
  - 최초로 렌더링 될 때는 호출되지 않는다.
  - ```state``` 값 변경에 의해 재 렌더링될 때 호출된다.
  - 해당 함수에서 ```true```가 호출될 때만 ```render()```가 실행된다.
  - 만약 ```false```가 반환되면 재 렌더링되지 않는다.

> ### 컴포넌트를 삭제하는 법
- 해당 예제에선 삼항 연산자와 ```useState()``` Hook을 이용하여 컴포넌트 삭제 기능을 구현했다.
```javascript
function App() {
    let [ funcShow, setFuncShow ] = useState(true);
    return (
        <div className="container">
            <input type="button" value="함수형 컴포넌트 삭제" onClick={
                () => {
                    if( funcShow )
                        setFuncShow(false);
                    else
                        setFuncShow(true);
                }
            }/> 
            { funcShow ? <FuncComponent initNumber={ 2 }></FuncComponent> : null } 
        </div>
    );
}
```