---
layout: post
title: 6. 익스프레스 웹 서버 만들기
author: admin
date: 2023-04-25 00:00:00 +900
lastmod: 2023-04-25 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, NODEJS교과서] # 대문자로 작성
tags: [node, nodejs, 노드교과서] # 소문자로 작성
---

> 기존 블로그에 작성했던 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `NODEJS`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---

<br/>

# **🌈 익스프레스 웹 서버 만들기**

## **💻 익스프레스 프로젝트 시작하기**

### **🍳 Express**

http 모듈로 웹 서버를 만들 때 코드가 보기 좋지 않고, 확장성도 떨어짐

- 프레임워크로 해결

- 대표적인 것이 Express(익스프레스), Koa(코아), Hapi(하피)

- 코드 관리도 용이하고 편의성이 많이 높아짐

>express 순서: app->app관련 설정 ->공통 미들웨어 -> 라우터 (범위 넓은 얘들은 뒤로)-> 에러 미들웨어

> tip
> 
> - 한 router에 send 두번 이상 나오면 에러
> - res.json(json 응답)은 return이 아님. 밑의 코드 실행 가능함
> - res.render(응답을 보내는 것)
> - throw new err-> try catch에서 error가 있는 경우가 많음 
>   - next(에러처 - 리 미들웨어로 넘어감)에 넣어 서 처리
>- next('route')는 다음 라우터를 실행
{:.prompt-tip}

![express, koa, hapi 다운로드 수 비교](https://github.com/leekoby/leekoby.github.io/assets/118284808/087d664f-6257-40ea-a9b9-69d0dacea2b2)

<br/>

### **🍳 package.json 만들기**

직접 만들거나 npm init 명령어 생성

nodemon이 소스 코드 변경 시 서버를 재시작해줌

🔻package.json

```js
{
  "name": "learn-express",
  "version": "0.0.1",
  "description": "익스프레스를 배우자",
  "main": "app.js",
  "scripts": {
    "start": "nodemon app"
  },
  "author": "leekoby",
  "license": "MIT",
  "devDependencies": {
    "express": "^4.18.2",
    "nodemon": "^3.0.1"
  }
}
```

또는 npm init 후 `npm i`를 통한 직접 설치 

```sh
npm i express
npm i -D nodemon
```

> scripts 부분에 `nodemon 파일명` 을 꼭 넣어주자

<br/>

### **🍳 app.js 작성하기**

서버 구동의 핵심이 되는 파일

- `app.set(‘port’, 포트)`로 서버가 실행될 포트 지정

- `app.get(‘주소’, 라우터)`로 GET 요청이 올 때 어떤 동작을 할지 지정

- `app.listen(‘포트’, 콜백)`으로 몇 번 포트에서 서버를 실행할지 지정

🔻app.js

```js
const express = require('express');

const app = express();
app.set('port', process.env.PORT || 3000);

app.get('/', (req, res) => {
  res.send('Hello, Express');
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기 중');
});
```

<br/>

### **🍳 서버 실행하기**

- `app.js`: 핵심 서버 스크립트

- `public`: 외부에서 접근 가능한 파일들 모아둠

- `views`: 템플릿 파일을 모아둠

- `routes`: 서버의 라우터와 로직을 모아둠

추후에 models를 만들어 데이터베이스 사용

<br/>

### **🍳 익스프레스 서버 실행하기**

> npm start

package.json의 start 스크립트 콘솔에서 실행

localhost:3000

![익스프레스 서버 실행하기](https://github.com/leekoby/leekoby.github.io/assets/118284808/5557dffa-b06c-4eeb-b006-bcd00b0392b4)

<br/>

### **🍳 HTML 서빙하기**

🔻 index.html

```html
<html>
<head>
  <meta charset="UTF-8" />
  <title>익스프레스 서버</title>
</head>
<body>
  <h1>익스프레스</h1>
  <p>배워봅시다.</p>
</body>
</html>
```

🔻 app.js

```js
const express = require('express');
const path = require('path');

const app = express();
app.set('port', process.env.PORT || 3000);
app.get('/', (req, res) => {
  // res.send('Hello, Express');
  res.sendFile(path.join(__dirname, '/index.html'));
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기 중');
});
```

![HTML 서빙하기](https://github.com/leekoby/leekoby.github.io/assets/118284808/005a4039-7aed-419d-b0a7-f0bf30c0555f)

<br/>

---

<br/>

## **💻 자주 사용하는 미들웨어**

### **🍳 미들웨어**

- 요청과 응답의 중간에 위치하여 미들웨어

- 라우터와 에러 핸들러 또한 미들웨어의 일종

- app.use(미들웨어)로 사용
  
  - 공통된 코드들을 모든 라우터에서 실행해줌

- 위에서 아래로 순서대로 실행됨.

- 미들웨어는 req, res, next가 매개변수인 함수
  
  - next 매개변수를 실행해줘야 다음 라우터 중 일치하는걸 찾아줌

- `req`: 요청, `res`: 응답 조작 가능

- `next()`로 다음 미들웨어로 넘어감.

- app.use나 app.get 같은 라우터에 미들웨어를 여러 개 장착할 수 있다.

🔻 app.js

```js
...
app.set('port', process.env.PORT || 3000);

app.use((req, res, next) => {
  console.log('모든 요청에 다 실행됩니다.');
  next();
});
app.get('/', (req, res, next) => {
  console.log('GET / 요청에서만 실행됩니다.');
  next();
}, (req, res) => {
  throw new Error('에러는 에러 처리 미들웨어로 갑니다.')
});

app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).send(err.message);
});

app.listen(app.get('port'), () => {
...
```

**미들웨어가 실행되는 경우**

|||
|:-|:-|
|app.use(미들웨어)|모든 요청에서 미들웨어 실행|
|app.use('/abc',미들웨어)|abc로 시작하는 요청에서 미들웨어 실행|
|app.post('/abc',미들웨어)|abc로 시작하는 POST 요청에서 미들웨어 실행|

<br/>

### **🍳 에러 처리 미들웨어**

> `err`, `req`, `res`, `next` 까지 매개변수가 4개 🎯중요
{:.prompt-warning}

- 첫 번째 err에는 에러가 관한 정보가 담김

- res.status 메서드로 HTTP 상태 코드를 지정 가능(기본값 200)

- 에러 처리 미들웨어를 안 연결해도 익스프레스가 에러를 알아서 처리해주긴 함.

- 특별한 경우가 아니면 가장 아래에 위치하도록 함.

- 보통 router들은 res.send 하면 거기에서 끝내줘야함

![에러 처리 미들웨어](https://github.com/leekoby/leekoby.github.io/assets/118284808/6b9c92a5-b2f4-444a-8f05-73c861450338)


🔻app.js
```js
...
app.use((err, req, res, next) => {
  console.error(err); 
  //서버에 어떤 에러인지 알려줌
  res.send('에러났어'); 
  //웹엔 어떤 에러인지 알려주면 보안 문제 생길수도 있음
  res.status(500).send(err.message);
  //status(500)라고 브라우저에 뜸(뻥칠수도 있음)
});
...
```
app.use((err, req, res, next) 바로 위에 app.use(req,res,next)=>{res.send('404지롱')} 쓰면 404 에러임을 의미

### **🍳 자주 쓰는 미들웨어**

morgan cookie-parser express-session dotenv 설치

```sh
npm i morgan cookie-parser express-session dotenv
```
- app.use로 장착

- 내부에서 알아서 next를 호출해서 다음 미들웨어로 넘어감

🔻 app.js

```js
const express = require('express');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const dotenv = require('dotenv');
const path = require('path');

dotenv.config();
const app = express();
app.set('port', process.env.PORT || 3000);

app.use(morgan('dev'));
app.use('/', express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
  name: 'session-cookie',
}));

app.use((req, res, next) => {
  console.log('모든 요청에 다 실행됩니다.');
  next();
});
...
```

🔻 .env

```js
COOKIE_SECRET=cookiesecret
```

<br/>

### **🍳 dotenv**

.env 파일을 읽어서 process.env로 만듦

- dot(점) + env

- process.env.COOKIE_SECRET에 cookiesecret 값이 할당됨(키=값 형식)

- 비밀 키들을 소스 코드에 그대로 적어두면 소스 코드가 유출되었을 때 비밀 키도 같이 유출됨

- .env 파일에 비밀 키들을 모아두고 .env 파일만 잘 관리하면 됨

<br/>

### **🍳 morgan**

서버로 들어온 요청과 응답을 기록해주는 미들웨어

- 로그의 자세한 정도 선택 가능(dev, tiny, short, common, combined)

  - 예시) GET / 200 51.267 ms – 1539

- 순서대로 HTTP요청 요청주소 상태코드 응답속도 – 응답바이트

- 개발환경에서는 dev, 배포환경에서는 combined를 애용함.(좀 더 자세한 정보 제공)

![morgan](https://github.com/leekoby/leekoby.github.io/assets/118284808/f6c23f73-3ddb-4396-b568-fdb9273ad12e)

<br/>

### **🍳 static**

정적인 파일들을 제공하는 라우터 역할

기본적으로 제공되기 때문에 따로 설치할 필요없이 express 객체 안에서 꺼내 씀

- 인수로 정적 파일의 경로를 제공

- 파일이 있을 때 fs.readFile로 직접 읽을 필요 없음

- 요청하는 파일이 없으면 알아서 next를 호출해 다음 미들웨어로 넘어감

- 파일을 발견했다면 다음 미들웨어는 실행되지 않음

```js
app.use('요청 경로', express.static('실제 경로'));

app.use('/', express.static(path.join(__dirname, 'public')));
```

컨텐츠 요청 주소와 실제 컨텐츠의 경로를 다르게 만들 수 있음

- 요청 주소 localhost:3000/stylesheets/style.css

- 실제 컨텐츠 경로 /public/stylesheets/style.css

- 서버의 구조를 파악하기 어려워져서 보안에 도움이 됨

<br/>

### **🍳 body-parser**

요청의 본문을 해석해주는 미들웨어

- 폼 데이터나 AJAX 요청의 데이터 처리

- json 미들웨어는 요청 본문이 json인 경우 해석, urlencoded 미들웨어는 폼 요청 해석

- put이나 patch, post 요청 시에 req.body에 프런트에서 온 데이터를 넣어줌

```js
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
```

버퍼 데이터나 text 데이터일 때는 body-parser를 직접 설치해야 함

```sh
$ npm i body-parser
```

```js
const bodyParser = require('body-parser');
app.use(bodyParser.raw());
app.use(bodyParser.text());
```

Multipart 데이터(이미지, 동영상 등)인 경우는 다른 미들웨어를 사용해야 함

<br/>

### **🍳 cookie-parser**

요청 헤더의 쿠키를 해석해주는 미들웨어

- parseCookies 함수와 기능 비슷

- req.cookies 안에 쿠키들이 들어있음

- 비밀 키로 쿠키 뒤에 서명을 붙여 내 서버가 만든 쿠키임을 검증할 수 있음

```js
app.use(cookieParser(비밀 키));
```

- 실제 쿠키 옵션들을 넣을 수 있음

- expires, domain, httpOnly, maxAge, path, secure, sameSite 등

- 지울 때는 clearCookie로(expires와 maxAge를 제외한 옵션들이 일치해야 함)

```js
res.cookie('name', 'zerocho', { 
  expires: new Date(Date.now() + 900000),
  httpOnly: true, 
  secure: true,
});
res.clearCookie('name', 'zerocho', { httpOnly: true, secure: true });
```

signed라는 옵션을 true로 설정하면 쿠키 뒤에 서명이 붙는다.

 내 서버가 쿠키를 만들었다는 것을 검증할 수 있으므로 대부분의 경우 서명 옵션을 켜두는 것이 좋다. 
 
 서명을 위한 비밀 키는 cookieParser 미들웨어에 인수로 넣은 process.env.COOKIE_SECRET이 된다.

<br/>

### **🍳  express-session**

세션 관리용 미들웨어

- 세션 쿠키에 대한 설정(secret: 쿠키 암호화, cookie: 세션 쿠키 옵션)

- 세션 쿠키는 앞에 s%3A가 붙은 후 암호화되어 프런트에 전송됨

- `resave`: 요청이 왔을 때 세션에 수정사항이 생기지 않아도 다시 저장할지 여부

- `saveUninitialized`: 세션에 저장할 내역이 없더라도 세션을 저장할지

- `req.session.save`로 수동 저장도 가능하지만 할 일 거의 없음

![express-session](https://github.com/leekoby/leekoby.github.io/assets/118284808/91116d64-a03a-4300-9bca-9b7b7210a674)

<br/>

### **🍳 미들웨어의 특성 활용하기**

```js
app.use((req, res, next) => {
  console.log('모든 요청에 다 실행됩니다.');
  next();
});
```

req, res, next를 매개변수로 갖는 함수(에러 처리 미들웨어만 예외적으로 err, req, res, next를 가짐)

익스프레스 미들웨어들도 다음과 같이 축약 가능

app.use나 app.get, app.post 등으로 장착

순서가 중요

static 미들웨어에서 파일을 찾으면 next를 호출 안 하므로 json, urlencoded, cookieParser는 실행되지 않음

```js
app.use(
  morgan('dev'),
  express.static('/', path.join(__dirname, 'public')),
  express.json(),
  express.urlencoded({ extended: false }),
  cookieParser(process.env.COOKIE_SECRET),
);
```

<br/>

### **🍳 next**

next를 호출해야 다음 코드로 넘어감

- next를 주석 처리하면 응답이 전송되지 않음

- 다음 미들웨어(라우터 미들웨어)로 넘어가지 않기 때문

- next에 인수로 값을 넣으면 에러 핸들러로 넘어감(‘route’인 경우 다음 라우터로)

![next의 동작](https://github.com/leekoby/leekoby.github.io/assets/118284808/c4dc3563-8429-4b68-a721-fcd5bb330899)

<br/>

### **🍳 미들웨어간 데이터 전달하기**

미들웨어 간에 데이터를 전달하는 방법도 있다. 

세션을 사용한다면 `req.session` 객체에 데이터를 넣어도 되지만, 세션이 유지되는 동안에 데이터도 계속 유지된다는 단점이 있다. 

만약 요청이 끝날 때까지만 데이터를 유지하고 싶다면 `res.locals` 객체에 데이터를 넣어두면 됩니다.

```js
app.use((req, res, next) => {
  res.locals.data = '데이터 넣기';
  next();
}, (req, res, next) => {
  console.log(res.locals.data); // 데이터 받기
  next();
});
```
현재 요청이 처리되는 동안 `res.locals` 객체를 통해 미들웨어 간에 데이터를 공유할 수 있다.

새로운 요청이 오면 `res.locals`는 초기화된다.

<br/>

### **🍳 app.set과의 차이**

app.set으로 익스프레스에서 데이터를 저장할 수 있다. 

app.get 또는 req.app.get으로 어디서든지 데이터를 가져올 수 있다. 

하지만 app.set을 사용하지 않고 res.locals 객체에 데이터를 넣어서 다음 미들웨어로 전달하는 이유가 있다. 

app.set은 익스프레스에서 전역적으로 사용되므로 하나의 요청 안에서만 유지되어야 하는 값을 넣기에는 부적절하다. 

app.set은 앱 전체의 설정을 공유할 때 사용하면 된다.

res.locals 객체는 하나의 요청 안에서만 유지되므로 res.locals 객체를 통해 요청에 종속되는 데이터를 전달하는 것이 좋다.

<br/>


### **🍳 미들웨어 확장하기**

다음 예제의 두 방식은 같은 기능을 한다.

```js
app.use(morgan('dev'));
// 또는
app.use((req, res, next) => {
  morgan('dev')(req, res, next);
});
```

이 패턴이 유용한 이유는 기존 미들웨어의 기능을 확장할 수 있기 때문

- 예를 들어 다음과 같이 분기 처리를 할 수도 있다. 

- 조건문에 따라 다른 미들웨어를 적용하는 코드

```js
app.use((req, res, next) => {
  if (process.env.NODE_ENV === 'production') {
    morgan('combined')(req, res, next);
  } else {
    morgan('dev')(req, res, next);
  }
});
```

<br/>

### **🍳 multer**

이미지, 동영상 등을 비롯한 여러 가지 파일을 멀티파트 형식으로 업로드할 때 사용하는 미들웨어

멀티파트 형식이란 다음과 같이 enctype이 multipart/form-data인 폼을 통해 업로드하는 데이터의 형식을 의미


🔻 multipart.html

```html
<form action="/upload" method="post" enctype="multipart/form-data">
  <input type="file" name="image" />
  <input type="text" name="title" />
  <button type="submit">업로드</button>
</form>
```

![멀티파트 데이터 형식](https://github.com/leekoby/leekoby.github.io/assets/118284808/81355a3c-a489-470e-984f-4da08f12cd9d)

폼을 통해 업로드하는 파일은 body-parser로는 처리할 수 없고 직접 파싱(해석)하기도 어려우므로 multer라는 미들웨어를 따로 사용하면 편리하다. 

<br/>

### **🍳 multer 설정하기**

#### multer 설치

```sh
npm i multer
```

#### multer 기본적인 설정

```js
const multer = require('multer');

const upload = multer({
  storage: multer.diskStorage({
    destination(req, file, done) {
      done(null, 'uploads/');
    },
    filename(req, file, done) {
      const ext = path.extname(file.originalname);
      done(null, path.basename(file.originalname, ext) + Date.now() + ext);
    },
  }),
  limits: { fileSize: 5 * 1024 * 1024 },
});
```

- multer 함수의 인수로 설정을 넣는다.

- storage는 어디에(destination) 어떤 이름으로(filename) 저장할지

- destination과 filename 함수의 req 매개변수에는 요청에 대한 정보

- file 객체에는 업로드한 파일에 대한 정보

- done 매개변수는 함수
  
  - 첫 번째 인수에는 에러가 있다면 에러 
  
  - 두 번째 인수에는 실제 경로나 파일 이름 
  
  - req나 file의 데이터를 가공해서 done으로 넘기는 형식

- 현재 설정으로는 uploads라는 폴더에 [파일명+현재시간.확장자] 파일명으로 업로드하고 있다. 

- 현재 시간을 넣어주는 이유는 업로드하는 파일명이 겹치는 것을 막기 위함

- limits 속성에는 업로드에 대한 제한 사항을 설정할 수 있다.
  
  - 현재 코드에서는 파일 사이즈(fileSize, 바이트 단위)는 5MB로 제한

다만, 위 설정을 실제로 활용하려면 서버에 uploads 폴더가 꼭 존재해야 한다. 

없다면 직접 만들어주거나 다음과 같이 fs 모듈을 사용해서 서버를 시작할 때 생성해야 한다.

<br/>

### **🍳 multer 미들웨어들**

single과 none, array, fields 미들웨어 존재

- single은 하나의 파일을 업로드할 때, none은 파일은 업로드하지 않을 때

- req.file 안에 업로드 정보 저장

- array와 fields는 여러 개의 파일을 업로드 할 때 사용

- array는 하나의 요청 body 이름 아래 여러 파일이 있는 경우

- fields는 여러 개의 요청 body 이름 아래 파일이 하나씩 있는 경우

![단일 파일 업로드](https://github.com/leekoby/leekoby.github.io/assets/118284808/24e83186-6426-4b41-9413-3ddd406b29ac)

- 두 경우 모두 업로드된 이미지 정보가 req.files 아래에 존재

![여러 파일 업로드](https://github.com/leekoby/leekoby.github.io/assets/118284808/9eac1083-063d-4352-96b7-f922ba26ca71)


![multer의 미들웨어](https://github.com/leekoby/leekoby.github.io/assets/118284808/92d63fa5-bad5-45eb-abb8-bd467348de27){: width="500" height="500" }

<br/>

---

<br/>

## **💻 Router로 객체 라우팅 분리하기**

### **🍳 express.Router**

app.js가 길어지는 것을 막을 수 있음

userRouter의 get은 /user와 /가 합쳐져서 GET /user/가 됨

🔻routes/index.js

```js
const express = require('express');

const router = express.Router();

// GET / 라우터
router.get('/', (req, res) => {
  res.send('Hello, Express');
});

module.exports = router;
```

🔻routes/user.js

```js
const express = require('express');

const router = express.Router();

// GET /user 라우터
router.get('/', (req, res) => {
  res.send('Hello, User');
});

module.exports = router;
```

🔻app.js

```js
const express = require('express');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const dotenv = require('dotenv');
const path = require('path');

dotenv.config();
const indexRouter = require('./routes');
const userRouter = require('./routes/user');

const app = express();
app.set('port', process.env.PORT || 3000);

app.use(morgan('dev'));
app.use('/', express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
  name: 'session-cookie',
}));

app.use('/', indexRouter);
app.use('/user', userRouter);

app.use((req, res, next) => {
  res.status(404).send('Not Found');
});

app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).send(err.message);
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기 중');
});
```

<br/>

### **🍳 라우트 매개변수**

:id를 넣으면 req.params.id로 받을 수 있음

- 동적으로 변하는 부분을 라우트 매개변수로 만듦

```js
router.get('/user/:id', (req, res) => {
  console.log(req.params, req.query);
});
```

- 일반 라우터보다 뒤에 위치해야 함

```js
router.get('/user/:id', (req, res) => {
  console.log('얘만 실행됩니다.');
});
router.get('/user/like', (req, res) => {
  console.log('전혀 실행되지 않습니다.');
});
```

- /users/123?limit=5&skip=10 주소 요청인 경우

```js
{ id: '123' } { limit: '5', skip: '10' }
```

<br/>

### **🍳 404 미들웨어**

요청과 일치하는 라우터가 없는 경우를 대비해 404 라우터를 만들기

이게 없으면 단순히 Cannot GET 주소 라는 문자열이 표시됨

```js
app.use((req, res, next) => {
  res.status(404).send('Not Found');
});
```

<br/>

### **🍳 라우터 그룹화하기**

주소는 같지만 메서드가 다른 코드가 있을 때

```js
router.get('/abc', (req, res) => {
  res.send('GET /abc');
});
router.post('/abc', (req, res) => {
  res.send('POST /abc');
});
```

router.route로 묶음

```js
router.route('/abc')
  .get((req, res) => {
    res.send('GET /abc');
  })
  .post((req, res) => {
    res.send('POST /abc');
  });
```

<br/>

### **🍳**

<br/>

<br/>

---

<br/>

## **💻 req, res 객체 살펴보기**

익스프레스의 req, res 객체는 http 모듈의 req, res 객체를 확장한 것이다.

기존 http 모듈의 메서드도 사용할 수 있고, 익스프레스가 추가한 메서드나 속성을 사용할 수도 있다. 

### **🍳 req**

- `req.app`: req 객체를 통해 app 객체에 접근할 수 있다. req.app.get('port')와 같은 식으로 사용

- `req.body`: body-parser 미들웨어가 만드는 요청의 본문을 해석한 객체

- `req.cookies`: cookie-parser 미들웨어가 만드는 요청의 쿠키를 해석한 객체

- `req.ip`: 요청의 ip 주소

- `req.params`: 라우트 매개변수에 대한 정보가 담긴 객체

- `req.query`: 쿼리스트링에 대한 정보가 담긴 객체

- `req.signedCookies`: 서명된 쿠키들은 req.cookies 대신 여기에 담겨 있다.

- `req.get(헤더 이름)`: 헤더의 값을 가져오고 싶을 때 사용하는 메서드

<br/>

### **🍳 res**

- `res.app`: req.app처럼 res 객체를 통해 app 객체에 접근할 수 있다.

- `res.cookie(키, 값, 옵션)`: 쿠키를 설정하는 메서드

- `res.clearCookie(키, 값, 옵션)`: 쿠키를 제거하는 메서드

- `res.end()`: 데이터 없이 응답

- `res.json(JSON)`: JSON 형식의 응답

- `res.locals`: 하나의 요청 안에서 미들웨어 간에 데이터를 전달하고 싶을 때 사용하는 객체

- `res.redirect(주소)`: 리다이렉트할 주소와 함께 응답

- `res.render(뷰, 데이터)`: 다음 절에서 다룰 템플릿 엔진을 렌더링해서 응답할 때 사용하는 메서드

- `res.send(데이터)`: 데이터와 함께 응답. 데이터는 문자열일 수도, HTML일 수도, 버퍼일 수도, 객체나 배열일 수도 있다.

- `res.sendFile(경로)`: 경로에 위치한 파일을 응답

- `res.set(헤더, 값)`: 응답의 헤더를 설정

- `res.status(코드)`: 응답 시의 HTTP 상태 코드를 지정

<br/>

### **🍳 메서드 체이닝**

```js
res
  .status(201)
  .cookie('test', 'test')
  .redirect('/admin');
```

<br/>

---

<br/>

## **💻 템플릿 엔진 사용하기**


### **🍳 템플릿 엔진**

HTML의 정적인 단점을 개선

- 반복문, 조건문, 변수 등을 사용할 수 있음

- 동적인 페이지 작성 가능

- PHP, JSP와 유사함

<br/>

### **🍳 Pug(구 Jade)**

문법이 Ruby와 비슷해 코드 양이 많이 줄어듦

HTML과 많이 달라 호불호가 갈림

익스프레스에 app.set으로 퍼그 연결

```sh
npm i pug
```

익스프레스와 연결하려면 app.js에 다음 부분이 들어 있어야 한다.

🔻app.js

```js
app.set('port', process.env.PORT || 3000);
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');

app.use(morgan('dev'));
```


<br/>

### **🍳 Pug – HTML 표현**

- 기존 HTML과 다르게 화살괄호(< >)와 닫는 태그가 없다. 

- 탭 또는 스페이스로만 태그의 부모 자식 관계를 규명

- 탭 한 번, 스페이스 두 번 또는 스페이스 네 번 모두 상관없다. 

- 모든 파일에 동일한 종류의 들여쓰기를 적용

- 자식 태그는 부모 태그보다 들여쓰기되어 있어야 한다. 

- 들여쓰기에 오류가 있으면 제대로 렌더링되지 않으니 주의

- doctype html은 `<!DOCTYPE html>`과 같다. 

- html, head, title 태그에서는 자식 태그일수록 한 단계씩 더 들여쓰기되어 있는 모습을 볼 수 있다. 

- 화살괄호가 없으므로 태그의 속성도 조금 다르게 표현

- 태그명 뒤에 소괄호로 묶어 적는다.

![pug image 1](https://github.com/leekoby/leekoby.github.io/assets/118284808/ddfb5f3d-2fa9-40a6-a9ea-2c8a072b0115)

![pug image 2](https://github.com/leekoby/leekoby.github.io/assets/118284808/f037b8ed-af2e-473c-b6f0-d89152ca29e7)

<br/>

### **🍳 Pug - 변수**

```js
router.get('/', (req, res, next) => {
  res.render('index', { title: 'Express' });
});
```

res.render 메서드에 두 번째 인수로 변수 객체를 넣는 대신, res.locals 객체를 사용해서 변수를 넣을 수도 있다.

```js
router.get('/', (req, res, next) => {
  res.render('index', { title: 'Express' });
});
```

템플릿 엔진이 res.locals 객체를 읽어서 변수를 집어넣음

장점은 현재 라우터뿐만 아니라 다른 미들웨어에서도 res.locals 객체에 접근할 수 있다는 것

다른 미들웨어에서 템플릿 엔진용 변수를 미리 넣을 수도 있다.

`=`이나` #{}`으로 변수 렌더링 가능(= 뒤에는 자바스크립트 문법 사용 가능)

![Pug 변수](https://github.com/leekoby/leekoby.github.io/assets/118284808/a1087d6e-8e47-49d6-826a-753bb595233c)

<br/>

### **🍳 Pug – 파일 내 변수**

퍼그 파일 안에서 변수 선언 가능

-뒤에 자바스크립트 사용

![Pug – 파일 내 변수](https://github.com/leekoby/leekoby.github.io/assets/118284808/a7850a2b-dfa0-47cf-9e2d-373ead755b3e)

-  기본적으로 변수의 특수 문자를 HTML 엔티티(entity)로 이스케이프(escape)(문법과 관련 없는 문자로 바꾸는 행위)한다.

- 이스케이프를 원하지 않는다면 = 대신 !=을 사용

![이스케이프](https://github.com/leekoby/leekoby.github.io/assets/118284808/52e66182-52af-4f79-b96b-4aad28e529f5)

<br/>

### **🍳 Pug – 반복문**

HTML과 다르게 반복문도 사용할 수 있으며, 반복 가능한 변수인 경우에만 해당

다음과 같이 each로 반복문을 돌릴 수 있다. each 대신 for 가능

![each로 반복문](https://github.com/leekoby/leekoby.github.io/assets/118284808/34089473-40a3-42ec-b412-2987ce474076)

- 값과 인덱스 가져올 수 있음

![each로 반복문 값과 인덱스 가져올 수 있음](https://github.com/leekoby/leekoby.github.io/assets/118284808/c89851ea-4641-4611-9263-de476b5a0797)

<br/>

### **🍳 Pug – 조건문**

조건문으로 편리하게 분기 처리할 수 있으며 `if`, `else if`, `else`를 사용할 수 있다. 

![Pug – 조건문](https://github.com/leekoby/leekoby.github.io/assets/118284808/7311c647-d3c2-44f4-bc0b-37fc93f9fdce)

<br/>

### **🍳 Pug – include**

퍼그 파일에 다른 퍼그 파일을 넣을 수 있음

- 헤더, 푸터, 내비게이션 등의 공통 부분을 따로 관리할 수 있어 편리

- include로 파일 경로 지정

![Pug – include](https://github.com/leekoby/leekoby.github.io/assets/118284808/29bfd367-0cd2-42bc-8e03-e077c10a8738)

<br/>

### **🍳 Pug – extends와 block**

레이아웃을 정할 수 있음

- 공통되는 레이아웃을 따로 관리할 수 있어 좋음, include와도 같이 사용

![Pug – extends와 block](https://github.com/leekoby/leekoby.github.io/assets/118284808/3c6d601c-f976-43d5-902d-2eb4724bf913)


<br/>

### **🍳 넌적스**

Pug의 문법에 적응되지 않는다면 넌적스를 사용하면 좋음

- Pug를 지우고 Nunjucks 설치

```sh
npm i nunjucks
```

- 확장자는 html 또는 njk(view engine을 njk로)

view engine을 퍼그 대신 넌적스로 교체합니다.

🔻 app.js

```js
...
const path = require('path');
const nunjucks = require('nunjucks');

dotenv.config();
const indexRouter = require('./routes');
const userRouter = require('./routes/user');

const app = express();
app.set('port', process.env.PORT || 3000);
app.set('view engine', 'html');

nunjucks.configure('views', { 
  express: app,
  watch: true,
});

app.use(morgan('dev'));
...
```

퍼그와는 연결 방법이 다소 다르다. 

configure의 첫 번째 인수로 views 폴더의 경로를 넣고, 두 번째 인수로 옵션을 넣는다. 

이때 express 속성에 app 객체를 연결한다. 

watch 옵션이 true이면 HTML 파일이 변경될 때 템플릿 엔진을 다시 렌더링한다.

<br/>

### **🍳 넌적스 - 변수**

```js
<h1>{{title}}</h1>
<p>Welcome to {{title}}</p>
<button class="{{title}}" type="submit">전송</button>
<input placeholder="{{title}} 연습" />
```

넌적스에서 변수는 \{\{ \}\}로 감싼다.

```html
<h1>Express</h1>
<p>Welcome to Express</p>
<button class="Express" type="submit">전송</button>
<input placeholder="Express 연습" />
```

내부에 변수를 사용할 수도 있다. 

변수를 선언할 때는{% raw %} `{% set 변수 = '값' %}` {% endraw %} 를 사용

<br/>

### **🍳 넌적스 - 반복문**

{% raw %} {% %} {% endraw %} 안에 for in 작성(인덱스는 loop 키워드)

![넌적스 - 반복문](https://github.com/leekoby/leekoby.github.io/assets/118284808/c463b482-fad3-4067-841a-904cf43b1310)

<br/>

### **🍳 넌적스 - 조건문**

{% raw %} {% if %} {% endraw %} 안에 조건문 작성

![넌적스 - 조건문](https://github.com/leekoby/leekoby.github.io/assets/118284808/dddb480a-2774-4219-a28c-03f0dd85a8ce)

<br/>

### **🍳 넌적스 - include**

파일이 다른 파일을 불러올 수 있음

include에 파일 경로 넣어줄 수 있음

![넌적스 - include](https://github.com/leekoby/leekoby.github.io/assets/118284808/25444874-404d-47d2-ac3c-685f1a9b6f32)

<br/>

### **🍳 넌적스 - 레이아웃**

레이아웃을 정할 수 있음

공통되는 레이아웃을 따로 관리할 수 있어 좋음, include와도 같이 사용

![넌적스 - 레이아웃](https://github.com/leekoby/leekoby.github.io/assets/118284808/0b6bac23-a559-41ae-8ff9-7f27b30f60b0)


<br/>

### **🍳 에러 처리 미들웨어**

에러 발생 시 템플릿 엔진과 상관없이 템플릿 엔진 변수를 설정하고 error 템플릿을 렌더링함

res.locals.변수명으로도 템플릿 엔진 변수 생성 가능

process.env.NODE_ENV는 개발환경인지 배포환경인지 구분해주는 속성


🔻app.js

```js
const express = require('express');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const dotenv = require('dotenv');
const path = require('path');
const nunjucks = require('nunjucks');

dotenv.config();
const indexRouter = require('./routes');
const userRouter = require('./routes/user');

const app = express();
app.set('port', process.env.PORT || 3000);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});

app.use(morgan('dev'));
app.use('/', express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
  name: 'session-cookie',
}));

app.use('/', indexRouter);
app.use('/user', userRouter);

app.use((req, res, next) => {
  const error =  new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  next(error);
});

app.use((err, req, res, next) => {
  res.locals.message = err.message;
  res.locals.error = process.env.NODE_ENV !== 'production' ? err : {};
  res.status(err.status || 500);
  res.render('error');
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기 중');
});
```
![에러 스택 트레이스](https://github.com/leekoby/leekoby.github.io/assets/118284808/ea211106-96e9-4ba1-9987-d402d472fe7f)

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

[Express 공식 홈페이지](http://expressjs.com){:target="_blank"}
{:.prompt-info }

[퍼그 공식 홈페이지](https://pugjs.org){:target="_blank"}
{:.prompt-info }

[넌적스 공식 홈페이지](https://mozilla.github.io/nunjucks){:target="_blank"}
{:.prompt-info }

[morgan](https://github.com/expressjs/morgan){:target="_blank"}
{:.prompt-info }

[body-parser](https://github.com/expressjs/body-parser){:target="_blank"}
{:.prompt-info }

[cookie-parser](https://github.com/expressjs/cookie-parser){:target="_blank"}
{:.prompt-info }

[static](https://github.com/expressjs/serve-static){:target="_blank"}
{:.prompt-info }

[express-session](https://github.com/expressjs/session){:target="_blank"}
{:.prompt-info }

[multer](https://github.com/expressjs/multer){:target="_blank"}
{:.prompt-info }

[dotenv](https://github.com/motdotla/dotenv){:target="_blank"}
{:.prompt-info }

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}