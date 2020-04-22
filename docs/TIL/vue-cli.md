# Vue CLI
- command line interface : 명령어를 통한 특정액션을 수행하는 도구(명령어 실행 도구)

### 다운로드
- node는 10 이상
- npm은 6 이상
```
node -v
npm -v
```
npm install -g @vue/cli 또는 yarn global add @vue/cli

Permission 에러? 파일 쓰기 권한이 없어서 발생하는 문제 -> sudo 를 붙이자!
/usr/local/lib/node_modules 에 저장됨 (Mac 기준)

### 기존 CLI vs 최신 CLI
- 기존 (Vue CLI 2.x)
    - vue init '프로젝트_이름(템플릿유형)' '프로젝트_폴더위치'
    - vue init webpack-simple '프로젝트 폴더위치'
    
- 최신 (Vue CLI 3.x)
    - vue create '프로젝트 폴더위치'

