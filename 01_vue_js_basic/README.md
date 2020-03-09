# 개요
Vue.js를 처음 접할 때 도움을 주는?

# 대상
jQuery로 화면/데이터를 컨트롤해본

# 특징
- Javascript Framework
  - js로 화면(html)을 그림
- Virtual DOM
  - 화면의 데이터를 변경하려면, 화면 변경(+ 데이터 변경)이 아닌 데이터만 변경하고 화면의 변경은 프레임워크에 맡기는 방식으로
  - (일반적으로 직접 화면을 컨트롤할 일이 없어짐)
- 템플릿 문법 지원
  - html 및 attribute에 문법 사용 가능(jsp 생각하면 됨.. react 배워봤으면 jsx)

# Why Vue.js?
- **동적인 데이터를 추가적인 작업 없이 그리기(render) 가능**
- 구조의 획일화
  - new Vue({ /* ... */}) 안에 js들이 존재
  - Vue CLI 환경에서는 .vue 파일에 html(template), css, js를 하나로 관리(컴포넌트화)하는 게 가능
## [비교](./compare.html)
- 간단한 화면 출력 및 이벤트 연결
- 템플릿 문법을 통한 vue 데이터 출력, 반복문 사용

# 일단 직접 사용해보기
## [채팅창 같은 화면?? 만들기](/sample_01.html)
1. 파일을 새로 생성하여 vue 라이브러리를 include한다.([sample_init.html](./sample_init.html) 파일 참고 또는 복사)
```html
<!-- 개발버전, 도움되는 콘솔 경고를 포함. -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```
1. 페이지를 컨트롤 할 vue 객체 영역을 선언한다. 여기서는 페이지 전체를 사용하고, id로 page를 지정한다.
```html
<body>
  <div id="page"></div>
</body>
```

1. page를 구성할 html을 template에 작성하고 렌더한다.
```html
<!-- 보통 /body 위에 script를 선언하면 됨 -->
<script>
  var Page = new Vue({
    // 전통적인 + 방식 또는 역슬래시(\)로 줄바꿈을 표시해도 됨
    // `(backtick, grave accent)를 사용해도 OK. 단 es6 문법을 지원하는 브라우저여야 함
    template: [
      '<div>', // 내부의 객체는 하나의 태그로 감싸여 있어야 한다.(여러개면 에러남) 나중엔 vue-fragment라는 라이브러리를 쓸 거임
      '  <h1>Vue.js로 만들어진 페이지입니다.</h1>',
      '  <div style="width: calc(100% - 22px); border: 1px solid black; padding: 5px 10px;">', // css는 파일로 빼도 됨
          // 여기에 과거항목 출력
      '  </div>',
      '  <input type="text" placeholder="문장을 입력하세요" style="width: calc(100% - 180px);">',
      '  <button type="button" style="width: 150px;">입력(Enter)</button>',
      '</div>'
    ].join('')
  })
  Page.$mount('#page')
</script>
```
![화면](/img/2020-03-09-13-15-32.png)

1. 각 element와 연동할 데이터를 생성 및 v-model 및 v-for로 연결시킨다.
```javascript
  var Page = new Vue({
    template: [
      '<div>',
      '  <h1>Vue.js로 만들어진 페이지입니다.</h1>',
      '  <div style="width: calc(100% - 22px); height: calc(100vh - 145px); border: 1px solid black; padding: 5px 10px;">',
      '    <div v-for="(item, i) in logList">{{ item }}</div>', // v-for에 data의 logList 연결
      '  </div>',
      '  <input type="text" v-model="newInput" placeholder="문장을 입력하세요" style="width: calc(100% - 180px);">',  // v-model에 data의 newInput 연결
      '  <button type="button" style="width: 150px;">입력(Enter)</button>',
      '</div>'
    ].join('')
    // 사용할 데이터
    // data 함수는 값이 바뀔 때마다 동적으로 호출되므로, 함수에서 리턴하는 방식으로 구성해야 실시간 반영이 된다
    // 나중에 데이터가 화면에서 바뀌지 않으면 백퍼 이거 문제
    // 예> data: { a: 1, b: 1 } 
    data: function() {
      // logList: 과거에 입력한 데이터 리스트
      // newInput: 새로 입력하는 데이터
      return {
        logList: [],
        newInput: ''
      }
    }
  })
```
  - 이런식으로 값을 입력해가며 화면에 출력 잘 되는지 확인해도 됨
  ![바인딩 테스트](/img/2020-03-09-13-32-06.png)

1. 클릭 이벤트 및 엔터 이벤트 달아주기
```javascript
  var Page = new Vue({
    // ..
    // methods에 사용할 함수 선언
    methods: {
      // 현재 newInput의 값을 logList에 add하고 값을 지우는 함수
      addText: function() {
        var text = this.newInput  // data()의 값은 this.데이터명 으로 가져올 수 있다
        if (!text)
          return    // 빈값 채우는거 방지
        
        this.logList.push(text)  // 그대로 값 추가
        this.newInput = ''        // 값 비우기
      }
    }
  })
```
```javascript
  var Page = new Vue({
    // 이벤트를 달아준다
    template: [
      '<div>',
      '  <h1>Vue.js로 만들어진 페이지입니다.</h1>',
      '  <div style="width: calc(100% - 22px); height: calc(100vh - 145px); border: 1px solid black; padding: 5px 10px;">',
      '    <div v-for="(item, i) in logList">{{ item }}</div>',
      '  </div>',
      '  <input type="text" v-model="newInput" @keypress.enter="addText" placeholder="문장을 입력하세요" style="width: calc(100% - 180px);">',  // enter 이벤트를 간략하게 선언 가능
      '  <button type="button" @click="addText" style="width: 150px;">입력(Enter)</button>', // click 이벤트를 연결
      '</div>'
    ].join('')
    // ..
  })
```
1. 세세한 세부작업(안해도 됨)
```javascript
  var Page = new Vue({
    template: [
      // ..
      '<div>',
      '  <h1>Vue.js로 만들어진 페이지입니다.</h1>',
      '  <div style="width: calc(100% - 22px); height: calc(100vh - 145px); border: 1px solid black; padding: 5px 10px;">',
      '    <template v-if="!logList.length">',  // v-if 문법을 사용해 데이터가 없을 때만 출력하는 메시지 추가
      '      <div style="text-align: center; color: #bbb;">입력된 데이터가 없습니다.</div>',
      '    </template>',
      '    <template v-else>',
      '      <div v-for="(item, i) in logList.slice().reverse()">', // 최신 항목을 위로 올리기 위해 .slice().reverse() 처리
      '        <span>{{ item.text }}</span>', // 시간 항목 출력을 위해 object화 및 text에 값 저장하여 출력
      '        <span> [{{ item.time }}]</span>', // 시간 텍스트를 저장하도록 하여 출력
      '      </div>',
      '    </template>',
      '  </div>',
      '  <input type="text" v-model="newInput" @keypress.enter="addText" placeholder="문장을 입력하세요" style="width: calc(100% - 180px);">',
      '  <button type="button" @click="addText" style="width: 150px;">입력(Enter)</button>',
      '</div>'
    ].join(''),
    // ..
    methods: {
      addText: function() {
        var text = this.newInput
        if (!text)
          return
        // 기존 text만 추가하는 대신 object 형태로 시간값을 추가하도록 변경
        this.logList.push({ text: text, time: new Date().toLocaleTimeString() })
        this.newInput = ''
      }
    }
  })
  Page.$mount('#page')
```

# 기본 구조 이해
## template
- Vue 객체에 연동할 template를 문자열 형식으로 지정하여 Vue에 $mount(또는 new Vue({ el })로 연결한 Element)하여 Vue 객체를 화면에 출력할 수 있다
- 기본적인 html 문법 외에 template 문법을 사용할 수 있다
  ```html
    <!-- 보간법: data()의 데이터를 연결 -->
    <span>{{ message }}</span>
    <!-- vue 속성: v-bind, v-on -->
    <input v-bind:value="message">
    <button v-on:click="onClick">클릭</button>
    <!-- 반복문: v-for -->
    <div v-for="o in list" :key="o.id">{{ o.value }}</div>
    <!-- 조건문: v-if, v-else-if, v-else -->
    <div v-if="message !== ''">{{ message }}</div>
    <div v-else>입력된 메시지가 없어요</div>

  ```

## data
- Vue 객체와 연동할 데이터를 data 함수에 선언
- data 함수는 값이 바뀔 때마다 동적으로 호출되므로, 함수에서 리턴하는 방식으로 구성해야 실시간 반영이 된다(나중에 데이터가 화면에서 바뀌지 않으면 백퍼 이거 문제)
- Vue 객체의 함수 내에서 this.데이터명으로 조회 가능
- Vue 객체의 템플릿 내에서 데이터명으로 사용 가능(this가 생략된 형태)
- this.데이터명 = 바꿀값 으로 값을 변경(set)할 수 있으나, 복잡한 구조(배열, 객체)라면 값이 반영되지 않을 수 있다.
  - $set 함수 사용
    - this.$set(상위객체, '바꿀값key', 바꿀값value)

## methods
- Vue 객체에서 사용하는 함수를 methods 객체에 선언
- Vue 객체의 함수 내에서 this.함수명으로 조회 가능
- Vue 객체의 템플릿 내에서 함수명으로 사용 가능(this가 생략된 형태)
  - <button @click="onButtonClicked">button</button>

## lifecycle
- 각 함수는 생명주기명: 으로 사용가능
```javascript
  new Vue({
    // ..
    mounted: function() {
      console.log('hello')
    }
    // ..
  })
```
### beforeCreate
- 생성 전에 호출되는 함수
- 다른 함수들과 다르게 this 객체가 Vue 객체에 매핑되어있지 않다(this 사용불가)
### created
- 생성되고 나서 호출되는 함수
### beforeMount
- 안씀
### mounted
- DOM Element들이 연결(mount)되고 호출되는 함수
- mount되었음 이 화면에 전부 출력되었음 을 의미하지는 않음(일부 레퍼런스($refs)들에 접근하지 못할 수 있음)
  - this.nextTick(function() {}) 형태로 사용해야 각 DOM Element에 문제없이 접근할 수 있음
### beforeUpdate
- 안씀
### updated
- 안씀(watch를 쓰지..)
### beforeDestroy
- Vue 객체가 파괴되기 전 호출되는 함수
- 쓰게되면 beforeCreate, created에서 선언된 기능을 해제(event 해제 등)할 때 보통 사용
### destroyed
- Vue 객체가 파괴된 후 호출되는 함수
- 난 안써봄

## computed(계산된 속성)
- data와 비슷하나, 임의의 값 설정(set) 불가
- 보통 data를 통해 계산된 값을 출력하도록 함
- 함수 형태로 선언, this.함수명으로 조회 가능(실제로는 함수가 아니고 변수로 담김)
```javascript
new Vue({
  data: function() {
    return {
      user: 'm114',
      message: 'Hello!'
    }
  },
  computed: {
    userMessage: function() {
      return message + ' "' + user + '"'  // Hello! "m114"
    }
    // 만약 기능적으로 data.user와 data.message가 바뀌게 되면 바인딩된 값도 바뀜
  }
  methods: {
    changeUserMessage: function() {
      this.user = 'admin'
      this.message = 'Bye!'
    }
    // changeUserMessage 함수를 실행하면 this.userMessage는 'Bye! "admin"'이 됨
  }
})
```

## watch
data(또는 computed)에 바인딩된 값이 변할 때의 이벤트를 정의할 수 있다
```js
new Vue({
  data: function() {
    return {
      message: 'hello'
    }
  },
  watch: {
    // data(computed)내의 데이터명을 함수 key로 한다.
    message: function(value, oldValue) {
      // 변경될 때마다 이전값과 현재값을 출력
      console.log('message', oldValue, '>', value)
    }
  }
})
```

## components
템플릿에서 외부 라이브러리 또는 이전에 선언한 Vue 객체 등 외부 컴포넌트를 사용할 때 선언해줘야 사용 가능하다
```javascript
const _MyCustomizedInput = new Vue({ /* ... */})
new Vue({
  template: '<div><my-customized-input></my-customized-input></div>',  // 외부 컴포넌트 사용(html 권장에 따라 template에서는 kebob-case를 사용하면, components의 key로 선언된 PascalCase를 알아서 바인딩해준다)
  components: {
    MyCustomizedInput: _MyCustomizedInput
  }
})
```

# 참고 및 Thanks to
- [Vue.js(kr) Guide](https://kr.vuejs.org/v2/guide/index.html)
- [Vue에서 컴포넌트 템플릿을 정의하는 7가지 방법](https://github.com/FEDevelopers/tech.description/wiki/Vue%EC%97%90%EC%84%9C-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%ED%85%9C%ED%94%8C%EB%A6%BF%EC%9D%84-%EC%A0%95%EC%9D%98%ED%95%98%EB%8A%94-7%EA%B0%80%EC%A7%80-%EB%B0%A9%EB%B2%95)
