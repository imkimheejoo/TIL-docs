# Vue Instance

### 인스턴스란?
로 개발할 때 필수로 생성해야 하는 코드이자 단위입니다.

```javascript
var vm = new Vue();
console.log(vm);
```

### 인스턴스를 만들어야 하는 이유는?(vue를 왜 생성자 함수로 찍어내는가)
- 생성자 함수를 통해서 인스턴스를 만들어 그 안에 속성, API를 설정할 수 있습니다. 예를들어, 다음코드가 있다고 가정해봅시다.
```javascript
function Vue(){
    this.logText = function(){
        console.log('hello');
    }
}
var vm = new Vue();
vm.logText(); //hello가 나옴
```
- 우리는 logText()를 매번 정의할 필요 없이 Vue에 미리 정의한 logText()를 가져다 사용할 수 있습니다.
- 즉, 생성자 함수로 Vue()에서 API와 속성을 정의하면 우리는 가져다 쓰기만 하면 되는 장점을 가지고 있기 때문에 인스턴스를 생성합니다.