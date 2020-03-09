# 개요
es6 문법 사용가능조건 및 문법 소개

# 사용가능조건
- 브라우저가 es6를 지원할 때
- 객체, 객체.함수 형태의 es6기능은 polyfill을 통해 구 브라우저에서도 쓸 수 있으나, 특수한 문법들은 일반적으로 구 브라우저에서 사용 불가하다
## 간단한 확인
- 사용할 브라우저의 개발자 도구를 열어 쓰고 싶은 기능을 써서 에러가 안나면 됨
- https://caniuse.com/ 에서 검색해보기
## 폴리필 사용
### 자동
1. https://polyfill.io/v3/url-builder/
1. 사용하고 싶은 기능을 체크
1. URL을 복사하여 script로 로드
(실제 운영 시에는) polyfill.io 서버를 구축하는게 나을걸..

### 수동
1. [Mozila MDN](https://developer.mozilla.org/) {es6기능} 검색
1. polyfill 항목을 찾는다
1. 스크립트를 붙여넣는다

## 폴리필로 해결 불가능한 기능
- ex> array function(() => {}), async/await, backtick(`hello ${message}`) 등
- 이는 js를 트랜스파일링(컴파일 같은거.. 동 레벨의 언어로 변경하기 때문에 컴파일은 아님)해서 해결할 수 있다
- [Babel](https://babeljs.io/)을 사용하여 해결

### Babel을 사용하기 위한 필수 조건
- npm으로 스크립트를 빌드하는 구조여야 함
- 스크립트를 html에서 include 하며, 따로 npm을 사용하지 않는 **기존 방식으로는 불가능**하다고 보면 됨([온라인 트랜스파일러](https://babeljs.io/repl)는 있지만...)
- [webpack](https://webpack.js.org/), [parcel](https://parceljs.org/)(이 경우엔 내장되어있음) 등을 거쳐 빌드하는 방식일 경우 사용 가능
  - Vue CLI는 webpack 기반이므로 일부 설정값만 설정해주면 알아서 변환해준다

# es6
위에서 이야기한 특수한 문법 위주로 설명함
(개발자 도구에서 칠 때 var 대신 let, var로 사용하면 중복 선언때문에 에러떠서 귀찮으니까 var로 선언했지만 실제로는 var 또는 let을 사용합니다)

## 화살표 함수(arrow function)
```javascript
var arrowFunction = (value) => 'Hello ' + value
var basicFunction = function (value) {
  return 'Hello ' + value
}
var arrowFunctionWithoutReturn = (value) => { console.log('Hello ' + value) }
var basicFunctionWithoutReturn = function (value) {
  console.log('Hello ' + value)
}
```
### 특징
arrow function에서는 this, arguments를 바인딩하지 않음
=> vue.js에서 data 함수와 methods의 함수를 사용할 때 this를 사용하면 undefined가 뜨는 문제가 있음
=> arguments는 전개 구문과 연동하면 사용 가능
```js
var printArguments = (...arg) => console.log(...arg)
```

## [전개 구문(Spread syntax)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
array 및 object의 값을 전개하는 구문
### Array
```js
var oldArray = [1, 2, 3]
var newArray = [...oldArray]  /* 각 값이 복사됨 = clone */
```
### Object
```js
var oldObject = { id: 'aaa', name: 'bbb', desc: 'ccc' }
var newObject = { ...oldObject, desc: 'ddd' }   /* { id: 'aaa', name: 'bbb', desc: 'ddd' }, 뒤에 선언된 값은 추가되거나 덮어씌워짐 */
```
### Array with Function
```js
var multiArgFunction = (arg1, arg2, arg3) => {
  console.log('arg1 is', arg1)
  console.log('arg2 is', arg2)
  console.log('arg3 is', arg3)
}
var arrayArg = [1, 2, 3]
multiArgFunction(arrayArg)
// arg1 is [1, 2, 3]
// arg2 is undefined
// arg3 is undefined
multiArgFunction(...arrayArg) // == multiArgFunction.apply(null, arrayArg)
// arg1 is 1
// arg2 is 2
// arg3 is 3
```

## object 변수명으로 속성 정의
```js
var id = 'm114'
var name = '김철수'
var desc = '반갑습니다'
var user = { id, name, desc } /* {id: "m114", name: "김철수", desc: "반갑습니다"} */
// var user = {
//   id: id,
//   name: name,
//   desc: desc,
// }
```
## object의 key로 변수 사용
```js
var key = 'message'
var value = 'hello'
var obj = { [key]: value } /* { message: 'hello' } */
var obj2 = { ['2' + key + '2']: value + '2' } /* { 2message2: 'hello2' } */
```

## [구조 분해 할당](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
```js
// 배열 구조 분해
var list = ['name', 'm114']
var [key, value] = list
// var key = list[0]
// var value = list[1]

// 객체 구조분해
var data = { name: 'm114', message: 'hello' }

// 이름 그대로 받기
var { name, message } = data
// var name = data.name
// var message = data.message

// alias(별칭) 줘서 받기
var { name: nameInData, message: messageInData } = data
// var nameInData = data.name
// var messageInData = data.message

// 복잡한 구조
var nestedData = { page: { totalCount: 15, currentPage: 1 }, dataList: [{ id: 1, value: 'aa' }] }
var { page = { totalCount, currentPage: page }, list = [] } = nestedData
// var totalCount = nestedData.page.totalCount
// var page = nestedData.page.currentPage
// var list = nestedData.list || []                   // nestedData.list가 없으므로 디폴트로 선언한 빈 배열 사용
```

## function in object
- Vue에서 mounted 등에 쓸 때 유용하다(이쪽은 this 사용 가능)
- arrow function하고는 같이 사용 불가
```js
var obj = {
  hello() {
    console.log('hello')
  }
  // hello: () => { console.log('hello') }
}
```
### cf> function in class
리액트 쓸 때 class에서 써봄
```js
class Hello {
  // this.message = function() { console.log('hello') }
  message = () => console.log('hello')
}
```

## [기본 매개변수](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/Default_parameters)
```js
var printMessage = (user = 'Anonymous') => { console.log('Hello ' + user) }
printMessage('m114')    // Hello m114
printMessage()          // Hello Anonymous
printMessage(undefined) // Hello Anonymous
printMessage('')        // Hello 
```

## `(backtick)
- 문자열 출력용 값(', "같이)
- 문자열의 연결을 용이하게 함(여러 줄일때, +하거나 역슬래시를 다는 짓을 안해도 됨)
- ${value} 형태로 변수 사용 가능
```js
var user = 'm114'
var message = `Hello ${user}!
Glad to meet you.`
```
- (참고만)함수 실행 및 인자로 사용 가능
```js
var test = (...arg) => arg
var doTest = test`hello`  // => ['hello', raw: 'hello']
```

## [async/await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/async_function)
- 전제 사항: Promise를 알고 있어야 함
- Promise를 동기 함수처럼 쓰기 위한 문법임
- await를 쓰기 위해 async로 함수를 감싸고, resolve 받는 데이터를 await의 변수로 받는 형태로 사용
```js
var somePromise = () => new Promise(function(resolve, reject) {
  setTimeout(() => { resolve(true) }, 5000)
})

// classic Promise
var classicPromise = () => {
  somePromise().then(function(value) {
    console.log('classicPromise: resolve', value) // 5초뒤에 resolve true
  }).catch(function(e) {
    console.error(e)
  })
}
classicPromise()

// Async/Await Promise
var asyncAwaitPromise = async () => {
  try {
    var value = await somePromise()
    console.log('asyncAwaitPromise: resolve', value) // 5초뒤에 resolve true
  } catch (e) {
    console.error(e)
  }
}
asyncAwaitPromise()
```

# es6 vs commonJS..in browser
- webpack 등을 이용해 프론트엔드(js for browser) 소스를 빌드해서 쓰게 되면,
html에서 사용할 js를 include 하지 않고, js 내에서 사용할 js를 import 받을 수 있게 된다
- 이와 별개로 node.js(서버용 javascript) 환경에서는 commonJS라는 생태계를 사용해 표준을 정립하게 된다.
- commonJS의 주요 문법 중 module의 require과 exports가 있는데, 이는 es6의 import와 export로 사용 가능하다
```js
// commonJS
// import
const lib = require('/js/lib')
// export
module.exports = lib
module.exports = {
  lib: lib
}
```
```js
// es6
// import
import lib from '/js/lib'
// export
export { lib }
export default {
  lib: lib
}
```
- 프론트엔드 환경에서 쓴다면, 요즘은 주로 es6을 사용하고 빌드하므로 (잘 모르겠으면) 뒤에거만 알면 됨

## 참고
[requirejs](https://requirejs.org/) - browser 환경에서 js module을 사용하기 위한 프로젝트, amd 방식 중 대표적인 라이브러리
umd 방식 - 위의 amd 방식 및 commonjs/es6 방식에서 모두 module을 import 받을 수 있도록 처리(빌드)한 방식
[자바스크립트 모듈 제공을 위한 AMD, CommonJS 그리고 RequireJS 소개](https://blog.pigno.se/post/157992405313/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%AA%A8%EB%93%88-%EC%A0%9C%EA%B3%B5%EC%9D%84-%EC%9C%84%ED%95%9C-amd-commonjs-%EA%B7%B8%EB%A6%AC%EA%B3%A0-requirejs-%EC%86%8C%EA%B0%9C)