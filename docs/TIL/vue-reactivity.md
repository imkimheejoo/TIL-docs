# Vue js의 핵심기능인 Reacitivity(반응성)

### 기존방식
일반적인 변수의 기본적인 동작으로는 **객체의 할당과 접근**이 있습니다. <br>
![](/img/reactivity-1.png)<br>
하지만 이외에도 특정 변수의 동작, 객체의 특정 속성의 동작을 재정의하고 싶을 때는  `Object.defineProperty()` 을 사용하면 됩니다.

***사용법***
```javascript
    Object.defineProperty(대상객체,객체의 속성, {
    // 정의할 내용
    });
```

### JS의 데이터와 DOM의 바인딩 데이터를 일치시킬 수는 없을까🤔
JS의 변수에 값이 변경될때 일일히 기존코드처럼 `div.innerHTML` 하는것에 불편을 느끼기 시작합니다.
이를 해결하기 위해 우리는 ` Object.defineProperty`을 JS의 데이터와 바인딩 데이터를 일치시킬수 있게됩니다.<br>
***기존 코드***
```html
<!--일반 웹개발은 html ,css, js로 웹페이지를 구성가능-->
...
<body>
<div id="app">
</div>
<script>
    var div = document.querySelector('#app');
    console.log(div);
    var str = 'Hello Div!';
    div.innerHTML = str;     //해당 태그나 돔의 정보를 조작

    str = 'Hello Word!'; //    str의 정보를 바꿈
    div.innerHTML = str; // 기존에는 바뀐 문자열을 다시 DOM 에 조작해야함
</script>
</body>
```
- str의 값이 변경되어도, str을 바인딩 하는 DOM은 `div.innerHTML = str;` 코드를 추가해야 변경된 값으로 바인딩 됩니다.

***reactivity 적용***
```html
...
<body>
<div id="app">
</div>
<script>
    var div = document.querySelector('#app');
    div.innerHTML = 'Hello world';
    var viewModel = {};
    Object.defineProperty(viewModel, 'str', {
        get: function () {
            console.log('접근');
        },
        set: function (newValue) {
            console.log('할당', newValue);
            div.innerHTML = newValue;
        }
    });
</script>
</body>
```
![](/img/reactivity-2.gif)<br>
***Object.defineProperty***
- 객체의 동작을 재정의 하는 API입니다. 현재 예제에서는 viewModel에있는 str 속성에 대해 접근 동작과 할당동작을 재정의 하고 있습니다. 

**Vue.js 는 이러한 성질을 우리의 구현없이 제공해주는 프레임워크인데요, 이 성질을 Reactivity라고 합니다.**

### Reactivity 
- 데이터의 변화를 라이브러리에서 감지해서 알아서 화면을 자동으로 그려주는 것
- Vue.js 는 reactivity 라는 특성을 이용하여 변수, 속성의 변화를 감지하고 바로 DOM에 반영을 하는 장점을 가집니다.