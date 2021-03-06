# http 모듈로 서버 만들기

## 요청과 응답

<img src="https://thebook.io/img/080229/170.jpg" width="60%">

클라이언트는 서버로 요청(request)를 보내고, 서버는 클라이언트에 응답(response)을 보냄.

서버는 요청을 받는 부분과 응답을 보내는 부분 필요. 요청에 대한 이벤트 리스터 미리 등록.

이벤트 리스너를 가진 노드 서버

**createServer.js**

```javascript
const http = require('http');

http.createServer((req, res) => {
  // 여기에 어떻게 응답할지 적습니다.
});
```



응답을 보내는 부분과 서버에 연결하는 부분 추가하는 예제

**server1.js**

```javascript
const http = require('http');

http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  res.write('<h1>Hello Node!</h1>');
  res.end('<p>Hello Server!</p>');
})
  .listen(8080, () => { // 서버 연결
    console.log('8080번 포트에서 서버 대기 중입니다!');
  });
```

- **res.riteHead**: 응답에 대한 정보를 기록. 첫 번째 인수로 200(성공), 두 번째 인수로 응답에 대한 정보가 html형식임을 알림
- **res.rite**: 클라이언트로 보낼 데이터. 문자열이나 버퍼를 보냄
- **res.end**: 응답을 종료하는 메서드. 인수가 있다면 데이터도 보냄

**포트란**: 서버 내에서 프로세스를 구분하는 번호. IP 주소 뒤에 콜론(:)과 함께 붙여 사용



res.rite와 res.end에 일일이 HTML을 적는 것은 비효율적이므로 미리 HTML 파일을 만들고 fs모듈로 읽어서 전송



**server2.html**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Node.js 웹 서버</title>
</head>
<body>
    <h1>Node.js 웹 서버</h1>
    <p>만들 준비되셨나요?</p>
</body>
</html>
```

**server2.js**

```javascript
const http = require('http');
const fs = require('fs').promises;

http.createServer(async (req, res) => {
  try {
    const data = await fs.readFile('./server2.html');
    res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
    res.end(data);
  } catch (err) {
    console.error(err);
    res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });
    res.end(err.message);
  }
})
  .listen(8081, () => {
    console.log('8081번 포트에서 서버 대기 중입니다!');
  });
```

**HTTP 상태 코드**

- *2XX**: 성공을 알리는 상태 코드입니다. 대표적으로 200(성공), 201(작성됨)이 많이 사용됩니다.

- **3****XX**: 리다이렉션(다른 페이지로 이동)을 알리는 상태 코드입니다. 어떤 주소를 입력했는데 다른 주소의 페이지로 넘어갈 때 이 코드가 사용됩니다. 대표적으로 301(영구 이동), 302(임시 이동)가 있습니다. 304(수정되지 않음)는 요청의 응답으로 캐시를 사용했다는 뜻입니다.

- **4****XX**: 요청 오류를 나타냅니다. 요청 자체에 오류가 있을 때 표시됩니다. 대표적으로 400(잘못된 요청), 401(권한 없음), 403(금지됨), 404(찾을 수 없음)가 있습니다.

- **5****XX**: 서버 오류를 나타냅니다. 요청은 제대로 왔지만 서버에 오류가 생겼을 때 발생합니다. 이 오류가 뜨지 않게 주의해서 프로그래밍해야 합니다. 이 오류를 res.riteHead로 클라이언트에 직접 보내는 경우는 거의 없고, 예기치 못한 에러 발생 시 서버가 알아서 5XX대 코드를 보냅니다. 500(내부 서버 오류), 502(불량 게이트웨이), 503(서비스를 사용할 수 없음)이 자주 사용됩니다.

## REST와 라우팅 사용

### REST

Representational State Transfer의 줄임말. 서버의 자원 정의, 자원에 대한 주소를 지정하는 방법. **일종의 약속**

- GET: 서버 자원을 가져옴. 요청의 본문에 데이터X. 데이터를 서버로 보내야 한다면 쿼리스트링을 사용.
- POST: 서버에 자원을 새로 등록. 요청의 본문에 새로 등록할 데이터를 넣어 보냄.
- PUT: 서버의 자원을 요청에 들어 있는 자원으로 치환. 요청의 본문에 치환할 데이터를 넣어 보냄.
- PATCH: 서버 자원의 일부만 수정. 요청의 본문에 일부 수정할 데이터를 넣어 보냄
- DELETE: 서버의 자원을 삭제. 요청의 본문에 데이터 X
- OPTIONS: 요청을 하기 전에 통신 옵션을 설명하기 위해 사용

<img src="https://thebook.io/img/080229/179.jpg" width="60%">

**restServer.js**

```javascript
const http = require('http');
const fs = require('fs').promises;

const users = {}; // 데이터 저장용

http.createServer(async (req, res) => {
  try {
    console.log(req.method, req.url);
    if (req.method === 'GET') {
      if (req.url === '/') {
        const data = await fs.readFile('./restFront.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/about') {
        const data = await fs.readFile('./about.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/users') {
        res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
        return res.end(JSON.stringify(users));
      }
      // /도 /about도 /users도 아니면
      try {
        const data = await fs.readFile(`.${req.url}`);
        return res.end(data);
      } catch (err) {
        // 주소에 해당하는 라우트를 못 찾았다는 404 Not Found error 발생
      }
    } else if (req.method === 'POST') {
      if (req.url === '/user') {
        let body = '';
        // 요청의 body를 stream 형식으로 받음
        req.on('data', (data) => {
          body += data;
        });
        // 요청의 body를 다 받은 후 실행됨
        return req.on('end', () => {
          console.log('POST 본문(Body):', body);
          const { name } = JSON.parse(body);
          const id = Date.now();
          users[id] = name;
          res.writeHead(201);
          res.end('등록 성공');
        });
      }
    } else if (req.method === 'PUT') {
      if (req.url.startsWith('/user/')) {
        const key = req.url.split('/')[2];
        let body = '';
        req.on('data', (data) => {
          body += data;
        });
        return req.on('end', () => {
          console.log('PUT 본문(Body):', body);
          users[key] = JSON.parse(body).name;
          return res.end(JSON.stringify(users));
        });
      }
    } else if (req.method === 'DELETE') {
      if (req.url.startsWith('/user/')) {
        const key = req.url.split('/')[2];
        delete users[key];
        return res.end(JSON.stringify(users));
      }
    }
    res.writeHead(404);
    return res.end('NOT FOUND');
  } catch (err) {
    console.error(err);
    res.writeHead(500);
    res.end(err);
  }
 })
  .listen(8082, () => {
    console.log('8082번 포트에서 서버 대기 중입니다');
  });
```

req.method로 HTTP 요청 메서드 구분

GET일 경우 주소를 확인하여 파일 제공

POST /user 요청에서는 사용자를 새로 저장

PUT / user/아이디 요청에서는 해당 아이디의 사용자 데이터를 수정

DELETE /user/아이디 요청에서는 해당 아이디의 사용자 데이터를 제거

req.on('data')와 req.on('end')은 요처으이 본문에 들어있는 데이터를 꺼내기 위한 작업