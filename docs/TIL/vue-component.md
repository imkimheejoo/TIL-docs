# Vue.js Component

## 컴포넌트
![](/img/component-1.png ':size=600x300')<br>
화면의 영역을 구분한 것을 말합니다.(그림 참고)<br>
따라서 연한 회색칸에 여러 진한회색칸을 가지게 되는 **계층관계**도 나타낼 수 있습니다.
- 컴포넌트는 화면의 영역을 구분하여 개발할 수 있는 뷰의 기능으로 이때 오는 장점은 **반복적인 코드대신 컴포넌트를 불러와 사용**하기때문에 재사용성이 높습니다.
    - 인스턴스를 생성하면 기본적으로 Root 컴포넌트로 인식합니다.


## 컴포넌트 등록법
컴포넌트 등록법은 전역,지역 상관없이 다음과 같은 문법형태를 취합니다.
**'컴포넌트 이름':'컴포넌트 내용'**

***1. 전역 컴포넌트 등록***

**Vue.component('컴포넌트이름', 컴포넌트 내용);**

```html
...
<body>
<div id="app">
    <app-header></app-header>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    
    Vue.component('app-header', { 
        template: '<h1>Header</h1>' //template: 캄포넌트가 표현되는 마크업이나 스타일 (화면의 html 태그 같은거)
    }); //app-header라는 태그를 인스턴스 영역에 넣어주면 이 컴포넌트 내용이 반영이 됨
    var vm = new Vue({
        el: '#app',
    })
</script>
</body>
```
- 전역컴포넌트는 실제로 쓰이는 일이 거의 없습니다. 따라서, 전역으로 쓰여야 하는 컴포넌트만 라이브러리를 사용해서 등록합니다.
- 여러 인스턴스에 등록할 때 유용합니다. (하지만 대부분 인스턴스 하나로 사용합니다.)
    - ex) #app2에 <app-header></app-header> OK

***2. 지역컴포넌트 등록***
```
components: {
'컴포넌트이름': 컴포넌트 내용
}
```
```html
...
<body>
<div id="app">
    <app-footer></app-footer>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var vm = new Vue({
        el: '#app',
        components: {
            'app-footer' : {
                template: '<footer>Footer</footer>'
            }
        }
    });
</script>
</body>
```
- 서비스를 만들 때 가장많이 쓰이는 컴포넌트 등록법입니다.
- 인스턴스 한곳에서 정의하기 떄문에 전역 컴포넌트에 비해 가독성이 좋습니다.(어떤 컴포넌트가 등록되었는지 바로 파악)
- 하지만 여러 인스턴스에 컴포넌트를 등록해야 할때는 코드의 중복이 생겨 비효율적입니다. (계속 등록해줘야 하기 때문에)

## 컴포넌트 통신 방식
![](/img/component-2.png ':size=400x300')<br>
컴포넌트는 고유한 데이터 유효범위를 갖습니다. 즉, 데이터의 범위는 데이터의 컴포넌트가 됩니다. 하지만 각 컴포넌트간에 데이터 전달(공유)를 위해선 다음 두 방법을 사용할 수 있습니다.

**props, event** 방식

상위컴포넌트(위) -> 하위컴포넌트(아래) : props <br>
하위컴포넌트(아래) -> 상위컴포넌트(위) : event

### 컴포넌트 통신 규칙이 필요한 이유는? 🧐 ***-> props, event를 만든 이유***
***규칙이 없다면?***<br>
특정 컴포넌트의 변화에 따라 나머지 컴포넌트가 유기적으로 데이터를 주고받았을때 데이터의 방향을 예측하기 어렵습니다. 이는 즉, 버그를 추적하기 어렵다는 단점이 있습니다.
따라서 데이터의 흐름을 추적할 수 있기위한 장치가 필요하게 되었습니다(?)

### ***Props***
- 상위 컴포넌트에서 하위컴포넌트로 데이터를 전달하기 위한 방법입니다.
```html
 <하위컴포넌트이름 v-bind:props 속성이름="상위컴포넌트의 데이터이름"></하위컴포넌트이름>
```
 ***예시***
```html
...
<body>
<div id="app">
    <app-header v-bind:propsdata="message"></app-header>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var appHeader = {
        template: '<h1>{{propsdata}}</h1>',
        props: ['propsdata']
    };
    new Vue({
        el: '#app',
        components: {
            'app-header': appHeader
        },
        data: {
            message: 'hi', //root 컴포넌트에서 관리하는 데이터,
            num: 10
        }
    })
</script>
</body>
```

    ***코드 파악하기***
```html
<app-header v-bind:propsdata="message"></app-header>
```
    - 상위 컴포넌트 `(현재 root)`에 있는 `message`라는 데이터를 app-header라는 하위 컴포넌트의 `propsdata`에 전달합니다.
    

```javascript
    var appHeader = {
            template: '<h1>{{propsdata}}</h1>',
            props: ['propsdata']
        };
```
   - 상위에서 전달받을 데이터를 받기 위해선 `props` 속성을 사용해야합니다.
   - 전달받을 데이터는 여러개가 될 수 있기 때문에 배열로 선언합니다. 
   - `{{데이터 이름}}` 을 사용하면 데이터 값이 바로 바인딩 됩니다.
    
### ***Event***
- 하위 컴포넌트에서 상위 컴포넌트로 이벤트발생(데이터 전달도 이벤트)할 때 쓰이는 방법입니다.
```html
  <이벤트를_발생할_하위컴포넌트 v-on:하위컴포넌트에서 발생한 이벤트 이름="상위컴포넌트의 메서드 이름"></이벤트를_발생할_하위컴포넌트>
```

***예시***
```html
...
<body>
<div id="app">
    <app-content v-on:add="addNumber"></app-content>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script>
    var appContent = {
        template: '<button v-on:click="addNum">add</button>',
        methods: {
            addNum: function () {
                this.$emit('add',10);
            }
        }
    };

    new Vue({
        el: '#app',
        components: {
            'app-content': appContent
        },
        methods: {
            addNumber: function (num) {
                this.num += num;
            }
        },
        data: {
            num: 0
        }
    })
</script>
</body>
```
 ***코드 파악하기***
```html
<app-content v-on:add="addNumber"></app-content>
```
   - `add`는 app-content에서 발생한 이벤트를 의미합니다.
   - `addNumber`는 이벤트가 발생했을 때 상위 컴포넌트(현재 root)에서 실행되는 함수를 의미합니다.
       - 10이라는 인자를 같이 보내줬는데 addNumber만 쓰는 이유는?
          - 암묵적으로 vue에서 함수에 10이라는 값을 상위 컴포넌트 함수에 전달하기 때문입니다. 

```javascript
 var appContent = {
        template: '<button v-on:click="addNum">add</button>',
        methods: {
            addNum: function () {
                this.$emit('add',10);
            }
        }
    };
```
   - `v-on:click="실행시킬 함수이름"`은 click 이벤트가 발생했을 때 특정함수를 실행시키라는 문법입니다.
        - click 외에도 다양한 이벤트를 넣을 수 있습니다.
   - `this.$emit('이벤트의 이름',전달할 인자(인자가 없을때는 생략))`
        - ***위 코드에서는 add 라는 이벤트를 발생시키겠다. 이 때 10을 상위 컴포넌트에 전달을 하겠다***라는 문법입니다.
    
***cf) this***<br>
- 객체안에서 객체의 속성에서 다른 속성이나 어떤거를 가리킬때 쓰임 **this**는 해당 오브젝트를 바라봅니다.<br>
- `this.$emit` 에서의 this는 appContent를 의미합니다.
    
### 같은 레벨에서의 컴포넌트 통신방식
- 같은 레벨에서의 컴포넌트는 데이터를 바로 전달 할 수 없기 때문에 하위 -> 상위 -> 하위 로 데이터를 전달합니다.<br>
- 즉, 하위 ----> 상위를 위해 event 를 거친 후, 상위 ----> 하위를 위해 props를 해야합니다.<br>
![](/img/component-3.png ':size=400x280')<br>
