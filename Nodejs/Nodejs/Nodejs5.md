# 노드 기능 3

## 파일 시스템 접근하기

fs 모듈로 파일 시스템에 접근

fs 모듈은 콜백 형식의 모듈이므로 사용하기 불편. 프로미스 형식으로 바꿔주는 방법 사용

**기본 fs**

```javascript
const fs = require('fs');

fs.readFile('./readme.txt', (err, data) => {
    if (err) {
        thorw err;
    }
    console.log(data);
    console.log(data.toString());
});
```

**프로미스 형식**

``` javascript
const fs = require('fs').promises;

fs.readFile('./readme.txt')
.then((data) => {
    console.log(data);
    console.log(data.toString());
})
.catch((err) => {
   console.error(err);
});
```

fs 모듈의 다른 메서드를 배우기 전에 몇 가지 개념

### 동기/비동기 메서드

노드는 대부분의 메서드를 비동기 처리하지만 fs모듈은 동기 방식 메서드 다수

``` javascript
const fs = require('fs');

console.log('시작');
fs.readFile('./readme2.txt' (err, data) => {
    if(err){
        throw err;
    }
    console.log('1번', data.toString());
});)
fs.readFile('./readme2.txt' (err, data) => {
    if(err){
        throw err;
    }
    console.log('2번', data.toString());
});)
fs.readFile('./readme2.txt' (err, data) => {
    if(err){
        throw err;
    }
    console.log('3번', data.toString());
});)
console.log('끝');
```

비동기 메서드(readFile)는 백그라운드에 요청하고 다음 작업으로 넘어감

=>시작, 끝을 제외하고 실행 결과가 달라짐.

순서대로 실행하고 싶다면 readFileSync 등의 메서드 사용

**sync.js**

```javascript
const fs = require('fs');

console.log('시작');
let data = fs.readFileSync('./readme2.txt');
console.log('1번', data.toString());
data = fs.readFileSync('./readme2.txt');
console.log('2번', data.toString());
data = fs.readFileSync('./readme2.txt');
console.log('3번', data.toString());
console.log('끝');
```

이런 동기 메서드들이 실행하는 동안 메인 쓰레드는 대기해야하기 때문에 비효율.

동기 메서드는 프로그램 초기화 용도를 제외하고는 잘 사용안함.

비동기 방식을 사용하되 순서를 유지하려면 콜백 사용, 콜백 지옥이 생길수도...
=> 프로미스나 async/await으로 어느정도 해결

**asyncOrderPromise.js**

```javascript
const fs = require('fs').promises;

console.log('시작');
fs.readFile('./readme2.txt');
.then((data) => {
    console.log('1번',data.toString());
    return fs.readFile('./readme2.txt');
})
fs.readFile('./readme2.txt');
.then((data) => {
    console.log('2번',data.toString());
    return fs.readFile('./readme2.txt');
})
fs.readFile('./readme2.txt');
.then((data) => {
    console.log('3번',data.toString());
    console.log('끝');
})
.catch((err) => {
    console.error(err);
});
```



### 버퍼와 스트림 이해하기

파일을 읽거나 쓰는 방식에는 두가지, 버퍼와 스트림이 있음.

**buffer.js**

```javascript
const buffer = Buffer.from('저를 버퍼로 바꿔보세요');
console.log('from():', buffer);
console.log('length:', buffer.length);
console.log('toString():', buffer.toString);

const array = [Buffer.from('띄엄 '), Buffer.from('띄엄 '), Buffer.from('띄어쓰기')];
const buffer2 = Buffer.concat(array);
console.log('concat():', buffer2.toString());

const buffer3 = BUffer.alloc(5);
console.log('alloc():', buffer3);
```

용량이 큰 파일을 읽을 땐 메모리에 그만큼 버퍼를 만들어야함

특히 서버에선 메모리 문제 발생할 수 있음

그래서 버퍼의 크기를 작게 만든 후 여러번으로 나눠 보내는 방식=>스트림

**createReadStream.js**

```javascript
const fs = require('fs');

const readStream = fs.createReadStream('./readme3.txt', { hightWaterMark: 16 });
const data = [];

readStream.on('data', (chunk) => {
    data.push(chunk);
    console.log('data:',chunk, chunk.length);
});

readStream.on('end', () => {
    console.log('end:',Buffer.concat(data).toString());
});

readStream.on('error', (err)=>{
    console.log('error:',err);
});
```



createReadStream으로 파일을 읽고 그 스트림을 전달받아 createWriteStream으로 파일을 쓸 수도 있음.

**pipe.js**

```javascript
const fs = require('fs');
const readStream = fs.createReadStream('./readme4.txt');
const writeStream = fs.createWriteStream('./writeme3.txt');
readStream.pipe(writeStream);
```

pipe는 스트림 사이에 여러 번 연결 가능

**gzip.js**

```javascript
const zlib = require('zlib');
const fs = require('fd');

const readStream = fs.createReadStream('./readme4.txt');
const zlibStream = zlib.createGzip();
const writeStream = fs.createWriteStream('./writeme4.txt');
readStream.pipe(zlibStream).pipe(writeStream);
```

### 기타 fs 메서드

파일/폴더를 생성/삭제할 수도 있음

**fsCreate.js**

```javascript
const fs = require('fs').promises;
const constants  require('fs').constants;

fs.access('./folder', constants.F_OK|constants.W_OK|constants.R_OK)
.then(()=>{
    return Promise.reject('이미 폴더 있음');
})
.catch((err)=>{
    if(err.code==='ENOENT'){
        console.log('폴더 없음');
        return fs.mkdir('./folder');
    }
})
```

