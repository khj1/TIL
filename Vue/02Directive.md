# Vue - v-bind
> ### Directive란
- DOM 객체의 속성을 Model 데이터와 연동하기 위해 directive를 사용한다.

## v-bind, v-text, v-html
- ```v-bind```: HTML 속성에 값을 설정할 때 사용한다.
- ```v-text```: DOM 하위에 모델 데이터를 렌더링 없이 그대로 출력할 때 사용한다.
  - ```{{ }}```과 동일한 역할을 한다.
- -```v-html```
  - 모델 속성과 연결되는 값이 출력된다.
  - 모델 데이터가 html 태그를 포함하여, 태그가 적용된 내용을 렌더링해서 보여줄 때 사용하는 디렉티브이다.

> ### 형식
- ```v-bind```
    ```xml
    <태그 v-bind:HTML 속성 = "모델 속성">
    <!-- 다음과 같이 v-bind를 생략할 수 있다. -->
    <태그 :HTML 속성 = "모델 속성">
    ```
- ```v-text```, ```v-html```
    ```xml
    <태그 v-text="모델 속성">
    ```


> ### 예제
- script 부분
    ```javascript
    let vue = new Vue({
        el : "#app",
        data : {
            imagePath:"http://sample.bmaster.kro.kr/photos/61.jpg",
            idMsg:"아이디를 입력하세요.",
            id:"아이디",
            genTxt:["남자", "여자"],
            align01:{ textAlign:"center" } 
        }
    });
    ```
- View
    ```xml
    <td v-text="id"></td>
    <td><input type="text" :placeholder="idMsg"></td>

    <select>
        <option v-text="genTxt[0]"></option>
        <option v-text="genTxt[1]"></option>
    </select>

    <!-- 
        style 속성에 model 데이터 값을 설정하는 법 
            JSON 객체를 활용하여 설정한다.
    -->
    <td colspan="2" :style="align01">
    ```

***
## v-model
모델 데이터를 입력하거나 선택하기 위해 form 데이터에 속성으로 추가한다. 모델 데이터가 양 방향으로 변경 처리된다. 즉 ***사용자가 입력한 내용이 모델 데이터에 실시간 반영된다.***

React의 ```state```와 비슷한 역할을 한다.

> ### 적용 순서
- 대상 DOM 객체는 데이터를 입력 처리 가능한 form 하위 객체의 속성으로 설정한다.
- ```v-model```은 해당 입력 DOM 객체의 입력 값에 따라 Model 데이터에 영향을 준다. 입력하는 순간 Model 데이터가 변경된다.
- 해당 Model로 연동하는 출력처리 디렉티브( ```{{}}```, ```v-html```, ```v-text``` )를 통해 실시간 변경이 되는 것을 확인할 수 있다.
- **모델 데이터가 배열인 경우**
  - 체크박스인 경우 체크할 때 실시간으로 mapping 처리 된다.
  - ```v-model="배열모델"```은 순서에 따라 checkbox의 value 값과 연동된다.
  - checkbox를 check 했을 때 mapping된 value 값을 해당 모델 배열에 순서대로 할당한다.


> ### 형식
```xml
<태그 v-model="모델명">
```

> ### 예제
- script 부분
    ```javascript
    $(()=>{
        let vue = new Vue({
            el: "#app",
            data : { name:"" }    
        });
    });
    ```
- View
    ```xml
    <!-- input 태그에 name 모델 값을 변경하는 v-model 설정 -->
    구매자 : <input type="text" v-model="name"> <br>

    <!-- input 값이 변경됨에 따라 실시간으로 반영됨 -->
    <table border="1">
        <tr>
            <th>구매자</th>
            <td v-text="name"></td>
        </tr>
    </table>
    ```

***
## v-if, v-for, v-show
> ### ```v-if"```
- 조건에 부합되는 내용만 렌더링하고, 부합되지 않으면 렌더링하지 않는 디렉티브이다. 
> ### ```v-for```
- ```v-for``` 디렉티브를 통해 JSON 배열을 출력할 떄는 각 요소와 인덱스를 반환받을 수 있다.
- ```v-for```을 이용한 배열 처리
    ```xml
    <태그 v-for="(단위 객체, index) in 모델 배열">
    ```
- ```v-for```을 이용한 객체 처리
    ```xml
    <태그 v-for="(value, key, index) in 모델 객체명">
    ```
> ### ```v-show```
- 일단 DOM 요소를 렌더링한 후 조건에 만족하지 않으면 화면에서 hidden 처리하고, 조건에 만족할 때 화면에 출력한다.
- **HTML의 hidden form 역할을 수행할 수 있다.**

***
## v-once, v-pre
> ### v-once
- 초기에 렌더링된 모델 데이터를 고정적으로 출력한다.
- 모델 데이터를 변경하더라도 초기값을 그대로 유지하고 싶을 떄 사용하는 디렉티브이다.
> ### v-pre
- 렌더링하지 않고 directive 내용을 그대로 보여준다.
- 주로 디버깅이나 테스트 용으로 Model 데이터를 출력하고 싶지 않을 때 사용한다.

***
## 부록
> ### \<template> 태그
- 태그 자체를 포함하여 반복처리를 할 때 사용한다.
- ```v-for```를 통해 여러개의 태그를 한번에 반복하고 싶다면 반복을 원하는 모든 태그에 ```v-for``` 속성을 추가해줘야 했다.
- 이러한 번거로움을 덜기 위해 **\<template>** 태그를 사용한다. 반복하고 싶은 태그들을 **\<template>** 태그 하위에 두고 **\<template>** 태그에 한번만 ```v-for``` 속성을 추가하면 표현을 간소화할 수 있다.
- component에서는 기본 화면 구성 요소로 tag를 포함할 때 id 속성과 함께 사용한다.