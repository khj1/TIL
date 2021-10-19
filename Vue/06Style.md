# Vue - Style
## CSS와 Vue에서의 CSS 스타일 선언 및 적용
1. ```<태그 style="">``` => ```<태그 :style="">```
2. ```background-color: red;``` => ```{backgroundColor: 'red'}```
3. ```<태그 class="style1, style2">``` => ```<태그 :class={style1:true, style2:true}>```

## Vue에서의 클래스 적용 형태
1. ```:class = "{클래스명: true, 클래스명: false}"```
2. ```:class = "{클래스명: 모델1, 클래스명: 모델2}"```
    ```javascript
    new Vue({
        el:"#app",
        data:{
            s1: false, s2: false, s3: false,
            score: 0
        },
        computed :{
            info(){
                if(this.score >= 1 && this.score <= 100){
                    return { warning : false };
                } else {
                    return { warning : true };
                }
            }
        }
    });
    ```
    ```html
    <button :class="{set1:s1, set2:s2, set3:s3}">
        클래스 적용 버튼 1
    </button>
    <button :class="{set1:s1, set2:s2, set3:s3}">
        클래스 적용 버튼 2
    </button>
    <p>
        <input type="checkbox" v-model="s1" value="true"> set1 디자인 <br>
        <input type="checkbox" v-model="s2" value="true"> set2 디자인 <br>
        <input type="checkbox" v-model="s3" value="true"> set3 디자인 <br>
    </p>
    <div>
        <p>1부터 100까지만 입력 가능합니다.</p>
        <div>
            점수: <input type="text" class="score" v-model.number="score" :class="info">
            <img src="img/error.png" class="warnimage" v-show="info.warning">
        </div>
    </div>
    ```