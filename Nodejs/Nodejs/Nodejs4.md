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
- toString(): 조작한 searchParams 객체를 다시 문자열로 만듦. 이 문자열을 search에 대입하면 주소 객체에 반영됩니다.

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

crypto 내용 추후 추가

#### util