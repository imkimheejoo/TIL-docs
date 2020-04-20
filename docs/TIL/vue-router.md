# Vue Router

## 라우터란?
우리는 사용자가 요구하는 url에 따라 화면을 다르게 노출시켜야합니다. 이를 구현하기 위해서는 라우터를 사용하는데요, <br>
라우터는 한 컴포넌트에서 url의 변경에 따라 페이지가 다르게 노출되는 기능을 제공합니다. 즉, **페이지 이동(변경)**에 쓰이는 뷰 라이브러리를 뜻합니다. <br>
우리는 라우터를 사용하기 위해 `<router-view>`와 `<router-link>`가 의미하는 바를 파악해야합니다.

### router-view
라우터 뷰는 라우팅에서 특정 url에 등록된 컴포넌트를 노출시킬(뿌릴?) 영역을 표시하는 기능입니다. <br>
해당 url에 라우팅 컴포넌트(템플릿)를 뿌리기 위해선 하지만 ***Vue 인스턴스에 VueRouter를 등록***해야 사용이 가능합니다. (밑에 등록방법 section 을 봐주세요.) <br>
```html
<router-view></router-view> 
```

### router-link
라우터 링크는 라우터에서 페이지 이동을 위한 link 태그입니다.<br>
예를들어, 로그인 버튼을 누르면 /login url을 실행시키는 역할을 합니다.<br>
따라서 라우터 링크는 화면 elements에 `<a href="라우터링크에 설정한 url">라우터링크에서 설정한 내용</a>`로 나타납니다.
```html
<router-link to="/login">Login</router-link> <!--  == <a href="/login">Login</a> -->
```
하지만 라우터링크는 `<a>`와 달리 라우터에서 페이지 이동을 하기위한 기능이기 때문에 /login url에 대한 ***라우팅 기능이 등록***되어있어야 합니다. (밑에 등록방법 section 을 봐주세요.)

### 라우팅 등록방법
***예시***
```html
...
<body>
<div id="app">
    <div>
        <router-link to="/login">Login</router-link> 
    </div>
    <router-view></router-view>
</div>
<!--순서중요: 1. vue 2. router-->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script> <!--vue Router 를 사용하기 위해 cdn으로 다운-->
<script>
    var LoginComponent = {
        template: '<div>login</div>'
    };
    var router = new VueRouter({
        mode:'history', 
        routes:[
            {
                path: '/login',
                component: LoginComponent
            }
        ]
    });

    new Vue({
        el: '#app',
        router: router
    });
</script>
</body>
```
***코드 분석***

분석 순서대로 동작과정을 이해하는 것을 추천드립니다.

1. 어떤 url에 어떤 page를 노출시킬지 정의하자!
```javascript
var router = new VueRouter({
        mode:'history',
        routes:[
            {
                path: '/login',
                component: LoginComponent
            }
        ]
    });
```
- VueRouter 도 Vue와 마찬가지로 키:밸류 형식으로 구현합니다.
- `mode:'history'` : 페이지에서 로그인버튼을 누르면 /login 으로 가도록 설정했지만, vue에서느 서버.../#/login으로 가게되는데, 이를 허용하지 않는 옵션입니다.
- `routes` : **현재 페이지의 라우팅 정보를 등록하기 위한 옵션입니다.**
    - 라우팅 정보?
        - 어떤 url로 이동하면 어떤 page가 출력되야 하는가에 대한 정보
    - 현재 예시에선 하나의 라우팅정보만 담았지만 다른 url에 대한 정보를 등록 가능합니다. (routes가 배열인 이유!)
    - `path` : 페이지가 어떤 url에 뿌려질지에 대한 정보입니다.
    - `component` : `path` 시 노출되는 컴포넌트를 연결하는 속성입니다. 

2. 라우팅 정보가 Vue 인스턴스에서 쓰일 수 있도록 등록하자!
```javascript
new Vue({
        el: '#app',
        router: router
    })
```
- Vue Router를 웹에서 쓰기 위해선 화면에 대한 기능을 정의하는 Vue인스턴스에 등록해야합니다. 따라서 `router` 옵션으로 VueRouter 인스턴스를 등록합니다.

3. DOM의 어떤 영역에 url에 따른 페이지를 노출시킬지 결정하자!
```html
<div id="app">
    <router-view></router-view>
</div>
```
- 현재 url이 /login 이라면 이 라우터 뷰는 `<div>Login</div>`으로 바뀝니다.

4. a tag 를 누르면 라우팅에 등록된 컴포넌트를 노출시켜보자! (선택?)
```html
<div id="app">
    <div>
        <router-link to="/login">Login</router-link> 
    </div>
    <router-view></router-view>
</div>
```
- 주소에 url을 바꿔 router view 컴포넌트 내용을 바꿀 수 있지만, 사용자의 편의를 위해 어떤 요소를 클릭 시 router-view를 바꾸기 위해 쓰이는 방법입니다.
- `to` : 이동할 url 주소를 적습니다.
- router-link는 화면에 `<a href="/login">Login</a>`로 나타내집니다.
    

