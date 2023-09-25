---
layout: post
title: 4. http 모듈로 서버 만들기
author: admin
date: 2023-04-23 00:00:00 +900
lastmod: 2023-04-23 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NODE, NODEJS교과서] # 대문자로 작성
tags: [node, nodejs, 노드교과서] # 소문자로 작성
---

> 기존 블로그에 작성했던 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `NODEJS`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---

<br/>

# **🌈 http 모듈로 서버 만들기**

## **💻 요청과 응답 이해하기**

### **🍳 서버와 클라이언트**

서버와 클라이언트의 관계

- 클라이언트가 서버로 요청(request)을 보냄

- 서버는 요청을 처리

- 처리 후 클라이언트로 응답(response)을 보냄

![서버와 클라이언트의 관계](https://github.com/leekoby/leekoby.github.io/assets/118284808/464cb3f0-49c0-467d-aded-9bdd08953cbf){: width="500" height="500" }

<br/>

### **🍳 노드로 http 서버 만들기**

http 요청에 응답하는 노드 서버

- createServer로 요청 이벤트에 대기

- req 객체는 요청에 관한 정보가, res 객체는 응답에 관한 정보가 담겨 있음

🔻createServer.js

```js
const http = require('http');

http.createServer((req, res) => {
  // 여기에 어떻게 응답할 지 적어줍니다.
});
```

<br/>

### **🍳 8080 포트에 연결하기**

- res 메서드로 응답 보냄

- write로 응답 내용을 적고

- end로 응답 마무리(내용을 넣어도 됨)

listen(포트) 메서드로 특정 포트에 연결

🔻server1.js

```js
const http = require('http');

http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  // html을 string으로 인식하는 경우에 코드 추가, 한글 인식하기 위해 코드 추가;
  res.write('<h1>Hello Wolrd!</h1>');
  res.write('<p>Hello Leekoby</p>');
  res.end('<p>Hello Server</p>');
})
  .listen(8080, () => {// 서버 연결
    console.log('8080번 포트에서 서버 대기 중입니다.');
  });
```

또는 


```js
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  //html을 string으로 인식하는 경우에 코드 추가, 한글 인식하기 위해 코드 추가;
  res.write('<h1>Hello Wolrd!</h1>');
  res.write('<p>Hello Leekoby</p>');
  res.end('<p>Hello Server</p>');
})
  .listen(8080);// 서버 연결
server.on('listening', () => {
  console.log('8080번 포트에서 서버 대기 중입니다.');
});

server.on('error', (error) => {
  console.error(error);
});
```

<br/>

### **🍳 8080 포트로 접속하기**

스크립트를 실행하면 8080 포트에 연결됨

![8080 포트로 접속하기](https://github.com/leekoby/leekoby.github.io/assets/118284808/b7d3e64d-14ab-4025-a35b-9d57252eea81){: width="500" height="500" }

> localhost:8080 또는 http://127.0.0.1:8080에 접속

✅ 결과

![결과](https://github.com/leekoby/leekoby.github.io/assets/118284808/6a1d977f-5a95-47f9-a2e4-86c08c843237){: width="500" height="500" }

![서버 구조도](https://github.com/leekoby/leekoby.github.io/assets/118284808/b5c3b6d1-910f-4824-ba6f-05f9e9b94c83){: width="500" height="500" }

<br/>

### **🍳 localhost와 포트**

localhost는 컴퓨터 내부 주소

- 외부에서는 접근 불가능

포트는 서버 내에서 프로세스를 구분하는 번호

- 기본적으로 http 서버는 80번 포트 사용(생략가능, https는 443)

- 예) www.gilbut.com:80 -> www.github.com

- 다른 포트로 데이터베이스나 다른 서버 동시에 연결 가능

![ip와 포트](https://github.com/leekoby/leekoby.github.io/assets/118284808/709d57fa-0d63-439e-9760-e153bed04c26){: width="500" height="500" }


<br/>

### **🍳 이벤트 리스너 붙이기**

listening과 error 이벤트를 붙일 수 있음.

🔻server1-1.js

```js
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  res.write('<h1>Hello Node!</h1>');
  res.end('<p>Hello Server!</p>');
});
server.listen(8080);

server.on('listening', () => {
  console.log('8080번 포트에서 서버 대기 중입니다!');
});
server.on('error', (error) => {
  console.error(error);
});
```

<br/>

### **🍳 한 번에 여러 개의 서버 실행하기**

createServer를 여러 번 호출하면 됨.

- 단, 두 서버의 포트를 다르게 지정해야 함.

- 같게 지정하면 `EADDRINUSE` 에러 발생

🔻server1-2.js

```js
const http = require('http');

http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  res.write('<h1>Hello Node!</h1>');
  res.end('<p>Hello Server!</p>');
})
  .listen(8080, () => { // 서버 연결
    console.log('8080번 포트에서 서버 대기 중입니다!');
  });

http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  res.write('<h1>Hello Node!</h1>');
  res.end('<p>Hello Server!</p>');
})
  .listen(8080, () => { // 서버 연결
    console.log('8081번 포트에서 서버 대기 중입니다!');
  });
```

<br/>

### **🍳 html 읽어서 전송하기**

write와 end에 문자열을 넣는 것은 비효율적

- fs 모듈로 html을 읽어서 전송하자

- write가 버퍼도 전송 가능

🔻server2.html

```js
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <!-- 한글 나오게 하려면 utf-8을 적어줌. -->
    <title>Node.js 웹 서버</title>
  </head>
  <body>
    <h1>Node.js 웹 서버</h1>
    <p>만들 준비되셨나요?</p>
  </body>
</html>
```

🔻server2.js

```js
const http = require('http');
const fs = require('fs').promises;

http.createServer(async (req, res) => {
  //try & catch로 에러 처리
  try {
    const data = await fs.readFile('./server2.html');
    res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
    res.end(data);
  } catch (err) {
    console.error(err);
    res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });//plain은 일반 문자열임을 알려줌
    res.end(err.message);
  }
})
  .listen(8081, () => {
    console.log('8081번 포트에서 서버 대기 중입니다!');
  });
```

✅ 결과 

![결과](https://github.com/leekoby/leekoby.github.io/assets/118284808/859cfeff-a12a-42e0-95b9-486f1b3709be){: width="500" height="500" }

> **<span style="color:#3366ff"> HTTP 상태 코드</span>**
> 
> **<span style="color:#ff6600">2XX</span>**
> - 성공을 알리는 상태 코드. 
> - 대표적으로 200(성공), 201(작성됨)이 많이 사용
> 
> **<span style="color:#ff6600">3XX</span>**
> - 리다이렉션(다른 페이지로 이동)을 알리는 상태 코드
> - 어떤 주소를 입력했는데 다른 주소의 페이지로 넘어갈 때 이 코드가 사용
> - 대표적으로 301(영구 이동), 302(임시 이동)
> - 304(수정되지 않음)는 요청의 응답으로 캐시를 사용했다는 뜻
> 
> **<span style="color:#ff6600">4XX</span>**
> - 요청 오류
> - 요청 자체에 오류가 있을 때 표시
> - 대표적으로 400(잘못된 요청), 401(권한 없음), 403(금지됨), 404(찾을 수 없음)가 있다.
> 
> **<span style="color:#ff6600">5XX</span>**
> - 서버 오류
> - 요청은 제대로 왔지만 서버에 오류가 생겼을 때 발생
> - 이 오류가 뜨지 않게 주의해서 프로그래밍해야 한다. 
> - 이 오류를 res.writeHead로 클라이언트에 직접 보내는 경우는 거의 없고, 예기치 못한 에러가 발생하면 서버가 알아서 5XX대 코드를 보낸다.
> - 500(내부 서버 오류), 502(불량 게이트웨이), 503(서비스를 사용할 수 없음)이 자주 사용
> **<span style="color:#ff6600"></span>**
{:.prompt-info }

> 무조건 응답을 보내야 한다.
> 
> 요청 처리 과정 중에 에러가 발생했다고 해서 응답을 보내지 않으면 안 된다. 
> 
> 요청이 성공했든 실패했든 응답을 클라이언트로 보내서 요청이 마무리되었음을 알려야 한다. 
> 
> 응답을 보내지 않는다면, 클라이언트는 서버로부터 응답이 오길 하염없이 기다리다가 일정 시간이 지난 후 Timeout(시간 초과) 처리한다.
{:.prompt-warning}

<br/>

---

<br/>

## **💻 REST API와 라우팅**

### **🍳 REST API**

서버에 요청을 보낼 때는 주소를 통해 요청의 내용을 표현

/index.html이면 index.html을 보내달라는 뜻

항상 html을 요구할 필요는 없음

서버가 이해하기 쉬운 주소가 좋음

REST API(Representational State Transfer)

서버의 자원을 정의하고 자원에 대한 주소를 지정하는 방법

`/user`이면 사용자 정보에 관한 정보를 요청하는 것

`/post`면 게시글에 관련된 자원을 요청하는 것

> **<span style="color:#3366ff">HTTP 요청 메서드</span>**
> 
> **<span style="color:#ff6600">GET</span>**
> - 서버 자원을 가져오려고 할 때 사용
> 
> **<span style="color:#ff6600">POST</span>**
> - 서버에 자원을 새로 등록하고자 할 때 사용(또는 뭘 써야할 지 애매할 때)
> 
> **<span style="color:#ff6600">PUT</span>**
> - 서버의 자원을 요청에 들어있는 자원으로 치환하고자할 때 사용
> 
> **<span style="color:#ff6600">PATCH</span>**
> - 서버 자원의 일부만 수정하고자 할 때 사용
> **<span style="color:#ff6600">DELETE</span>**
> - 서버의 자원을 삭제하고자할 때 사용
{:.prompt-info }

<br/>

### **🍳 HTTP 프로토콜**

클라이언트가 누구든 서버와 HTTP 프로토콜로 소통 가능

- iOS, 안드로이드, 웹이 모두 같은 주소로 요청 보낼 수 있음

- 서버와 클라이언트의 분리

![rest api](https://github.com/leekoby/leekoby.github.io/assets/118284808/0496c557-45f8-4020-9619-16d8cb3215bf){: width="500" height="500" }

RESTful

- REST API를 사용한 주소 체계를 이용하는 서버

- GET /user는 사용자를 조회하는 요청, POST /user는 사용자를 등록하는 요청

![서버 주소 구조](https://github.com/leekoby/leekoby.github.io/assets/118284808/d6dfa501-57c1-4674-a3cf-b4dbdc38756c){: width="500" height="500" }

<br/>

### **🍳 REST 서버 만들기**

[GitHub 저장소 ch4 4.2 소스 참조](https://github.com/ZeroCho/nodejs-book/tree/master/ch4){:target="_blank"}

🔻resFront.html

```js
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="utf-8" />
  <title>RESTful SERVER</title>
  <link rel="stylesheet" href="./restFront.css" />
</head>
<body>
<nav>
  <a href="/">Home</a>
  <a href="/about">About</a>
</nav>
<div>
  <form id="form">
    <input type="text" id="username">
    <button type="submit">등록</button>
  </form>
</div>
<div id="list"></div>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script src="./restFront.js"></script>
</body>
</html>
```

🔻resFront.css

```js
a { color: blue; text-decoration: none; }
```

🔻about.html

```js
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>RESTful SERVER</title>
  <link rel="stylesheet" href="./restFront.css" />
</head>
<body>
<nav>
  <a href="/">Home</a>
  <a href="/about">About</a>
</nav>
<div>
  <h2>소개 페이지입니다.</h2>
  <p>사용자 이름을 등록하세요!</p>
</div>
</body>
</html>
```

🔻resFront.js

```js
async function getUser() { // 로딩 시 사용자 가져오는 함수
  try {
    const res = await axios.get('/users');
    const users = res.data;
    const list = document.getElementById('list');
    list.innerHTML = '';
    // 사용자마다 반복적으로 화면 표시 및 이벤트 연결
    Object.keys(users).map(function (key) {
      const userDiv = document.createElement('div');
      const span = document.createElement('span');
      span.textContent = users[key];
      const edit = document.createElement('button');
      edit.textContent = '수정';
      edit.addEventListener('click', async () => { // 수정 버튼 클릭
        const name = prompt('바꿀 이름을 입력하세요');
        if (!name) {
          return alert('이름을 반드시 입력하셔야 합니다');
        }
        try {
          await axios.put('/user/' + key, { name });
          getUser();
        } catch (err) {
          console.error(err);
        }
      });
      const remove = document.createElement('button');
      remove.textContent = '삭제';
      remove.addEventListener('click', async () => { // 삭제 버튼 클릭
        try {
          await axios.delete('/user/' + key);
          getUser();
        } catch (err) {
          console.error(err);
        }
      });
      userDiv.appendChild(span);
      userDiv.appendChild(edit);
      userDiv.appendChild(remove);
      list.appendChild(userDiv);
      console.log(res.data);
    });
  } catch (err) {
    console.error(err);
  }
}

window.onload = getUser; // 화면 로딩 시 getUser 호출
// 폼 제출(submit) 시 실행
document.getElementById('form').addEventListener('submit', async (e) => {
  e.preventDefault();
  const name = e.target.username.value;
  if (!name) {
    return alert('이름을 입력하세요');
  }
  try {
    await axios.post('/user', { name });
    getUser();
  } catch (err) {
    console.error(err);
  }
  e.target.username.value = '';
});
```

🔻restServer.js에 주목

```js
const http = require('http');
const fs = require('fs').promises;

const users = {}; // 데이터 저장용

http.createServer(async (req, res) => {
  try {
    if (req.method === 'GET')//주소창에 치는 것-> GET요청 {
      if (req.url === '/') {
        const data = await fs.readFile('./restFront.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/about') {
        const data = await fs.readFile('./about.html');
        res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
        return res.end(data);
      } else if (req.url === '/users') {
        res.writeHead(200, { 'Content-Type': 'application/json; charset=utf-8' });
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
          res.writeHead(201, { 'Content-Type': 'text/plain; charset=utf-8' });
          //201-생성됨
          res.end('ok');
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
          res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
          return res.end('ok');
        });
      }
    } else if (req.method === 'DELETE') {
      if (req.url.startsWith('/user/')) {
        const key = req.url.split('/')[2];
        delete users[key];
        res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
        return res.end('ok');
      }
    }
    res.writeHead(404);
    return res.end('NOT FOUND');
  } catch (err) {
    console.error(err);
    res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });
    res.end(err.message);
  }
})
  .listen(8082, () => {
    console.log('8082번 포트에서 서버 대기 중입니다');
  });
```
GET 메서드에서 /, /about 요청 주소는 페이지를 요청하는 것이므로 HTML 파일을 읽어서 전송.

AJAX 요청을 처리하는 /users에서는 users 데이터를 전송.

JSON 형식으로 보내기 위해 JSON.stringify를 해줌.

그 외의 GET 요청은 CSS나 JS 파일을 요청하는 것이므로 찾아서 보내주고, 없다면 404 NOT FOUND 에러를 응답.

POST와 PUT 메서드는 클라이언트로부터 데이터를 받으므로 특별한 처리가 필요. 

req.on('data', 콜백)과 req.on('end', 콜백) 부분 버퍼와 스트림에서 배웠던 readStream

readStream으로 요청과 같이 들어오는 요청 본문을 받을 수 있음.

단, 문자열이므로 JSON으로 만드는 JSON.parse 과정이 한 번 필요.

DELETE 메서드로 요청이 오면 주소에 들어 있는 키에 해당하는 사용자를 제거.

해당하는 주소가 없을 경우 404 NOT FOUND 에러를 응답.

REST 서버 실행하기

localhost:8082에 접속

![요청 확인](https://github.com/leekoby/leekoby.github.io/assets/118284808/8c95e463-33e1-4997-883b-787f1c445693){: width="500" height="500" }

<br/>

### **🍳 REST 요청 확인하기**

개발자도구(F12) Network 탭에서 요청 내용 실시간 확인 가능

- Name은 요청 주소, Method는 요청 메서드, Status는 HTTP 응답 코드

- Protocol은 HTTP 프로토콜, Type은 요청 종류(xhr은 AJAX 요청)

![REST 요청 확인](https://github.com/leekoby/leekoby.github.io/assets/118284808/067d1a12-d7e4-4c91-b444-adec79162f90){: width="500" height="500" }

<br/>

> **<span style="color:#3366ff"> res.end 앞에 return을 붙이는 이유</span>**
>
> 노드도 일반적인 자바스크립트 문법을 따르므로 return을 붙이지 않는 한 함수가 종료되지 않는다. 
> 
> 따라서 다음에 코드가 이어지는 경우에는 return으로 명시적으로 함수를 종료할 수 있다. 
> 
> return을 붙이지 않아 res.end 같은 메서드가 여러 번 실행된다면 `Error: Can't render headers after they are sent to the client.` 에러가 발생
{:.prompt-info }

<br/>

### **🍳 헤더와 본문**

http 요청과 응답은 헤더와 본문을 가짐

- 헤더는 요청 또는 응답에 대한 정보를 가짐

- 본문은 주고받는 실제 데이터

- 쿠키는 부가적인 정보이므로 헤더에 저장

![http 요청/응답](https://github.com/leekoby/leekoby.github.io/assets/118284808/187301ad-d3ef-4d89-9d02-ae4156630066){: width="500" height="500" }

<br/>

---

<br/>

## **💻 쿠키와 세션 이해하기**


<br/>

### **🍳 쿠키의 필요성**

요청에는 한 가지 단점이 있음

- 누가 요청을 보냈는지 모름(IP 주소와 브라우저 정보 정도만 앎)

- 로그인을 구현하면 됨

- 쿠키와 세션이 필요

- 쿠키: 키=값의 쌍

- name=zerocho

- 매 요청마다 서버에 동봉해서 보냄

- 서버는 쿠키를 읽어 누구인지 파악

![쿠키](https://github.com/leekoby/leekoby.github.io/assets/118284808/73e4706b-dcbe-45f7-aabf-69582abe373c){: width="500" height="500" }

<br/>

### **🍳 쿠키 서버 만들기**

쿠키 넣는 것을 직접 구현

- `writeHead`: 요청 헤더에 입력하는 메서드

- `Set-Cookie`: 브라우저에게 쿠키를 설정하라고 명령

쿠키: 키=값의 쌍

- name=zerocho

- 매 요청마다 서버에 동봉해서 보냄

🔻cookie.js

```js
const http = require('http');

http.createServer((req, res) => {
  console.log(req.url, req.headers.cookie);
  res.writeHead(200, { 'Set-Cookie': 'mycookie=test' });
  res.end('Hello Cookie');
})
  .listen(8083, () => {
    console.log('8083번 포트에서 서버 대기 중입니다!');
  });
```

✅ 결과

```sh
/ undefined
/favicon.ico { mycookie: 'test' }
```

![결과](https://github.com/leekoby/leekoby.github.io/assets/118284808/5ba58b5d-cc00-4c50-af8f-975586c0ac08){: width="500" height="500" }

`req.headers.cookie`: 쿠키가 문자열로 담겨있음

`req.url`: 요청 주소

localhost:8082에 접속

- 요청이 전송되고 응답이 왔을 때 쿠키가 설정됨

- favicon.ico는 브라우저가 자동으로 보내는 요청

- 두 번째 요청인 favicon.ico에 쿠키가 넣어짐

<br/>

### **🍳 쿠키로 나를 식별하기**

쿠키에 내 정보를 입력

- `parseCookies`: 쿠키 문자열을 객체로 변환

- 주소가 /login인 경우와 /인 경우로 나뉨

- /login인 경우 쿼리스트링으로 온 이름을 쿠키로 저장

- 그 외의 경우 쿠키가 있는지 없는지 판단있으면 환영 인사 없으면 로그인 페이지로 리다이렉트

🔻cookie2.html

```js
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>쿠키&세션 이해하기</title>
</head>
<body>
<form action="/login">
    <input id="name" name="name" placeholder="이름을 입력하세요" />
    <button id="login">로그인</button>
</form>
</body>
</html>
```

🔻cookie2.js

```js
const http = require('http');
const fs = require('fs').promises;
const url = require('url');
const qs = require('querystring');

const parseCookies = (cookie = '') =>
  cookie
    .split(';')
    .map(v => v.split('='))
    .reduce((acc, [k, v]) => {
      acc[k.trim()] = decodeURIComponent(v);
      return acc;
    }, {});

http.createServer(async (req, res) => {
  const cookies = parseCookies(req.headers.cookie); // { mycookie: 'test' }
  // 주소가 /login으로 시작하는 경우
  if (req.url.startsWith('/login')) {
    const { query } = url.parse(req.url);
    const { name } = qs.parse(query);
    const expires = new Date();
    // 쿠키 유효 시간을 현재시간 + 5분으로 설정
    expires.setMinutes(expires.getMinutes() + 5);
    res.writeHead(302, {//302는 redirection
      Location: '/',
      'Set-Cookie': `name=${encodeURIComponent(name)//한글이기 때문에
        }; Expires=${expires.toGMTString()//쿠키만료 기간(직접 적을 수 있음)
        }; HttpOnly; Path=/`,
    });
    res.end();
    // name이라는 쿠키가 있는 경우
  } else if (cookies.name) {
    res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
    res.end(`${cookies.name}님 안녕하세요`);
  } else {
    try {
      const data = await fs.readFile('./cookie2.html');
      res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
      res.end(data);
    } catch (err) {
      res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });
      res.end(err.message);
    }
  }
})
  .listen(8084, () => {
    console.log('8084번 포트에서 서버 대기 중입니다!');
  });
```

<br/>

### **🍳 쿠키 옵션**

Set-Cookie 시 다양한 옵션이 있음

`쿠키명=쿠키값`

- 기본적인 쿠키의 값입니다. 

- mycookie=test 또는 name=zerocho 같이 설정합니다.

`Expires=날짜`

- 만료 기한

- 이 기한이 지나면 쿠키가 제거

- 기본값은 클라이언트가 종료될 때까지

`Max-age=초`

- Expires와 비슷하지만 날짜 대신 초를 입력할 수 있다. 

- 해당 초가 지나면 쿠기가 제거

- Expires보다 우선

`Domain=도메인명`

- 쿠키가 전송될 도메인을 특정할 수 있다. 

- 기본값은 현재 도메인

`Path=URL`

- 쿠키가 전송될 URL을 특정할 수 있다. 

- 기본값은 `/`이고 이 경우 모든 URL에서 쿠키를 전송할 수 있다.

`Secure`

- HTTPS일 경우에만 쿠키가 전송

`HttpOnly`

- 설정 시 자바스크립트에서 쿠키에 접근할 수 없다. 

- 쿠키 조작을 방지하기 위해 설정하는 것이 좋다.

<br/>

### **🍳 쿠키 서버 실행하기**

> [nodejs-book/ch4/4.3 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch4/4.3){:target="_blank"}

localhost:8084 포트에 접속

- Application 탭(F12) 열기

- 로그인을 하면 쿠키가 생성됨

![로그인](https://github.com/leekoby/leekoby.github.io/assets/118284808/12bfad10-f1de-466b-851b-9be46be614ad){: width="500" height="500" }

<br/>

### **🍳 세션 사용하기**

쿠키의 정보는 노출되고 수정되는 위험이 있음

- 중요한 정보는 서버에서 관리하고 클라이언트에는 세션 키만 제공

- 서버에 세션 객체(session) 생성 후, uniqueInt(키)를 만들어 속성명으로 사용

- 속성 값에 정보 저장하고 uniqueInt를 클라이언트에 보냄

🔻session.js

```js
const http = require('http');
const fs = require('fs').promises;
const path = require('path');

const parseCookies = (cookie = '') =>
  cookie
    .split(';')
    .map(v => v.split('='))
    .reduce((acc, [k, v]) => {
      acc[k.trim()] = decodeURIComponent(v);
      return acc;
    }, {});

const session = {};

http.createServer(async (req, res) => {
  const cookies = parseCookies(req.headers.cookie);
  if (req.url.startsWith('/login')) {
    const url = new URL(req.url, 'http://localhost:8085');
    const name = url.searchParams.get('name');
    const expires = new Date();
    expires.setMinutes(expires.getMinutes() + 5);
    const uniqueInt = Date.now();
    session[uniqueInt] = {
      name,
      expires,
    };
    res.writeHead(302, {
      Location: '/',
      'Set-Cookie': `session=${uniqueInt}; 
      Expires=${expires.toGMTString()}; HttpOnly; Path=/`,
    });
    res.end();
  // 세션 쿠키가 존재하고, 만료 기간이 지나지 않았다면
  } else if (cookies.session && session[cookies.session].expires > new Date()) {
    res.writeHead(200, { 'Content-Type': 'text/plain; charset=utf-8' });
    res.end(`${session[cookies.session].name}님 안녕하세요`);
  } else {
    try {
      const data = await fs.readFile(path.join(__dirname, 'cookie2.html'));
      res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
      res.end(data);
    } catch (err) {
      res.writeHead(500, { 'Content-Type': 'text/plain; charset=utf-8' });
      res.end(err.message);
    }
  }
})
  .listen(8085, () => {
    console.log('8085번 포트에서 서버 대기 중입니다!');
  });
```

<br/>

### **🍳 세션 서버 실행하기**

> localhost:8085

![로그인 이후](https://github.com/leekoby/leekoby.github.io/assets/118284808/4ff8a7bf-c4df-4f07-8bc2-b8b58de34c16){: width="500" height="500" }

실제 배포용 서버에서는 세션을 위와 같이 변수에 저장하지 않는다. 

서버가 멈추거나 재시작되면 메모리에 저장된 변수가 초기화되기 때문입

또한, 서버의 메모리가 부족하면 세션을 저장하지 못하는 문제도 생긴다. 

그래서 보통은 레디스(Redis)나 멤캐시드(Memcached) 같은 데이터베이스에 넣어둔다.

<br/>

---

<br/>

## **💻 http와 http2**

### **🍳 https**

웹 서버에 SSL 암호화를 추가하는 모듈

- 오고 가는 데이터를 암호화해서 중간에 다른 사람이 요청을 가로채더라도 내용을 확인할 수 없음

- 요즘에는 https 적용이 필수(개인 정보가 있는 곳은 특히)

![https](https://github.com/leekoby/leekoby.github.io/assets/118284808/27a2777e-49a6-410b-8d6b-909b3ee8b97b){: width="500" height="500" }


<br/>

### **🍳 https 서버**

http 서버를 https 서버로

- 암호화를 위해 인증서가 필요한데 발급받아야 함

- createServer가 인자를 두 개 받음

- 첫 번째 인자는 인증서와 관련된 옵션 객체

- pem, crt, key 등 인증서를 구입할 때 얻을 수 있는 파일 넣기

- 두 번째 인자는 서버 로직

🔻server1.js

```js
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

🔻server1-3.js

```js
const https = require('https');
const fs = require('fs');

https.createServer({
  cert: fs.readFileSync('도메인 인증서 경로'),
  key: fs.readFileSync('도메인 비밀키 경로'),
  ca: [
    fs.readFileSync('상위 인증서 경로'),
    fs.readFileSync('상위 인증서 경로'),
  ],
  //인증기관에서 가져와야함
}, (req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  res.write('<h1>Hello Node!</h1>');
  res.end('<p>Hello Server!</p>');
})
  .listen(443, () => {
    console.log('443번 포트에서 서버 대기 중입니다!');
  });
```

<br/>

### **🍳 http2**

SSL 암호화와 더불어 최신 HTTP 프로토콜인 http/2를 사용하는 모듈

- 요청 및 응답 방식이 기존 http/1.1보다 개선됨

- 웹의 속도도 개선됨

![http/1.1과 http/2의 비교](https://github.com/leekoby/leekoby.github.io/assets/118284808/abf75e76-bb71-4877-bab0-463eb47b12e1){: width="500" height="500" }

<br/>

### **🍳 http2 적용 서버**

https 모듈을 `http2`로, createServer 메서드를 `createSecureServer` 메서드로

🔻server1-4.js

```js
const http2 = require('http2');
const fs = require('fs');

http2.createSecureServer({
  cert: fs.readFileSync('도메인 인증서 경로'),
  key: fs.readFileSync('도메인 비밀키 경로'),
  ca: [
    fs.readFileSync('상위 인증서 경로'),
    fs.readFileSync('상위 인증서 경로'),
  ],
}, (req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
  res.write('<h1>Hello Node!</h1>');
  res.end('<p>Hello Server!</p>');
})
  .listen(443, () => {
    console.log('443번 포트에서 서버 대기 중입니다!');
  });
```

<br/>

---

<br/>

## **💻 cluster**

### **🍳 cluster**

기본적으로 싱글 스레드인 노드가 CPU 코어를 모두 사용할 수 있게 해주는 모듈

- 포트를 공유하는 노드 프로세스를 여러 개 둘 수 있음

- 요청이 많이 들어왔을 때 병렬로 실행된 서버의 개수만큼 요청이 분산됨

- 서버에 무리가 덜 감

- 코어가 8개인 서버가 있을 때: 보통은 코어 하나만 활용

- cluster로 코어 하나당 노드 프로세스 하나를 배정 가능

- 성능이 8배가 되는 것은 아니지만 개선됨

- 단점: 컴퓨터 자원(메모리, 세션 등) 공유 못 함

  -Redis 등 별도 서버로 해결

<br/>

### **🍳 서버 클러스터링**

마스터 프로세스와 워커 프로세스

마스터 프로세스는 CPU 개수만큼 워커 프로세스를 만듦(worker_threads랑 구조 비슷)

🔻cluster.js

```js
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  console.log(`마스터 프로세스 아이디: ${process.pid}`);
  // CPU 개수만큼 워커를 생산
  for (let i = 0; i < numCPUs; i += 1) {
    cluster.fork();
  }
  // 워커가 종료되었을 때
  cluster.on('exit', (worker, code, signal) => {
    console.log(`${worker.process.pid}번 워커가 종료되었습니다.`);
    console.log('code', code, 'signal', signal);
    cluster.fork();
  });
} else {
  // 워커들이 포트에서 대기
  http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
    res.write('<h1>Hello Node!</h1>');
    res.end('<p>Hello Cluster!</p>');
    setTimeout(() => { // 워커 존재를 확인하기 위해 1초마다 강제 종료
      process.exit(1);
    }, 1000);
  }).listen(8086);

  console.log(`${process.pid}번 워커 실행`);
}
```

![클러스터링](https://github.com/leekoby/leekoby.github.io/assets/118284808/aa13996e-6b34-409b-84e8-bc976ee1b61f){: width="500" height="500" }

<br/>

### **🍳 워커 프로세스 개수 확인하기**

요청이 들어올 때마다 서버 종료되도록 설정

- 실행한 컴퓨터의 코어가 8개이면 8번 요청을 받고 종료됨

![워커 프로세스 개수 확인하기](https://github.com/leekoby/leekoby.github.io/assets/118284808/c2f91b56-799c-4c97-a749-0d168d0f4256){: width="500" height="500" }

<br/>

### **🍳 워커 프로세스 다시 살리기**

워커가 죽을 때마다 새로운 워커를 생성

- 이 방식은 좋지 않음

- 오류 자체를 해결하지 않는 한 계속 문제가 발생

- 그래도 예기치 못한 에러로 인해 서버가 종료되는 현상을 방지할 수 있어 클러스터링을 적용해두는 것이 좋다.

![워커 프로세스 다시 살리기](https://github.com/leekoby/leekoby.github.io/assets/118284808/8a0f2d83-0147-4437-a91a-051ddc7c079d){: width="500" height="500" }

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

> [http 모듈 소개](https://nodejs.org/dist/latest-v18.x/docs/api/http.html){:target="_blank"}
{:.prompt-tip }

> [쿠키 설명](https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies){:target="_blank"}
{:.prompt-tip }

> [세션 설명](https://developer.mozilla.org/ko/docs/Web/HTTP/Session){:target="_blank"}
{:.prompt-tip }

> [https 모듈 소개](https://nodejs.org/dist/latest-v18.x/docs/api/https.html){:target="_blank"}
{:.prompt-tip }

> [http2 모듈 소개](https://nodejs.org/dist/latest-v18.x/docs/api/http2.html){:target="_blank"}
{:.prompt-tip }

> [cluster 모듈 소개](https://nodejs.org/dist/latest-v18.x/docs/api/cluster.html){:target="_blank"}
{:.prompt-tip }

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}