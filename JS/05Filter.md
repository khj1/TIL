# Filter Function
## filter()
```filter()``` 함수는 명칭과 같이 필터링 해주는 함수이다. 콜백 함수의 조건에 해당하는 요소만 선별하여 새로운 배열을 반환해준다.

필터 함수는 해당 배열의 요소를 하나하나 반복하며 조건에 맞는 값을 선별해준다.

> ### 형식
```javascript
let oldArr = [1, 2, 3, ...]
let newArr = oldArr.filter(
    callBackFunction( 요소값, 요소인덱스, 사용하는 배열 객체){
        return 필터링할 조건;
    }, thisArgs );
```
- 형식 마지막의 thisArgs는 filter에서 사용할 ```this``` 값으로 사용한다. 만약 생략할 경우 ```this```에는 ```undefined```이 전달된다.

> ### 예제
- ```filter()``` 메소드의 두번째 인자를 생략
```javascript
(function filterTest3() {
    let objJSONArray = [
        {name: "혜빈", salary: 100000},
        {name: "제인", salary: 200000},
        {name: "나윤", salary: 300000},
        {name: "주이", salary: 400000},
        {name: "아인", salary: 500000},
        {name: "낸시", salary: 600000},
    ];
    let newJSON = objJSONArray.filter((elt, i, arr) => {
        return elt.salary >= 300000;
    });
    console.log("newJSON: ", newJSON);
})();
```
- ```filter()``` 메소드의 두번째 인자를 사용
```javascript
(function filterTest4(){
    let testArray = [1, 2, 3, 4, 5, 50, 100];
    let thisArg = { min:1, max:10 };

    function getMinMax(value){
        return value > this.min && value <= this.max;
    }

    let newArray = testArray.filter(getMinMax, thisArg);
    console.log("thisArgs 사용결과", newArray);
})();
```

***
## 부록
> ### 함수를 정의하고 즉시 호출하는 방법
```javascript
(function 함수명(){
    실행부;
})();
```