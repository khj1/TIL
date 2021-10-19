# React.js - react-router-dom
리엑트는 기본적으로 화면의 새로고침 없이 페이지를 갱신한다. 이 경우 주소가 하나로 고정되기 때문에 즐겨찾기와 같은 기능을 사용할 수 없다. 어떤 주소로 들어왔을 때 그 주소를 알아내어 그에 해당하는 컴포넌트를 렌더링하고, 그 상태를 관리하기 위해 내부적으로 ```state```나 ```props```를 관리할 수 있게 해주는 도구가 ***react-router-dom*** 이다.

## Router 관련 태그 및 속성
***※ 라우팅이란*** 사용자가 어떤 주소로 들어왔을 때 그 **주소에 해당하는 적당한 페이지를 사용자에게 보내주는 것**을 말한다.
> ### Router 관련 예제
```javascript
import { BrowserRouter, Route, Switch, Link, NavLink, useParams } from "react-router-dom";

function App() {
    return (
        <BrowserRouter>
            <div className="App">
                <h1>Hello React Router DOM</h1>
                <ul>
                    <li><a href="/">Home(a)</a></li>
                    <li><a href="/Topics">Topics(a)</a></li>
                    <li><a href="/Contact">Contact(a)</a></li>
                </ul>
                <ul>
                    <li><NavLink exact to="/">Home(Link)</NavLink></li>
                    <li><NavLink to="/Topics">Topics(Link)</NavLink></li>
                    <li><NavLink to="/Contact">Contact(Link)</NavLink></li>
                </ul>
                <Route exact path="/"><Home></Home></Route>
                <Route path="/Topics"><Topics></Topics></Route>
                <Route path="/Contact"><Contact></Contact></Route>

                <h3>Switch 적용하기</h3>
                <Switch>
                    <Route exact path="/"><Home></Home></Route>
                    <Route path="/Topics"><Topics></Topics></Route>
                    <Route path="/Contact"><Contact></Contact></Route>
                    <Route path="/">404:Page Not Found</Route>
                </Switch>
            </div>
        </BrowserRouter>
    );
};
```
> ### \<BrowserRouter> 태그
- react-router-dom을 적용하고 싶은 컴포넌트의 **최상위 컴포넌트**를 감싸주는 Wrapper로 사용한다.
> ### \<Route> 태그
- URL에 따른 적당한 컴포넌트를 렌더링 하기 위해 사용하는 컴포넌트이다.
  ```javascript
  <Route exact path="/"><Home></Home></Route>
  ```
  - ```path``` : 해당 경로를 포함하는 URL이 호출되었을 때 컴포넌트를 mount한다.
  - ```exact``` : ```path```( 경로 )와 정확히 일치하는 경우에만 라우팅된다.
> ### \<Switch> 태그
- URL과 일치하는 첫번째 컴포넌트가 발견되면 나머지 컴포넌트는 아예 렌더링하지 않는 역할을 한다.
- 즉 최초로 발견되는 컴포넌트 하나만 렌더링한다.
  ```javascript
  <Switch>
      <Route exact path="/"><Home></Home></Route>
      <Route path="/Topics"><Topics></Topics></Route>
      <Route path="/Contact"><Contact></Contact></Route>
      // 엉뚱한 URL이 입력되면 호출된다.
      <Route path="/">404:Page Not Found</Route>
  </Switch>
  ```

> ### \<Link> 태그
- 기존 \<a> 태그는 링크를 클릭할 때 마다 페이지가 새로고침 되었다.
- \<Link> 태그는 링크를 눌렀을 때 페이지가 리로드되지 않도록 처리해준다.
- \<Link> 태그의 ```to``` 속성이 \<a> 태그의 ```href``` 속성의 역할을 한다.

> ### \<NavLink> 태그
- \<Link> 태그와 기본적으로 동일한 기능을 제공한다.
- 추가적으로 \<NavLink> 태그의 링크를 눌렀을 때 라우터로 호출되는 엘리먼트에 ```class="active"``` 속성이 부여된다.
  ```javascript
  <li><NavLink exact to="/">Home(Link)</NavLink></li>
  <li><NavLink to="/Topics">Topics(Link)</NavLink></li>
  <li><NavLink to="/Contact">Contact(Link)</NavLink></li>
  ```

***
## Nested Routing
중첩 라우팅이란 라우팅 맵핑을 최상위 컴포넌트 뿐만 아니라 여러개의 컴포넌트에 걸쳐서 단계별로 정의하는 라우팅 기법이다.

라우팅으로 호출된 컴포넌트 내에서 또다른 라우팅이 이루어질 수 있다.
- ***위 예제에서 이어지는 코드입니다.***
```javascript
let contents = [
    { id:1, cate:"free", title:"자유게시판", desc:"<h2>자유게시판 리스트</h2>" },
    { id:2, cate:"qna", title:"QnA게시판", desc:"<h2>QnA게시판 리스트</h2>" },
    { id:3, cate:"faq", title:"FAQ게시판", desc:"<h2>FAQ게시판 리스트</h2>" }
];

// Topic 관련 URL이 호출되었을 때 렌더링되는 컴포넌트
function Topics(){
    let liTag = [];
    for(let i in contents){
        liTag.push(
            <li key={contents[i].id}>
                <NavLink to={"/Topics/" + contents[i].cate}>{contents[i].title}</NavLink>
            </li>
        );
    }
    return (
        <div>
            <h2>Topics</h2>
            <div>Topics 컴포넌트</div>
            <ul>
                { liTag }
            </ul>
            <Route path="/Topics/:topic_cate"><Desc></Desc></Route>        
        </div>
    );
};

// <Desc> 컴포넌트
const Desc = () => {
    let params = useParams();
    console.log("params", params); // {topic_cate: "faq"}와 같은 형태로 전송됨

    let topic_cate = params.topic_cate;
    let selected_item = {
        title : "Sorry",
        desc : "Not Found"
    }
    for(let i=0; i<contents.length; i++){
        if(contents[i].cate === topic_cate){
            selected_item = contents[i];
            break;
        }
    }
    return (
        <div>
            <h3>{selected_item.title}</h3>
            {selected_item.desc}
        </div>
    );
}
```

> ### useParam() - Hook
- 파라미터로 전송된 값을 받아오기 위한 훅
- ```<Route path="/Topics/:topic_cate">```과 같이 라우팅 처리를 할 때 파라미터를 ```:변수명```과 같은 형태로 전송한다.
- 스프링의 ```@PathVariable```과 비슷하다.
- 예시
  - 라우터 = ```/Topics/:param1/:param2```
  - 링크 = ```/Topics/aaa/bbb```
  ```javascript
  let param = useParams();
  param = { param1 : aaa, param2 : bbb }
  ```