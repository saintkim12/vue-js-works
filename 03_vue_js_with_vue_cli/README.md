# 개요
Vue CLI 3 환경을 구축하여 Vue.js를 사용

# 읽기 전에
- script 방식으로 Vue.js 환경에 익숙해진 뒤 사용해보는 게 좋긴 함(아님 npm 생태계에 익숙하거나...)
- 번들러(webpack 등)로 빌드하는 방식이 이상적인 방법이긴 하지만 상황에 따라 최선의 해답이 아닐 수도 있음

# [Vue CLI 3](https://cli.vuejs.org/guide/)
## 장점
- Vue.js를 사용하여 빠르게 개발하기 위한 전체 시스템
- Webpack을 번들러로 사용하여 webpack+@의 기능 이용 가능(빌드, eslint 등 문법체크, scss 등 전처리기...)
- .vue 파일에 대한 번들 처리로 **vue component를 .vue 파일에서 관리 가능**(html(template), css, js 등을 컴포넌트(기능)별로 모으기...)
- SPA(Single Page Application)을 구축하기 용이

## 단점
- npm 생태계 이해 필요
- webpack 이해 필요
- 익숙하지 않은 개발환경 + 세팅 필요
- 공식 문서가 영문임
- eslint가 활성화되어 익숙해지지 않는 이상 빨간줄 때문에 힘들것
- MPA(Multiple Page Application)을 구축해야 한다면 추가적인 학습 필요(webpack의 entry의 개념을 알아야함)

# 기본 환경 설정
VSCode 혹은 기타 에디터(IDE 혹은 에디터 + Command line 환경)
node.js (10.14.2 쓰고있는데 더 높은 버전도 될걸) - [node.js](https://nodejs.org/), [nvm](https://github.com/creationix/nvm) or [nvm-windows](https://github.com/coreybutler/nvm-windows)
git - git 설치 안하고 써본적이 없어서 모르겠는데 git 설치하라고 에러뜨면 git 설치하기

# [Vue CLI](https://github.com/vuejs/vue-cli) 설치
> Vue Cli는 어디까지나 Vue + Webpack을 커스터마이징한 자체 번들러를 갖춘 프로젝트를 **쉽게 생성하는** 것이 주 역할이므로, 다른 프로젝트를 clone 받아서 쓰거나, 자신이 환경을 처음부터 구축해볼 것이 아니라면 굳이 Vue CLI를 설치할 필요는 없음

## npm을 통해 Vue CLI 설치
```bash
# 콘솔 창을 열어서
npm i -g @vue/cli
``` 
## 프로젝트 생성하기
```bash
# 현재 폴더의 하위로 hello-world라는 vue cli 프로젝트가 생성됨
vue create hello-world

# 또는 웹브라우저로 작업하려면
vue ui
```
[공식사이트 설명 참고](https://cli.vuejs.org/guide/creating-a-project.html#using-the-gui)
default를 선택하면 프로젝트가 생성됨
(나중에 익숙해지면 Menually select features를 선택하고 추가할 기능을 선택)
(어느 것을 선택하더라도 npm install, npm remove로 라이브러리를 추가/삭제할 수 있음)

```bash
# 프로젝트 폴더에 들어가 개발서버 실행해보기
cd hello-world
npm run serve
```
![npm run serve](/img/2020-03-10-10-03-34.png)
![localhost:8080](/img/2020-03-10-10-06-09.png)

# 생성된 프로젝트 사용해보기
> 만약 자신이 생성하지 않고 이미 생성된 프로젝트를 사용하는 거여도 상관은 없음
> 만약 VSCode에서 코딩한다면 다음 작업으로 VSCode를 project 폴더로 바로 열 수 있다
  ```bash
  # cd hello-world
  code .   # 환경 설정에 따라 안될 수도 있음
  ```
  아니면 그냥 Open Folder로 열어도 됨

## 폴더 구조 보기
![폴더 구조](/img/2020-03-10-10-17-23.png)
### node_modules
npm 라이브러리가 설치되는 곳
### public
(webpack에서)각 vue 페이지의 대표 페이지(entry) 등 번들링 하지 않을 공통?기본?템플릿? 페이지/파일들을 두는 곳
webpack으로 빌드할 때 여기 있는 파일들을 기초로 빌드함
### src
실제 작업할 소스 파일을 두는 곳
### babel.config.js
babel 설정
### package.json, package-lock.json
npm 라이브러리 명세서(package-lock.json도 저장소에 공유해야 하는 것을 잊지 말고)

## npm run serve
위에서 설명했듯 npm run serve를 사용해 개발 서버를 작동시킬 수 있음
### (참고사항)package.json
- scripts 부분을 보면 npm run 후 실행할 스크립트를 지정한 것을 볼 수 있다
![package.json](/img/2020-03-10-10-37-02.png)
- 나중에 빌드할 때 npm run build를 쓸 거임

## 페이지 작업하기
### (VSCode) 확장프로그램 설치
#### Vetur
vue 파일은 에디터가 일반적으로 그냥 txt로 인식하는데, Vetur을 설치하여 Vue Component를 올바르게 보이게 할 수 있음
![vetur](/img/2020-03-10-10-47-27.png)
#### eslint
에디터에서 문법을 체크하도록 eslint 확장프로그램을 설치
![eslint](/img/2020-03-10-10-46-43.png)
### msg 바꿔보기
> npm run serve를 사용할 시, 작업 사항에 대한 반영이 실시간으로 되므로, **일반적으로** 페이지를 새로고침하지 않아도 반영됨(구조가 복잡해지면 안되는 경우도 있음..)
프로젝트의 구조는 main.js를 통해 App.vue라는 App 컴포넌트를 부르고, 이 안에서 components/HelloWorld.vue라는 컴포넌트를 부르며 msg라는 속성을 전달하는 구조로 되어 있다.
```html
<!-- App.vue -->
<template>
  <div id="app">
    <!-- .. -->
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
export default {
  // ..
  components: {
    HelloWorld
  }
}
</script>
<!-- components/HelloWorld.vue -->
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <!-- .. -->
  </div>
</template>
```
따라서 App.vue의 HelloWorld의 msg 속성을 변경하면 HelloWorld에서 렌더되는 값이 바뀌게 된다
```html
<!-- App.vue -->
<template>
  <div id="app">
    <!-- .. -->
    <HelloWorld msg="한글도 잘 나올까?"/>
  </div>
</template>
```
![msg 변경](/img/2020-03-10-10-59-07.png)

## vue cli 설정 변경하기
[vue-config-js](https://cli.vuejs.org/config/#vue-config-js)
- Vue CLI의 설정을 지정/변경하고 싶다면 root 폴더(package.json이 있는)에 vue.config.js를 만들어야 한다.
```js
// vue.config.js
module.exports = {
  outputDir: './build', // 빌드 시 경로, 기본 ./dist
  publicPath: '/',      // 각 페이지들의 기본 경로(prefix), 각 페이지들이 BASE_URL/subpath/의 하위로 매핑되어야 한다면 '/subpath/'가 되어야 함
}
```
- 자세한 설정은 위 공식 사이트 보고 확인할 것
- 참고로 npm run serve로 실행하는 개발서버(devServer) 세팅도 여기서 함

# 자주 쓰이는 라이브러리 확인
## vue CLI에서 plugin 형태로 제공하는 라이브러리
### [babel](https://babeljs.io/)
모던 브라우저에서 쓰이는 문법이 사용된 파일들을 빌드 시 트랜스파일링(오래된 브라우저에서도 읽을 수 있도록 처리)해주는 라이브러리
### [vue-router](https://router.vuejs.org/kr/)
SPA에서 페이지 이동 처리를 다루는 라이브러리
### [vuex](https://vuex.vuejs.org/kr/)
- 상태 관리 라이브러리(react를 써봤다면 redux, mobx 등을 생각하면 됨)
- 위 예시에서 HelloWorld.vue의 msg를 바꾸기 위해 App.vue를 수정했는데, 이 depth가 깊어지면(컴포넌트들이 깊게 중첩되어있다면) 값들을 관리하기 어려워진다
- 이 상태값들을 한 곳에 담아 관리하는 게 상태 관리 라이브러리들의 기본 개념
### css 전처리기
- Vue CLI(webpack)을 통해 작업하는 프로젝트들은 빌드해서 배포하는 것이 기본 원칙이므로, 빌드 시 필요한 다른 값들을 빌드하는 것이 가능해졌다
- 이 때, 편리한 문법 및 변수 처리가 가능한 scss(sass)를 사용하고 이 파일을 css로 변환하면, 브라우저가 이해할 수 있는 css가 생성된다
- 이 작업을 css 전처리기를 설정하는 것으로 할 수 있다
- (일단 node-sass 사용중)
### eslint
- default로 프로젝트 생성 시 eslint는 기본적인 문법 오류만 잡게 세팅함
- airbnb 스타일과 standard 스타일 등 여러 문법이 있음
- (현재는 standard 스타일을 일부 커스터마이징하여 쓰고 있음)

## 기타 라이브러리
### axios
비동기 요청 처리에 도움을 주는 라이브러리(jQuery의 $.ajax 생각하면 됨)
### lodash
공통 함수들 모아둠
### bootstrap-vue(bootstrap)
bootstrap vue버전
### vuelidate
문법 검사
### ...

# 자주 뜨는 에러
## eslint
eslint를 사용하지 않음으로 하지 않는 이상 한동안 문법검사 기능 때문에 고생할거임
-> 그래도 쓰는 걸 권장... 정 불편한 기능이 있으면 rule을 설정하자(.eslintrc)
