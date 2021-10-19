# Promise
## Promise 란?
비동기 처리를 순차적으로 실행할 필요가 있는 경우가 있다. 기존의 방식은 CallBack을 중첩시켜 표현하므로 에러 처리, 예외 처리가 어렵다는 단점이 존재한다. 프로미스는 기존 콜백의 단점을 해결하기 위한 라이브러리이다.

> ### Promise의 3가지 상태( state )
- ```Pending``` : 대기상태
  - 비동기 처리 로직이 완료되지 않은 상태
- ```Fulfiled``` : 이행상태
  - 비동기 처리가 완료되어 프로미스가 결과값을 반환해준 상태
- ```Rejected``` : 실패상태
  -  비동기 처리가 실패하거나 오류가 발생한 상태.

```javascript
function myGetData(callBackFunc){
    params = {};
    // 프로미스 객체를 선언 후 반환
    return new Promise(function(resolve, reject){
        $.get('./myData.txt', params, function(resData){
            if(resData)
                // 이행 상태가 되면 resolve() 함수를 호출한다.
                resolve(resData);
            else
                // 실패 상태가 되면 reject() 함수를 호출한다.
                reject(new Error("요청 실패"));
        });
    })
}
myGetData().then(function(argData){
    // 프로미스가 이행상태가 되면 then()을 통해 처리결과를 받을 수 있다.
    console.error("then..");
    console.log(argData);
}).catch(function(errData){
    // 실패 상태가 되면 catch()에서 에러 결과를 받을 수 있다.
    console.error("reject..");
    console.log(errData);
});
```