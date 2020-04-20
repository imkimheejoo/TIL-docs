# computed
만약 num 이라는 숫자가 있을 때, 우리는 num의 두 배인 값을 화면에 그리고 싶다고 가정해봅시다. 저는 처음엔 그냥 num *2 를 doubleNum에 넣으면 되는거 아닌가? 하고 다음과 같이 구현했습니다.
```html
<div id="app">
    <p>{{num}}</p>
    <p>{{doubleNum}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data:{
            num: 10,
            doubleNum: this.num * 2
        }
    })
</script>
```
그러고 결과를 확인해보니 doubleNum은 NaN이 나왔고.. 아니왜..? 라는 생각이 들었습니다... <br>
자, 그러면 어떻게 2배 값을 만들수 있을까..! 여기서 등장하는 기법 computed 입니다.

```html
...
<body>
<div id="app">
    <p>{{num}}</p>
    <p>{{doubleNum}}</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data:{
            num: 10,
        },
        computed:{ // data가 바뀌면 자동적으로 computed 의 리턴값도 바뀜
            doubleNum: function () {
                return this.num * 2;
            }
        }
    });
</script>
</body>
```
현재 computed는 doubleNum 데이터에 대한 속성을 정의하였습니다. doubleNum 키값은 num 데이터가 변화할때 밸류 함수가 실행되어 값을 바로바로 갱신시킵니다.<br>
음... computed가 뭔지는 모르겠지만 **데이터의 변경에 따른 로직을 수행**하는 성질을 가지는 것 같습니다.

---

다른 상황을 가정해봅시다. ` <p v-bind:class="{속성이름 : boolean}">Hello</p>` 과 같이 클래스 속성을 조건에 따라 다르게 나타낼수 있습니다. <br>
하지만 조건 값(boolean)을 Vue 인스턴스가 관리한다면, 코드의 중복을 막을 수 있는 장점이 있습니다. 이는 computed로 가능한데요, 예시는 다음과 같습니다.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .warning {
            color: red;
        }
    </style>
</head>
<body>
<div id="app">
    <p v-bind:class="errorTextColor">Hello</p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            isError: false
        },
        computed: {
            errorTextColor: function () {
                return this.isError ? 'warning' : null;
            }
        }
    })
</script>
</body>
</html>
```
- 속성에 대한 값(데이터?)를 computed가 관리함으로써 DOM에는 간결하게 표현되는 것을 알 수 있습니다.
- 또한 errorTextColor의 조건을 다른 태그가 써야할때 조건 코드를 또 쓰지 않아도 되는것을 알 수 있습니다.

## computed란?
그렇다면 위 두 예시를 통해 computed를 정의해봅시다. computed는 모두 **`data` 에 초점**을 맞춘 기능입니다.<br> 
즉, data의 변화 감지, 데이터의 표현을 간결화해주는 기능이라고 생각합니다.
