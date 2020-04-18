### docsify 관련 문서
- [docsify 화면 설정](https://docsify.js.org/#/configuration)
- [docsify 문서 작성](https://docsify.js.org/#/helpers)
### docsify 를 깔았는데도 not Found?
- `npm i docsify-cli -g` 을 했는데도 되지 않을 경우 입니다.
```
> docsify serve docs 
zsh: command not found: docsify
```
- 원인 : zsh이 업데이트 하고 나서 생긴 에러(추측..)
- 해결방법 : `export PATH=$PATH:$(npm prefix -g)/bin` 실행하면 잘될 것!
    - 하지만 터미널 끄면 다시..원래대로..

### 마크다운에 이모지를 쓰는법 🧚🏻‍♂️
- `command + control` 키를 누르고 `space`