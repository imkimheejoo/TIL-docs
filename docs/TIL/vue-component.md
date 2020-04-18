# Vue.js Component

## 컴포넌트
![](/img/component-1.png ':size=600x300')<br>
화면의 영역을 구분한 것을 말합니다.(그림 참고)<br>
따라서 연한 회색칸에 여러 진한회색칸을 가지게 되는 계층관계도 나타낼 수 있습니다.
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

