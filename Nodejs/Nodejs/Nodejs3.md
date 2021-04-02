# 노드 기능

노드와 상호작용, 자바스크립트 파일 실행

객체와 모듈 사용방법

## REPL 사용하기

자바스크립트는 스크립트 언어이므로 미리 컴파일하지 않아도 즉석에서 코드 실행 가능

REPL은 Read, Eval, Print, Loop를 의미

우선 Ctrl+`을 눌러 터미널(콘솔) 실행

```
$node
```

프롬프트가 > 모양으로 바뀌었다면 코드 입력 가능

REPL을 종료하려면 Ctrl+C를 두 번 누르거나 .exit 입력

## JS파일 실행하기

긴 코드의 경우 REPL이 아닌 JS파일을 만들어 실행하는 것이 좋음

```javascript
function helloWorld() {
  console.log('Hello World');
  helloNode();
}

function helloNode() {
  console.log('Hello Node');
}

helloWorld();
```

`$ node helloWorld` 를 입력해 위의 파일 실행

확장자(.js)는 생략 가능

__REPL이 아닌 콘솔에 입력해야함__

## 모듈로 만들기

자바스크립트와 다르게 노드는 코드를 모듈로 만들 수 있음

__크롬 60 버전부터 브라우저에서도 모듈 사용__

- 모듈: 특정한 기능을 하는 함수나 변수들의 집합, 하나의 프로그램이면서 다른 프로그램의 부품

__var.js__

```javascript
const odd='홀수입니다'
const even='짝수입니다'
module.exports={
    odd,
    even,
};
```

__func.js__

```javascript
const{odd,even}=require('./var');

function checkOddOrEven(num){
    if(num%2){
        return odd;
    }
    return even;
}

module.exports=checkOddOrEven;
```

__index.js__

```javascript
const { odd, even } = require('./var');
const checkNumber = require('./func');

function checkStringOddOrEven(str) {
  if (str.length % 2) { // 홀수면
    return odd;
  }
  return even;
}

console.log(checkNumber(10));
console.log(checkStringOddOrEven('hello'));
```

module.exports에 변수, 객체or함수를 대입

require을 통해 모듈을 불러옴

콘솔에 실행

```
$ node index
짝수입니다
홀수입니다
```

실행 과정은 다음과 같음

<img src="https://thebook.io/img/080229/095.jpg" width="60%">

### 참고: ES6 모듈

```javascript
import { odd, even } from './var';

function checkOddOrEven(num) {
  if (num % 2) { // 홀수면
    return odd;
  }
  return even;
}

export default checkOddOrEven;
```

require와 module.exports가 import, export default로 바뀜

노드 9 버전부터 ES6 모듈 사용 가능하지만 확장자를 js로 지정해야함

package.json=>type:"module" 속성을 넣어 js로 사용 가능

## 노드 내장 객체

노드에서는 내장 객체와 내장 모듈을 제공

### global 객체

브라우저의 window와 같은 전역 객체

require은 global.require에서 global 생략

console은 global.console에서 global 생략

__노드에서 window와 document 객체는 사용X, DOM이나 BOM이 없기 때문__

REPL을 이용해 내부 확인

```
$ node
> global
<ref *1> Object [global] {
  global: [Circular *1],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] {
    [Symbol(nodejs.util.promisify.custom)]: [Function (anonymous)]
  },
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(nodejs.util.promisify.custom)]: [Function (anonymous)]
  }
}
> global.console
Object [console] {
  log: [Function: log],
  warn: [Function: warn],
  dir: [Function: dir],
  time: [Function: time],
  timeEnd: [Function: timeEnd],
  timeLog: [Function: timeLog],
  trace: [Function: trace],
  assert: [Function: assert],
  clear: [Function: clear],
  count: [Function: count],
  countReset: [Function: countReset],
  group: [Function: group],
  groupEnd: [Function: groupEnd],
  table: [Function: table],
  debug: [Function: debug],
  info: [Function: info],
  dirxml: [Function: dirxml],
  error: [Function: error],
  groupCollapsed: [Function: groupCollapsed],
  Console: [Function: Console],
  profile: [Function: profile],
  profileEnd: [Function: profileEnd],
  timeStamp: [Function: timeStamp],
  context: [Function: context]
}
```

