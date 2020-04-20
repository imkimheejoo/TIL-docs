# 템플릿 문법
- 뷰로 화면을 조작하는 방법
- 데이터바인딩, 디렉티브로 나뉨 

***데이터 바인딩 예시***
```html
<div>{{message}}</div>
```

***디렉티 예시***
- html 태그에서 class, id, 같은 표준 속성을 제외 v- 라고 붙는 속성을 말함
```html
<div v-on:click...></div>
```

- watch vs computed
- r공통점: 둘다 데이터의 변화에 따라 일어나는 행동
- 차이점
    - watch
        - 무거운 로직 -> 매번 실행되는게 부담스러운 애들 (데이터 요청)에 적합
    - computed
        - 단순 값의 계산, validation
        - computed 로 쓰는 것을 권장
        - 캐싱 지원, 튜닝
 ### computed
 