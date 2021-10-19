# Async, Await
```async```와 ```await``` 키워드는 ES7에 추가되었다. 이 기능들은 기본적으로 비동기 코드를 쓰고, ```Promise```를 더 쉽게 사용할 수 있게 만들어준다.

## 비동기 키워드
> ### async
- 비동기 함수로 만들기 위해 function() 앞에 ```async``` 키워드를 추가한다.
- async function()은 ```await``` 키워드가 비동기 코드를 호출할 수 있게 해주는 함수다.
- async function()가 반환하는 값은 Promise가 된다.
- 화살표 함수로 비동기 함수를 구현하면 다음과 같다.
  ```javascript
  let hello = async () => { return "Hello" };
  ```
- 실제로는 비동기 함수가 fulfiled Promise를 반환하기 때문에 반환된 값을 사용하기 위해선 ```.then()``` 블럭을 사용해야한다.
  ```javascript
  hello().then((value) => console.log(value))

  // 위 코드를 다음과 같이 간소화할 수 있다.
  hello().then(console.log)
  ```
- 정리하면 ```async```를 함수와 같이 사용하면 결과를 직접 반환하는게 아니라 Promise를 반환하게 된다. 또한 동기식 함수에서 ```await```을 사용하여 잠재적인 오버헤드[^1]를 피할 수 있다.

[^1]: 어떤 처리를 하기위해 들어가는 간접적인 처리 시간, 메모리 등을 말한다.

> ### await
- 비동기 함수를 ```await``` 키워드와 함께 쓰면 그 장점이 더 확실해진다.
- ```await``` 키워드는 Promise 기반 함수 앞에 놓을 수 있다.
- ```await``` 키워드가 사용되면 해당 Promise가 ```fulfil``` 상태가 될 때 까지 진행을 잠시 중단한다.
- ```await``` 키워드는 JavaScript 런타임이 이 라인에서 비동기 코드를 일시 중지하여 비동기 함수 호출이 결과를 반환할 때 까지 기다리게 한다.
- 그러나 외부의 다른 동기 코드는 실행될 수 있도록 한다.
- 작업이 완료되면 코드는 계속 이어져서 실행된다.

> ### 예제
- 기존 ```fetch``` ~ ```then``` ~ ```catch``` 구문
```javascript
fetch(url)
    .then(result => {
        return result.json();
    })
    .then(json => {
        setJson(json);
    })
    .catch(e => {
        console.log(e.message);
    });
```
- ```async``` ~ ```await``` 구문
```javascript
const fucn = async () => {
    let result = await fetch(url);
    let json = await result.json();

    return json;
}

// then 구문을 함수 내에 포함시켜서 then 블록을 사용하지 않아도된다.
func().then( (json) => { setJson(json) } )
    .catch( (e) => {console.log(e.message)} );
```
- 비동기 코드를 실행할 블럭을 정의하려면 반드시 비동기 함수를 생성해야한다.

> ### Promise.all ( iterable )
- 모든 프로미스가 이행될 때 까지 기다렸다가 그 결과값을 담은 배열을 반환하는 메서드
- 여러개의 프로미스가 모두 ```resolve```된 후 다음 로직을 실행해야하는 경우에 사용한다.
- 복수의 URL에 request를 보내고, 모든 요청에 대해 응답이 왔을 때 화면을 렌더 해야하는 상황이 그 예시이다. 
- 예제1
  ```javascript
    let urls = [
    'https://www.example.com/users/1',
    'https://www.example.com/product/2',
    'https://www.example.com/article/3'
    ];

    // fetch를 사용해 url을 프라미스로 매핑
    let requests = urls.map(url => fetch(url));

    // Promise.all은 모든 작업이 리졸브 될 때까지 대기
    Promise.all(requests)
    .then(responses => responses.forEach(
        response => alert(`${response.url}: ${response.status}`)
    ));
  ```
- 입력된 프로미스 배열이 하나라도 리젝트 되면 ```Promise.all``` 또한 리젝트 된다. ( Transaction? ) 
- 예제2
  ```javascript
    async function displayContent() {
        let coffee = fetchAndDecode('coffee.jpg', 'blob');
        let tea = fetchAndDecode('tea.jpg', 'blob');
        let description = fetchAndDecode('description.txt', 'text');

        let values = await Promise.all([coffee, tea, description]);

        let objectURL1 = URL.createObjectURL(values[0]);
        let objectURL2 = URL.createObjectURL(values[1]);
        let descText = values[2];

        let image1 = document.createElement('img');
        let image2 = document.createElement('img');
        image1.src = objectURL1;
        image2.src = objectURL2;
        document.body.appendChild(image1);
        document.body.appendChild(image2);

        let para = document.createElement('p');
        para.textContent = descText;
        document.body.appendChild(para);
        }

    displayContent()
    .catch((e) =>
    console.log(e)
    );
  ```

***
## async/await의 단점
async/await은 우리가 작성한 코드를 마치 동기식 코드처럼 보이게 한다. 또한 ```await``` 키워드를 사용하여 실제로 어떤 면에서는 동기적으로 행동할 수 도 한다. 이는 우리가 작성한 코드가 바로 이어지는 수 많은 Promise에 의해 느려질 수 있음을 의미한다. 

이 문제를 완화할 수 있는 패턴이 있다. 모든 Promise 오브젝트를 변수에 저장하여 미리 실행되게하고 변수가 사용할 때 꺼내서 쓰는 것이다.

> ### 예제
- 느린 비동기 작업
  ```javascript
  async function timeTest() {
    await timeoutPromise(3000);
    await timeoutPromise(3000);
    await timeoutPromise(3000);
  }
  ```
- 빠른 비동기 작업
  ```javascript
  async function timeTest() {
    const timeoutPromise1 = timeoutPromise(3000);
    const timeoutPromise2 = timeoutPromise(3000);
    const timeoutPromise3 = timeoutPromise(3000);

    await timeoutPromise1;
    await timeoutPromise2;
    await timeoutPromise3;
  }
  ```