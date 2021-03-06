## 노드 기능 2

### 노드 내장 모듈 사용하기

### os

운영체제 정보를 가져옴

__os.js__

```javascript
const os = require('os');

console.log('운영체제 정보---------------------------------');
console.log('os.arch():', os.arch()); 				//process.arch와 동일
console.log('os.platform():', os.platform());		//process.platform과 동일
console.log('os.type():', os.type());				//운영체제의 종류를 보여줌
console.log('os.uptime():', os.uptime());			//운영체제 부팅 이후 흐른 시간(초)
console.log('os.hostname():', os.hostname());		//컴퓨터의 이름
console.log('os.release():', os.release());			//운영체제 버전

console.log('경로------------------------------------------');
console.log('os.homedir():', os.homedir());			//홈 디렉터리 경로
console.log('os.tmpdir():', os.tmpdir());			//임시 파일 저장 경로

console.log('cpu 정보--------------------------------------');
console.log('os.cpus():', os.cpus());				//컴퓨터 코어 정보
console.log('os.cpus().length:', os.cpus().length);	//코어 갯수

console.log('메모리 정보-----------------------------------');
console.log('os.freemem():', os.freemem());			//사용 가능한 메모리
console.log('os.totalmem():', os.totalmem());		//전체 매모리
```

os.constants라는 객체는 에러와 신호에 대한 정보

운영체제별 다른 서비스를 제공하고 싶을 때 사용

#### path

폴더와 파일의 경로를 쉽게 조작하도록 도와줌

운영체제별로 경로 구분자가 다르기 때문

- **Windows**: C:\Users\ZeroCho처럼 \로 구분
- **POSIX**: /home/zerocho처럼 /로 구분

path.js

```javascript
const path = require('path');

const string = __filename;

console.log('path.sep:', path.sep);
console.log('path.delimiter:', path.delimiter);
console.log('------------------------------');
console.log('path.dirname():', path.dirname(string));
console.log('path.extname():', path.extname(string));
console.log('path.basename():', path.basename(string));
console.log('path.basename - extname:', path.basename(string, path.extname(string)));
console.log('------------------------------');
console.log('path.parse()', path.parse(string));
console.log('path.format():', path.format({
  dir: 'C:\\users\\zerocho',
  name: 'path',
  ext: '.js',
}));
console.log('path.normalize():', path.normalize('C://users\\\\zerocho\\\path.js'));
console.log('------------------------------');
console.log('path.isAbsolute(C:\\):', path.isAbsolute('C:\\'));
console.log('path.isAbsolute(./home):', path.isAbsolute('./home'));
console.log('------------------------------');
console.log('path.relative():', path.relative('C:\\users\\zerocho\\path.js', 'C:\\'));
console.log('path.join():', path.join(__dirname, '..', '..', '/users', '.', '/zerocho'));
console.log('path.resolve():', path.resolve(__dirname, '..', 'users', '.', '/zerocho'));
```

- path.sep: 경로의 구분자입니다. 윈도는 \, POSIX는 /입니다.

- path.delimiter: 환경 변수의 구분자. process.env.PATH를 입력하면 여러 개의 경로가 이 구분자로 구분. 윈도는 세미콜론(;)이고, POSIX는 콜론(:)

- path.dirname(경로): 파일이 위치한 폴더 경로

- path.extname(경로): 파일의 확장자

- path.basename(경로, 확장자): 파일의 이름(확장자 포함)을 표시. 파일의 이름만 표시하고 싶다면 basename의 두 번째 인수로 파일의 확장자 넣기

- path.parse(경로): 파일 경로를 root, dir, base, ext, name으로 분리

- path.format(객체): path.parse()한 객체를 파일 경로로 합침

- path.normalize(경로): /나 \를 실수로 여러 번 사용했거나 혼용했을 때 정상적인 경로로 변환
- path.isAbsolute(경로): 파일의 경로가 절대경로인지 상대경로인지를 true나 false 알림

- path.relative(기준경로, 비교경로): 경로를 두 개 넣으면 첫 번째 경로에서 두 번째 경로로 가는 방법을 알림

- path.join(경로, …): 여러 인수를 넣으면 하나의 경로로 합침. 상대경로인 ..(부모 디렉터리)과 .(현 위치)도 알아서 처리

- path.resolve(경로, …): path.join()과 비슷함. resolve는 절대경로로 인식, join은 상대경로로 처리

#### URL

인터넷 주소를 쉽게 조작하도록 도와줌

WHATWG 방식과 예전부터 노드에서 사용하던 방식

<img src="https://thebook.io/img/080229/119.jpg" width="60%">

__url.js__

```javascript
const url = require('url');

const { URL } = url;
const myURL = new URL('http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor');
console.log('new URL():', myURL);
console.log('url.format():', url.format(myURL));
console.log('------------------------------');
const parsedUrl = url.parse('http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor');
console.log('url.parse():', parsedUrl);
```

기존 노드 방식에서는 두 메서드 주로 사용

- url.parse(주소): 주소를 분해
- url.format(객체): WHATWG 방식 url과 기존 노드의 url 모두 사용. 분해되었던 url 객체를 다시 조립

WHATWG와 노드는 취향에 따라 사용하지만, 노드의 url 형식을 꼭 사용해야 하는 경우가 있음

host 부분없이 pathname 부분만 오는 주소인 경우에 url 사용

WHATWG 방식은 search 부분을 searchParams 라는 특수한 객체로 반환

**searchParams.js**

```javascript
const { URL } = require('url');

const myURL = new URL('http://www.gilbut.co.kr/?page=3&limit=10&category=nodejs&category=javascript');
console.log('searchParams:', myURL.searchParams);
console.log('searchParams.getAll():', myURL.searchParams.getAll('category'));
console.log('searchParams.get():', myURL.searchParams.get('limit'));
console.log('searchParams.has():', myURL.searchParams.has('page'));

console.log('searchParams.keys():', myURL.searchParams.keys());
console.log('searchParams.values():', myURL.searchParams.values());

myURL.searchParams.append('filter', 'es3');
myURL.searchParams.append('filter', 'es5');
console.log(myURL.searchParams.getAll('filter'));

myURL.searchParams.set('filter', 'es6');
console.log(myURL.searchParams.getAll('filter'));

myURL.searchParams.delete('filter');
console.log(myURL.searchParams.getAll('filter'));

console.log('searchParams.toString():', myURL.searchParams.toString());
myURL.search = myURL.searchParams.toString();
```

myURL 안에는 searchParams 객체. 이 객체는 search 부분을 조작하는 다양한 메서드를 지원

- setAll(키): 키에 해당하는 모든 값들을 가져옴
- get(키): 키에 해당하는 첫 번째 값만 가져옴
- has(키): 해당 키가 있는지 없는지를 검사
- keys(): searchParams의 모든 키를 반복기(iterator)객체로 가져옴
- values(): searchParams의 모든 값을 반복기 객체로 가져옴
- append(키, 값): 해당 키를 추가
- set(키, 값): append와 비슷하지만, 같은 키의 값들을 모두 지우고 새로 추가
- delete(키): 해당 키를 제거
- toString(): 조작한 searchParams 객체를 다시 문자열로 만듦. 이 문자열을 search에 대입하면 주소 객체에 반영

__query는 querystring 모듈을 한 번 더 사용해야 하므로 searchParams가 유용함__

##### querystring

기존 노드의 url 사용시 search 부분을 사용하기 쉽게 객체화

**querystring.js**

```javascript
const url = require('url'); 
const querystring = require('querystring'); 

const parsedUrl = url.parse('http://www.gilbut.co.kr/?page=3&limit=10&category=nodejs&category=javascript'); 
const query = querystring.parse(parsedUrl.query);
console.log('querystring.parse():', query);
console.log('querystring.stringify():', querystring.stringify(query));
```

- querystring.parse(쿼리): url의 query 부분을 자바스크립트 객체로 분해
- querystring.stringify(객체): 분해된 query 객체를 문자열로 다시 조립

#### Crypto

암호화를 도와주는 모듈

##### 단방향 복호화

복호화 할 수 없는 암호화 방식, 주로 해시 기법 사용

- createHash(알고리즘): 사용할 해시 알고리즘을 인수 대입. sha512 정도로 충분하지만 더 강한 알고리즘 필요
- update(문자열): 변환할 문자열을 넣습니다.
- digest(인코딩): 인코딩할 알고리즘을 넣습니다. base64, hex, latin1이 주로 사용. base64를 많이 사용

**hash.js**

``` javascript
const crypto = require('crypto');

console.log('base64: ', crypto.createHash('sha512').update('비밀번호').digest('base64'));
console.log('hex: ', crypto.createHash('sha512').update('비밀번호').digest('hex'));
console.log('base64: ', crypto.createHash('sha512').update('다른 비밀번호').digest('base64'));
```



현재는 주로 pbkdf2나 bcypt, scrypt라는 알고리즘으로 비밀번호 암호화.

그 중 노드에서 지원하는 pbkdf2: 기존 문자열에 salt라고 불리는 문자열을 붙인 후 해시 알고리즘을 반복해서 적용

**pbkdf2.js**

```javascript
const crypto = require('crypto');

crypto.randomBytes(64, (err, buf)=>{
    const salt = buf.toString('base64');
    console.log('salt: ', salt);
    crypto.pbkdf2('비밀번호', salt, 100000, 64, 'sha512', (err, key) =>{
        console.log('password: ', key.toString('base64'));
    });
});
```

randomBytes() 는 64바이트의 문자열을 만듦=>salt

pbkdf2의 인수: 비밀번호, salt, 반복 횟수, 출력 바이트, 해시 알고리즘

sha512로 변환된 결과값을 다시 sha512로 변환=>10만 번 반복

1초 정도 작업할 정도로 반복 횟수 설정, 내부적으로 스레드풀을 사용하므로 블로킹 걱정 X

##### 양방향 복호화

키를 사용. 코드를 완벽히 이해하려면 암호학 공부 필요....

**cipher.js**

```javascript
const crypto = require('crypto');

const algorithm = 'aes-256-cbc';
const key = 'abcedfghijklmnopqrstuvwxyz123456';
const iv = '1234567890123456';
const cipher = crypto.createCipheriv(algorithm, key, iv);
let result = cipher.update('암호화할 문장', 'utf8', 'base64');
result += cipher.final('base64');
console.log('암호화: ', result);

const decipher = crypto.createDecipheriv(algorithm, key, iv);
let result2 = decipher.update(result, 'base64', 'utf8');
result2 += decipher.final('utf8');
console.log('복호화: ', result);
```

- crypto.createCipheriv(알고리즘, 키, iv): 사용할 알고리즘, key, iv를 지정
- cipher.update(문자열, 인코딩, 출력 인코딩): 암호화할 대상과 인코딩, 출력 결과물의 인코딩 지정
- cpyto.createDecipheriv(알고리즘, 키, iv): 복호화 할 때 사용. 암호화할 때 사용한 인수를 그대로 사용해야 함
- decipher.update(문자열, 인코딩, 출력 인코딩): 암호화된 문장과 그 인코딩, 복호화할 인코딩. 암호화 할 때 사용한 인코딩 방식 반대로 입력
- decipher.final(출력 인코딩): 복호화 결과물의 인코딩을 넣음

**npm 패키지인 crypto-js를 이용하여 간단하게 암호화 가능!**

#### util

각종 편의기능을 모아둔 모듈, 가끔 deprecated되어 사라지기도

자주 사용하는 두 기능

- util.depreceate: 함수가 deprecated 처리되었음을 알림. 첫 번째 인수: 콜백 함수, 두 번째 인수: 경고 메시지
- util.promisify: 콜백 패턴을 프로미스 패턴으로 바꿈=>async/await 패턴까지 사용.



#### worker_threads

노드에서 멀티 스레드 방식으로 작업하는 방법

**worker_threads.js**

```javascript
const {
    Worker, insMainThread, parentPort,
} = require('worker_threads');

if (ifMainThread) {			// 메인 스레드일 때 
    const
    worker = new Worker(__filename);	// 워커를 생성하고 현재 파일 실행시킴
    									// 생성된 워커는 현재 파일의 else부분만 실행
    // 이벤트 리스너로 워커로부터 메시지를 받음
    worker.on('message', message => console.log('from worker', message));
    worker.on('exit', () => console.log('worker exit'));
    // postMessage로 워커에 메시지를 보냄
    worker.postMessage('ping'); 
} else {					// 워커일 때
    // 이벤트 리스터로 메시지를 받고 postMessage로 부모에 메시지 보냄
    parentPort.on('message', (value) => {
        console.log('from parent', value);
        parentPort.postMessage('pong');
        parentPort.close();
    });
}
```

여러 개의 워커 스레드에 데이터를 넘기기

**worker_data.js**

```javascript
const {
    Worker, isMainThread, parentPort, workerData,
} = require('worker_threads');

if (isMainThread){ 				//메인일 때
    const threads = new Set();
    
    // 워커를 생성하고 데이터를 각각 start: 1, 2로 지정
    threads.add(new Worker(__filename),{
        workerData: { start: 1},
    }));
    threads.add(new Worker(__filename),{
        workerData: { start: 2},
    }));
    // 이벤트 리스너로 각 워커로부터 메시지를 받음
    for (let worker of threads) {
        worker.on('message', message => console.log('from worker', message));
        worker.on('exit', () => {
            threads.delete(worker);
            if(threads.size===0){
                console.log('jab done');
            }
        })
    }
} else{	// 워커일 때
    const data = workerData;
    // 부모에 메시지 보냄
    parentPort.postMessage(data.start + 100);
}

//출력 결과
//from worker 101
//from worker 102
//job done
```

특히 소수의 개수를 구하는 등의 작업은 연산이 많이 들어감

**prime-worker.js**

```javascript
const { Worker, isMainThread, parentPort, workerData } = require('worker_threads');

const min = 2;

let primes = [];

function findPrimes(start, range) {	// 워커가 실행할 함수
    let is Prime = ture;
    let end = start + range;
    for (let i = start; i<end; i++) {
        for (let j = min; j<Math.sqrt(end); j++) {
            if(i !==j && i%j==0) {
                isPrime = false;
                break;
            }
        }
        if (isPrime) {
            primes.push(i);
        }
        isPrime = true;
    }
}

if(isMainThread) {	// 메인일 때
    const max = 1000000;
    const threadCount = 8;
    const threads = new Set();
    const range = Math.ceil((max-min)/threadCount);
    let start = min;
    console.itme('prime');
    for(let i=0;i<threadCOunt-1;i++){	// 워커 생성 및 start, range 지정
        const wStart = start;
        threads.add(new Worker(__filename, { workerData: {start: wStart, range } }));
        start+=range;
    }
    threads.add(new Worker(__filename, 
                           { workerData: { start, range: range + ((max-min+1)%threadCOunt) } }));
    // 이벤트 리스너로 각 워커의 메시지를 받음
    for (let worker of threads){
        // 워커가 보낸 에러 메시지 출력
        worker.on('error', (err)=>{
            throw err;
        });
        // 워커가 사라지면 쓰레드에서 워커를 제거
        worker.on('exit', () => {
            threads.delete(worker);
            // 모든 워커가 사라지면 타이머 종료
            if(threads.size === 0){
                console.timeEnd('prime');
                console.log(prime)
            }
        });
        // 워커가 보낸 메시지를 primes에 추가
        worker.on('message', (msg) => {
            primes = primes.concat(msg);
        });
    } 
}else{
    findPrimes(workerData.start, workerData,range);
    parentPort.postMessage(primes);
}
```



#### child_process

현재 노드 프로세스 외에 새로운 프로세스를 띄워서 명령을 수행하고, 노드 프로세스에 결과를 알려줌

**exec.js**

```javascript
const exec = require('child_process').exec;

const process = exec('dir');

process.stdout.on('data', function(data){
    console.log(data.toString());
}); // 실행 결과

process.stderr.on('dta', function(data){
    console.error(data.toString());
});	// 실행 에러
```

프로세스를 생성하여 명령 프롬프트의 dir을 실행하고 결과를 출력



#### 기타 모듈들

생략