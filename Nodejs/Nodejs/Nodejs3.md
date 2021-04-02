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

전역 객체라는 점을 이용하여 파일 간에 간단한 데이터를 공유

__globalA.js__

```javascript
module.exports = () => global.message;
```

**globalB.js**

```javascript
const A = require('./globalA');

global.message = '안녕하세요';
console.log(A());
```

globalA.js는 global.message 값을 반환

globalB.js는 global객체에 속성명이 message인 값을 대입하고 globalA 모듈의 함수를 호출

```
$ node globalB

안녕하세요
```

__global 객체가 많아질 경우 유지 보수에 어려움을 겪을 수 있음__

## console

console도 노드에서는 global 객체 안에 들어있음

보통 디버깅을 위해 사용

**로깅 함수**

- console.time(레이블): console.timeEnd(레이블)과 대응되어 같은 레이블을 가진 time과 timeEnd 사이의 시간을 측정
- console.log(내용): 평범한 로그를 콘솔에 표시
-  console.error(에러 내용): 에러를 콘솔에 표시합니다.
- onsole.table(배열): 배열의 요소로 객체 리터럴을 넣으면, 객체의 속성들이 테이블 형식으로 표
- console.dir(객체, 옵션): 객체를 콘솔에 표시할 때 사용. 첫 번째 인수로 표시할 객체를 넣고, 두 번째 인수로 옵션을 넣음. 옵션의 colors를 true로 하여 가독성 증가. depth는 객체 안의 객체를 몇 단계까지 보여줄지를 결정(default=2)
- console.trace(레이블): 에러가 어디서 발생했는지 추적. 일반적으로 에러 발생 시 에러 위치를 알려주므로 자주 사용하지는 않지만, 위치가 나오지 않는다면 사용

__console 객체에는 새로운 메소드가 계속 추가__

## 타이머

타이머 기능을 제공하는 setTimeout, setInterval, setImmediate는 노드에서 window 대신 global 객체 안에 들어있음

- setTimeout(콜백 함수, 밀리초): 주어진 밀리초(1,000분의 1초) 이후에 콜백 함수를 실행
- setInterval(콜백 함수, 밀리초): 주어진 밀리초마다 콜백 함수를 반복 실행
- setImmediate(콜백 함수): 콜백 함수를 즉시 실행

타이머 함수들은 모두 아이디를 반환. 아이디를 사용하여 타이머 취소.

- clearTimeout(아이디): setTimeout을 취소
- clearInterval(아이디): setInterval을 취소
- clearImmediate(아이디): setImmediate를 취소

__setTime(콜백, 0)은 사용하지 않도록 권장__

## \_\_fimename, \_\_dirname

- \_\_filenaname: 파일 경로 및 파일명 표시
- \_\_dirname: 파일 경로 표시

## module, exports, require

모듈을 만들 때 `module.exports`가 아닌 exports 객체로 만들 수 있음

__var.js__

```javacript
exports.odd='홀수입니다'
exports.even='짝수입니다'
```

__exports 객체 사용시__

객체만 대입 가능하므로 주의

**exports 객체와 module.exports를 동시에 사용하지 않을 것**

