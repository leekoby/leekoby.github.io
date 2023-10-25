---
layout: post
title: 10. 웹 API 서버 만들기
author: admin
date: 2023-04-29 00:00:00 +900
lastmod: 2023-04-29 00:00:00 +900
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

# **🌈 웹 API 서버 만들기**

## **💻 API 서버 이해하기**

API: Application Programming Interface

다른 애플리케이션에서 현재 프로그램의 기능을 사용할 수 있게 함

웹 API: 다른 웹 서비스의 기능을 사용하거나 자원을 가져올 수 있게 함

다른 사람에게 정보를 제공하고 싶은 부분만 API를 열고, 제공하고 싶지 않은 부분은 API를 만들지 않으면 됨

API에 제한을 걸어 일정 횟수 내에서만 가져가게 할 수도 있음

NodeBird에서는 인증된 사용자에게만 정보 제공

![API개념도와 서버 관계도](https://github.com/leekoby/leekoby.github.io/assets/118284808/a73a57fd-8925-42ac-8d5e-ab5e5ea8e96b)

<br/>

---

<br/>

## **💻 프로젝트 구조 갖추기**

### **🍳 폴더 구조 세팅**

폴더를 만들고 package.json 생성

생성 후 npm i로 패키지 설치

NodeBird에서 config, models, passport 모두 복사해서 붙여넣기

routes 폴더에서는 auth.js와 middlewares 재사용

.env 파일 복사

views 폴더를 만들고 error.html 파일 생성

🔻 view/error.html

```html
<!-- 아래 {}를 이중으로 바꿔주세요  -->
<h1>{message}</h1> 
<h2>{error.status}</h2>
<pre>{error.stack}</pre>
```

🔻 package.json
```js
{
  "name": "ch10",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "bcrypt": "^5.0.0",
    "cookie-parser": "^1.4.5",
    "dotenv": "^16.0.0",
    "express": "^4.17.1",
    "express-session": "^1.17.1",
    "morgan": "^1.10.0",
    "mysql2": "^2.1.0",
    "nunjucks": "^3.2.1",
    "passport": "^0.5.2",
    "passport-kakao": "1.0.1",
    "passport-local": "^1.0.0",
    "sequelize": "^6.0.0",
    "uuid": "^8.3.2"
  },
  "devDependencies": {
    "nodemon": "^2.0.3"
  }
}
```

```sh
npm i bcrypt cookie-parser dotenv express express-session mysql2 nunjucks passport passport-local
npm i sequelize uuid
npm i -D nodemon
```

<br/>

### **🍳 app.js 생성**

8002번 포트 사용

- 8001번을 사용하는 NodeBird 서비스와 8003을 사용할 nodecat 서비스와 함께 사용할 수 있음

- 콘솔을 여러 개 실행해 각각의 서비스를 돌리면 됨

- views/login.html 화면 생성

- NodeBird 서비스의 계정으로 로그인하면 됨(카카오톡 로그인은 안 됨)!

🔻 app.js 

```js
const express = require('express');
const path = require('path');
const cookieParser = require('cookie-parser');
const passport = require('passport');
const morgan = require('morgan');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();
const authRouter = require('./routes/auth');
const indexRouter = require('./routes');
const { sequelize } = require('./models');
const passportConfig = require('./passport');

const app = express();
passportConfig();
app.set('port', process.env.PORT || 8002);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});
sequelize.sync({ force: false })
  .then(() => {
    console.log('데이터베이스 연결 성공');
  })
  .catch((err) => {
    console.error(err);
  });

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
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
}));
app.use(passport.initialize());
app.use(passport.session());

app.use('/auth', authRouter);
app.use('/', indexRouter);

app.use((req, res, next) => {
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
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
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

### **🍳 도메인 모델 생성**

models/domain.js 작성

- API를 사용할 도메인(또는 호스트)을 저장하는 모델

- ENUM type으로 free나 premium만 쓸 수 있게 제한

- clientSecret은 uuid 타입으로

🔻 models/domain.js

```js
const Sequelize = require('sequelize');

class Domain extends Sequelize.Model {
  static initiate(sequelize) {
    Domain.init({
      host: {
        type: Sequelize.STRING(80),
        allowNull: false,
      },
      type: {
        type: Sequelize.ENUM('free', 'premium'),
        allowNull: false,
      },
      clientSecret: {
        type: Sequelize.UUID,
        allowNull: false,
      },
    }, {
      sequelize,
      timestamps: true,
      paranoid: true,
      modelName: 'Domain',
      tableName: 'domains',
    });
  }

  static associate(db) {
    db.Domain.belongsTo(db.User);
  }
};

module.exports = Domain;
```

<br/>

### **🍳 도메인 등록 라우터** 

routes/index에서 도메인 등록 라우터 생성

- uuid 패키지로 사용자가 등록한 도메인에 고유한 비밀번호 부여

- uuid는 충돌(고유하지 않은 상황) 위험이 있지만 매우 희박

- 비밀번호가 일치하는 요청만 API 응답

🔻routes/index.js

```js
const express = require('express');
const { renderLogin, createDomain } = require('../controllers');
const { isLoggedIn } = require('../middlewares');

const router = express.Router();

router.get('/', renderLogin);

router.post('/domain', isLoggedIn, createDomain);

module.exports = router;
```

🔻 controller/index.js

```js
const { v4: uuidv4 } = require('uuid');
const { User, Domain } = require('../models');

exports.renderLogin = async (req, res, next) => {
  try {
    const user = await User.findOne({
      where: { id: req.user?.id || null },
      include: { model: Domain },
    });
    res.render('login', {
      user,
      domains: user?.Domains,
    });
  } catch (err) {
    console.error(err);
    next(err);
  }
}

exports.createDomain = async (req, res, next) => {
  try {
    await Domain.create({
      UserId: req.user.id,
      host: req.body.host,
      type: req.body.type,
      clientSecret: uuidv4(),
    });
    res.redirect('/');
  } catch (err) {
    console.error(err);
    next(err);
  }
};

```

<br/>

### **🍳 도메인 등록하고 비밀번호 발급받기**

라우터 작성 후 localhost:8002 접속

- 도메인이 다른 프런트엔드에서 요청을 보내면 CORS 에러(10.7) 발생

- 로그인 후 localhost:8003(nodecat 서버) 등록

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/81f1af4c-64e6-4971-8b17-40c20c10cdd0)

<br/>

---

<br/>

## **💻 JWT 토큰으로 인증하기**

### **🍳 인증을 위한 JWT**

NodeBird가 아닌 다른 클라이언트가 데이터를 가져가게 하려면 인증 과정이 필요함

JWT(JSON Web Token)을 사용함

- 헤더.페이로드.시그니처로 구성됨

- 헤더: 토큰 종류와 해시 알고리즘 정보가 들어있음

- 페이로드: 토큰의 내용물이 인코딩된 부분

- 시그니처: 일련의 문자열로, 시그니처를 통해 토큰이 변조되었는지 여부 확인

- 시그니처는 JWT 비밀키로 만들어지고, 비밀키가 노출되면 토큰 위조 가능

![JWT 토큰 예시](https://github.com/leekoby/leekoby.github.io/assets/118284808/351164cd-84c6-46a1-8744-de17e60caf65)

<br/>

### **🍳 JWT 사용 시 주의점**

JWT에 민감한 내용을 넣으면 안 됨

- 페이로드 내용 볼 수 있음

- 그럼에도 사용하는 이유는 토큰 변조가 불가능하고, 내용물이 들어있기 때문

- 내용물이 들어있으므로 데이터베이스 조회를 하지 않을 수 있음(데이터베이스 조회는 비용이 큰 작업)
- 노출되어도 좋은 정보만 넣어야 함

- 용량이 커서 요청 시 데이터 양이 증가한다는 단점이 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/be466987-79ea-4988-a298-ee44c5493e1a)

<br/>

### **🍳 노드에서 JWT 사용하기**

JWT 모듈 설치

```js
npm i jsonwebtoken
```

- JWT 비밀키 .env에 저장

- JWT 토큰을 검사하는 `verifyToken 미들웨어` 작성

- `jwt.verify` 메서드로 검사 가능(두 번째 인수가 JWT 비밀키)

- JWT 토큰은 req.headers.authorization에 설정해줌(다른 곳으로도 가능)

- 만료된 JWT 토큰인 경우 에러 발생

- 유효하지 않은 토큰인 경우 401에러 발생

- req.decoded에 페이로드를 넣어 다음 미들웨어에서 쓸 수 있게 함

🔻nodebird-api/.env

```js
COOKIE_SECRET=nodebirdsecret
KAKAO_ID=5d4daf57becfd72fd9c919882552c4a6
JWT_SECRET=jwtSecret
```

🔻 middlewares/index.js

```js
exports.isLoggedIn = (req, res, next) => {
  if (req.isAuthenticated()) {
    next();
  } else {
    res.status(403).send('로그인 필요');
  }
};

exports.isNotLoggedIn = (req, res, next) => {
  if (!req.isAuthenticated()) {
    next();
  } else {
    const message = encodeURIComponent('로그인한 상태입니다.');
    res.redirect(`/?error=${message}`);
  }
};

exports.verifyToken = (req, res, next) => {
  try {
    res.locals.decoded = jwt.verify(req.headers.authorization, process.env.JWT_SECRET);
    return next();
  } catch (error) {
    if (error.name === 'TokenExpiredError') { // 유효기간 초과
      return res.status(419).json({
        code: 419, // 임의
        message: '토큰이 만료되었습니다',
      });
    }
    return res.status(401).json({
      code: 401,
      message: '유효하지 않은 토큰입니다',
    });
  }
};
```

<br/>

### **🍳 JWT 토큰 발급 라우터 만들기**

routes/v1.js 작성

- 버전 1이라는 뜻의 v1.js

- 한 번 버전이 정해진 후에는 라우터를 함부로 수정하면 안 됨

- 다른 사람이 기존 API를 쓰고 있기 때문(그 사람에게 영향이 감)

- 수정 사항이 생기면 버전을 올려야 함 

- POST /token에서 JWT 토큰 발급

- 먼저 도메인 검사 후 등록된 도메인이면 jwt.sign 메서드로 JWT 토큰 발급

- 첫 번째 인수로 `페이로드`를 넣고, 두 번째 인수는 `JWT 비밀키`, 세 번째 인수로 `토큰 옵션`

- (expiresIn은 만료 시간, issuer은 발급자)

- expiresIn은 1m(1분), 60 * 1000같은 밀리초 단위도 가능

- GET /test 라우터에서 토큰 인증 테스트 가능

🔻 routes/v1.js 

```js
const express = require('express');

const { verifyToken } = require('../middlewares');
const { createToken, tokenTest } = require('../controllers/v1');

const router = express.Router();

// POST /v1/token
router.post('/token', createToken); // req.body.clientSecret

// POST /v1/test
router.get('/test', verifyToken, tokenTest);

module.exports = router;
```

🔻 controllers.v1.js

```js
const jwt = require('jsonwebtoken');
const { Domain, User } = require('../models');

exports.createToken = async (req, res) => {
  const { clientSecret } = req.body;
  try {
    const domain = await Domain.findOne({
      where: { clientSecret },
      include: {
        model: User,
        attribute: ['nick', 'id'],
      },
    });
    if (!domain) {
      return res.status(401).json({
        code: 401,
        message: '등록되지 않은 도메인입니다. 먼저 도메인을 등록하세요',
      });
    }
    const token = jwt.sign({
      id: domain.User.id,
      nick: domain.User.nick,
    }, process.env.JWT_SECRET, {
      expiresIn: '1m', // 1분
      issuer: 'nodebird',
    });
    return res.json({
      code: 200,
      message: '토큰이 발급되었습니다',
      token,
    });
  } catch (error) {
    console.error(error);
    return res.status(500).json({
      code: 500,
      message: '서버 에러',
    });
  }
};

exports.tokenTest = (req, res) => {
  res.json(res.locals.decoded);
};
```

<br/>

### **🍳 app.js 라우터 연결**

v1.js > 도메인 검사 -> 토큰 발급 -> 발급 받은 토큰으로 API 데이터 요청

🔻 app.js

```js
const express = require('express');
const path = require('path');
const cookieParser = require('cookie-parser');
const passport = require('passport');
const morgan = require('morgan');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();

const v1 = require('./routes/v1');
const authRouter = require('./routes/auth');
const indexRouter = require('./routes');
const { sequelize } = require('./models');
const passportConfig = require('./passport');

const app = express();
passportConfig();
app.set('port', process.env.PORT || 8002);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});
sequelize.sync({ force: false })
  .then(() => {
    console.log('데이터베이스 연결 성공');
  })
  .catch((err) => {
    console.error(err);
  });

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
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
}));
app.use(passport.initialize());
app.use(passport.session());

app.use('/v1', v1);
app.use('/auth', authRouter);
app.use('/', indexRouter);

app.use((req, res, next) => {
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
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
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

#### **JWT 토큰으로 로그인하기**

세션 쿠키 발급 대신 JWT 토큰을 쿠키로 발급하면 됨

- Authenticate 메서드의 두 번째 인수로 옵션을 주면 세션 사용하지 않음

- passport에서는 authenticate 메서드의 두 번재 ㄷ인수로 `session : false` 옵션을 주면 세션을 사용하지 않을 수 있음

- 세션에 데이터를 저장하지 않으므로 serializeUser와 deserializeUser는 사용하지 않음 

```js
router.post('/login',isNotLoggedIn, (reqq,res,next)=> {
	passport.authenticate('local',{session:false}, (authError, user,info)=> {
    if (authError) {
...

```

#### **클라이언트에서 JWT를 사용하고 싶다면**

- process.env.JWT_SECRET은 클라이언트에서 노출되면 안 됨

- RSA같은 양방향 비대칭 암호화 알고리즘을 사용해야 함

- JWT는 PEM 키를 사용해서 양방향 암호화를 하는 것을 지원함
- 
<br/>

---

<br/>

## **💻 다른 서비스에서 호출하기**

### **🍳 API 호출용 서버 만들기**

호출용으로 사용할 새로운 폴더 만들고 package.json 파일을 만듦

```js
npm i axios cookie-parser dotenv express express-session morgan nunjucks
npm i -D nodemon
```

🔻package.json

```js
{
  "name": "nodecat",
  "version": "1.0.0",
  "description": "노드버드 2차 서비스",
  "main": "index.js",
  "scripts": {
    "start": "nodemon app",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "leekoby",
  "license": "ISC",
  "dependencies": {
    "axios": "^1.5.0",
    "cookie-parser": "^1.4.6",
    "dotenv": "^16.3.1",
    "express": "^4.18.2",
    "express-session": "^1.17.3",
    "morgan": "^1.10.0",
    "nunjucks": "^3.2.4"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

<br/>

### **🍳 폴더 구조 갖추기**

app.js 파일 생성

🔻 app.js

```js
const express = require('express');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();
const indexRouter = require('./routes');

const app = express();
app.set('port', process.env.PORT || 4000);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});

app.use(morgan('dev'));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
}));

app.use('/', indexRouter);

app.use((req, res, next) => {
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
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
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

views/error.html는 nodebird-api로부터 복사

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7a43d4ca-f999-4cd8-b617-d2c5ed7304bf)

아까 발급받은 비밀키를 .env에 입력

🔻 .env

```js
COOKIE_SECRET=nodecat
CLIENT_SECRET=7d67444e-fd01-4f9b-8680-f72464d02a57
//API 서버에서 발급 받은 키
```
<br/>

### **🍳 토큰 테스트용 라우터 만들기**

routes/index.js 생성

- GET /test에 접근 시 세션 검사

- 세션에 토큰이 저장되어 있지 않으면 `POST http://localhost:8002/v1/token` 라우터로부터 토큰 발급

- 이 때 HTTP 요청 본문에 클라이언트 비밀키 동봉

- 발급에 성공했다면 발급받은 토큰으로 다시 `GET https://localhost:8002/v1/test` 라우터 접근해서 토큰 테스트

- node-bird api로 부터 가져오는 외부 서비스

🔻 routes/index.js

```js
const express = require('express');
const { test } = require('../controllers');

const router = express.Router();

// POST /test
router.get('/test', test);

module.exports = router;
```

🔻 controllers/index.js

```js
const axios = require('axios');

exports.test = async (req, res, next) => { // 토큰 테스트 라우터
  try {
    if (!req.session.jwt) { // 세션에 토큰이 없으면 토큰 발급 시도
      const tokenResult = await axios.post('http://localhost:8002/v1/token', {
        clientSecret: process.env.CLIENT_SECRET,
      });
      if (tokenResult.data?.code === 200) { // 토큰 발급 성공
        req.session.jwt = tokenResult.data.token; // 세션에 토큰 저장
      } else { // 토큰 발급 실패
        return res.json(tokenResult.data); // 발급 실패 사유 응답
      }
    }
    // 발급받은 토큰 테스트
    const result = await axios.get('http://localhost:8002/v1/test', {
      headers: { authorization: req.session.jwt },
    });
    return res.json(result.data);
  } catch (error) {
    console.error(error);
    if (error.response?.status === 419) { // 토큰 만료 시
      return res.json(error.response.data);
    }
    return next(error);
  }
};
```

<br/>

### **🍳 실제 요청 보내기**

npm start로 서버 시작
[http://localhost:4000/test](http://localhost:4000/test){:target="_blank"}로 접속

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/d2eeffb4-33a2-44ed-9fe6-31d76b9f4e6a)

1분을 기다린 후 다시 접속하면 토큰이 만료되었다는 메시지 뜸

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/83ec1650-49bb-471c-86dc-0ec2c0f54c6c)


<br/>

---

<br/>

## **💻 SNS API 서버 만들기**

### **🍳 NodeBird 데이터 제공하기**

nodebird-api의 라우터 작성

`GET /posts/my`, `GET /posts/hashtag/:title`

🔻 nodebird-api/routes/v1.js

```js
const express = require('express');

const { verifyToken } = require('../middlewares');
const { createToken, tokenTest, getMyPosts, getPostsByHashtag } = require('../controllers/v1');

const router = express.Router();

// POST /v1/token
router.post('/token', createToken);

// POST /v1/test
router.get('/test', verifyToken, tokenTest);

// GET /v1/posts/my
router.get('/posts/my', verifyToken, getMyPosts);

// GET /v1/posts/hashtag/:title
router.get('/posts/hashtag/:title', verifyToken, getPostsByHashtag);

module.exports = router;
```

🔻 nodebird-api/controllers/v1.js

```js
const jwt = require('jsonwebtoken');
const { Domain, User, Post, Hashtag } = require('../models');

exports.createToken = async (req, res) => {
  const { clientSecret } = req.body;
  try {
    const domain = await Domain.findOne({
      where: { clientSecret },
      include: {
        model: User,
        attribute: ['nick', 'id'],
      },
    });
    if (!domain) {
      return res.status(401).json({
        code: 401,
        message: '등록되지 않은 도메인입니다. 먼저 도메인을 등록하세요',
      });
    }
    const token = jwt.sign({
      id: domain.User.id,
      nick: domain.User.nick,
    }, process.env.JWT_SECRET, {
      expiresIn: '1m', // 1분
      issuer: 'nodebird',
    });
    return res.json({
      code: 200,
      message: '토큰이 발급되었습니다',
      token,
    });
  } catch (error) {
    console.error(error);
    return res.status(500).json({
      code: 500,
      message: '서버 에러',
    });
  }
};

exports.tokenTest = (req, res) => {
  res.json(res.locals.decoded);
};

exports.getMyPosts = (req, res) => {
  Post.findAll({ where: { userId: res.locals.decoded.id } })
    .then((posts) => {
      console.log(posts);
      res.json({
        code: 200,
        payload: posts,
      });
    })
    .catch((error) => {
      console.error(error);
      return res.status(500).json({
        code: 500,
        message: '서버 에러',
      });
    });
};

exports.getPostsByHashtag = async (req, res) => {
  try {
    const hashtag = await Hashtag.findOne({ where: { title: req.params.title } });
    if (!hashtag) {
      return res.status(404).json({
        code: 404,
        message: '검색 결과가 없습니다',
      });
    }
    const posts = await hashtag.getPosts();
    return res.json({
      code: 200,
      payload: posts,
    });
  } catch (error) {
    console.error(error);
    return res.status(500).json({
      code: 500,
      message: '서버 에러',
    });
  }
};
```
<br/>

### **🍳 NodeBird 데이터 가져오기**

nodebird-call의 라우터 작성

- 토큰을 발급받고 요청을 보내는 부분을 request 함수로 만들어 둠

- 요청은 axios로 보내고 세션 토큰 검사, 재발급까지 같이 수행

🔻 routes/index.js

```js
const express = require('express');
const { searchByHashtag, getMyPosts } = require('../controllers');

const router = express.Router();

router.get('/myposts', getMyPosts);

router.get('/search/:hashtag', searchByHashtag);

module.exports = router;
```

🔻 controllers/index.js

```js
const axios = require('axios');

const URL = process.env.API_URL;
axios.defaults.headers.origin = process.env.ORIGIN; // origin 헤더 추가

const request = async (req, api) => {
  try {
    if (!req.session.jwt) { // 세션에 토큰이 없으면
      const tokenResult = await axios.post(`${URL}/token`, {
        clientSecret: process.env.CLIENT_SECRET,
      });
      req.session.jwt = tokenResult.data.token; // 세션에 토큰 저장
    }
    return await axios.get(`${URL}${api}`, {
      headers: { authorization: req.session.jwt },
    }); // API 요청
  } catch (error) {
    if (error.response?.status === 419) { // 토큰 만료시 토큰 재발급 받기
      delete req.session.jwt;
      return request(req, api);
    } // 419 외의 다른 에러면
    throw error;
  }
};

exports.getMyPosts = async (req, res, next) => {
  try {
    const result = await request(req, '/posts/my');
    res.json(result.data);
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.searchByHashtag = async (req, res, next) => {
  try {
    const result = await request(
      req, `/posts/hashtag/${encodeURIComponent(req.params.hashtag)}`,
    ); // encodeURIComponent를 안넣으면 한글 인식 못 함
    res.json(result.data);
  } catch (error) {
    if (error.code) {
      console.error(error);
      next(error);
    }
  }
};
```

<br/>

### **🍳 실제 요청 보내기**

`localhost:4000/mypost`에 접속하면 게시글 받아옴(NodeBird 서비스에 게시글이 있어야 함)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a414cdd0-671f-4206-ab29-233d5d45467a)


`localhost:4000/search/노드` 라우터에 접속하면 노드 해시태그 검색

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1927c3a5-7874-46ca-ab0a-1b8fc65a6241)

<br/>

---

<br/>

## **💻 사용량 제한 구현하기**

### **🍳 사용량 제한 구현하기**

DOS 공격 등을 대비해야 함

- 일정 시간동안 횟수 제한을 두어 무차별적인 요청을 막을 필요가 있음

```js
npm i express-rate-limit
```

- apiLimiter 미들웨어 추가
  
  - windowMS(기준 시간), max(허용 횟수), delayMS(호출 간격), handler(제한 초과 시 콜백 함수)
deprecated 미들웨어는 사용하면 안 되는 라우터에 붙여서 사용 시 경고

🔻nodebird-api/middlewares/index.js

```js
const jwt = require('jsonwebtoken');
const rateLimit = require('express-rate-limit');

exports.isLoggedIn = (req, res, next) => {
  if (req.isAuthenticated()) {
    next();
  } else {
    res.status(403).send('로그인 필요');
  }
};

exports.isNotLoggedIn = (req, res, next) => {
  if (!req.isAuthenticated()) {
    next();
  } else {
    const message = encodeURIComponent('로그인한 상태입니다.');
    res.redirect(`/?error=${message}`);
  }
};

exports.verifyToken = (req, res, next) => {
  try {
    res.locals.decoded = jwt.verify(req.headers.authorization, process.env.JWT_SECRET);
    return next();
  } catch (error) {
    if (error.name === 'TokenExpiredError') { // 유효기간 초과
      return res.status(419).json({
        code: 419,
        message: '토큰이 만료되었습니다',
      });
    }
    return res.status(401).json({
      code: 401,
      message: '유효하지 않은 토큰입니다',
    });
  }
};

exports.apiLimiter = rateLimit({
  windowMs: 60 * 1000, // 1분
  max: 10,
  handler(req, res) {
    res.status(this.statusCode).json({
      code: this.statusCode, // 기본값 429
      message: '1분에 열 번만 요청할 수 있습니다.',
    });
  },
});

exports.deprecated = (req, res) => {
  res.status(410).json({
    code: 410,
    message: '새로운 버전이 나왔습니다. 새로운 버전을 사용하세요.',
  });
};
```

<br/>

### **🍳 응답 코드 정리**

응답 코드를 정리해서 어떤 에러가 발생했는지 알려주기

일관성이 있으면 됨

![응답 코드 정리](https://github.com/leekoby/leekoby.github.io/assets/118284808/e8ca2a60-d846-430c-b165-307b22b738a9)


<br/>

### **🍳 새 라우터 버전 내놓기**

사용량 제한 기능이 추가되어 기존 API와 호환되지 않음

이런 경우 새로운 버전의 라우터를 내놓으면 됨

v2 라우터 작성(apiLimiter 추가됨)

v1 라우터는 deprecated 처리(router.use로 한 번에 모든 라우터에 적용)

변경된 버전을 반영하는 것에 주의

🔻nodebird-api/routes/v1.js

```js
const express = require('express');

const { verifyToken, deprecated } = require('../middlewares');
const { createToken, tokenTest, getMyPosts, getPostsByHashtag } = require('../controllers/v1');

const router = express.Router();

router.use(deprecated);  // <- 주목

// POST /v1/token
router.post('/token', createToken);

// POST /v1/test
router.get('/test', verifyToken, tokenTest);

// GET /v1/posts/my
router.get('/posts/my', verifyToken, getMyPosts);

// GET /v1/posts/hashtag/:title
router.get('/posts/hashtag/:title', verifyToken, getPostsByHashtag);

module.exports = router;
```

🔻nodebird-api/routes/v2.js

```js
const express = require('express');

const { verifyToken, apiLimiter } = require('../middlewares');
const { createToken, tokenTest, getMyPosts, getPostsByHashtag } = require('../controllers/v2');

const router = express.Router();

// POST /v2/token
router.post('/token', apiLimiter, createToken);

// POST /v2/test
router.get('/test', apiLimiter, verifyToken, tokenTest);

// GET /v2/posts/my
router.get('/posts/my', apiLimiter, verifyToken, getMyPosts);

// GET /v2/posts/hashtag/:title
router.get('/posts/hashtag/:title', apiLimiter, verifyToken, getPostsByHashtag);

module.exports = router;
```

🔻nodebird-api/controllers/v2.js

```js
const jwt = require('jsonwebtoken');
const { Domain, User, Post, Hashtag } = require('../models');

exports.createToken = async (req, res) => {
  const { clientSecret } = req.body;
  try {
    const domain = await Domain.findOne({
      where: { clientSecret },
      include: {
        model: User,
        attribute: ['nick', 'id'],
      },
    });
    if (!domain) {
      return res.status(401).json({
        code: 401,
        message: '등록되지 않은 도메인입니다. 먼저 도메인을 등록하세요',
      });
    }
    const token = jwt.sign({
      id: domain.User.id,
      nick: domain.User.nick,
    }, process.env.JWT_SECRET, {
      expiresIn: '30m', // 30분
      issuer: 'nodebird',
    });
    return res.json({
      code: 200,
      message: '토큰이 발급되었습니다',
      token,
    });
  } catch (error) {
    console.error(error);
    return res.status(500).json({
      code: 500,
      message: '서버 에러',
    });
  }
};

exports.tokenTest = (req, res) => {
  res.json(res.locals.decoded);
};

exports.getMyPosts = (req, res) => {
  Post.findAll({ where: { userId: res.locals.decoded.id } })
    .then((posts) => {
      console.log(posts);
      res.json({
        code: 200,
        payload: posts,
      });
    })
    .catch((error) => {
      console.error(error);
      return res.status(500).json({
        code: 500,
        message: '서버 에러',
      });
    });
};

exports.getPostsByHashtag = async (req, res) => {
  try {
    const hashtag = await Hashtag.findOne({ where: { title: req.params.title } });
    if (!hashtag) {
      return res.status(404).json({
        code: 404,
        message: '검색 결과가 없습니다',
      });
    }
    const posts = await hashtag.getPosts();
    return res.json({
      code: 200,
      payload: posts,
    });
  } catch (error) {
    console.error(error);
    return res.status(500).json({
      code: 500,
      message: '서버 에러',
    });
  }
};
```

🔻nodebird-api/app.js

```js
const express = require('express');
const path = require('path');
const cookieParser = require('cookie-parser');
const passport = require('passport');
const morgan = require('morgan');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();
const v1 = require('./routes/v1');
const v2 = require('./routes/v2');
const authRouter = require('./routes/auth');
const indexRouter = require('./routes');
const { sequelize } = require('./models');
const passportConfig = require('./passport');

const app = express();
passportConfig();
app.set('port', process.env.PORT || 8002);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});
sequelize.sync({ force: false })
  .then(() => {
    console.log('데이터베이스 연결 성공');
  })
  .catch((err) => {
    console.error(err);
  });

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
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
}));
app.use(passport.initialize());
app.use(passport.session());

app.use('/v1', v1);
app.use('/v2', v2);
app.use('/auth', authRouter);
app.use('/', indexRouter);

app.use((req, res, next) => {
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
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
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

### **🍳 새 라우터 실행해보기**

nodecat(사용자 입장)의 버전 v2로 바꾸기

🔻 .env

```js
API_URL=http://localhost:8002/v2
```

v1 API를 사용하거나 사용량을 초과하면 에러 발생

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4d062190-b638-40ab-b1db-148d23f03a34)

<br/>

> 이 예제에는 사용량을 초과하는 것을 보여주고자 1분에 한 번으로 사용량을 제한했다. 
>
> 실제 시버스에서는 서비스 정책에 맞게 제한량을 조절할 수 있다.
> 
> `express-rate-limit`은 데이터베이스와 연결하는 것을 지원하지 않으므로 새로운 패키지를 찾거나 직접 구현해야함
>```js
...
exports.apliLimiter= rateLimit({
  windowMs: 60 * 1000, // 1분
  max: 10,
  handler(req,res){
    res.status(this.statusCode).json({
      code: this.statusCode, // 기본값 429
      message: "1분에 열 번만 요청할 수 있습니다."
    })
  }
})
...
> ```
{:.prompt-warning }

<br/>

---

<br/>

## **💻 CORS 이해하기**

### **🍳 CORS  확인하기**

nodecat 프런트 작성하기

프런트에서 서버의 API를 호출하면 어떻게 될까?

routes/index.js, controllers/index.js, views/main.html 작성

🔻 nodecat/routes/index.js

```js
const express = require('express');
const { searchByHashtag, getMyPosts, renderMain } = require('../controllers');

const router = express.Router();

router.get('/myposts', getMyPosts);

router.get('/search/:hashtag', searchByHashtag);

router.get('/', renderMain);

module.exports = router;
```

🔻 nodecat/controllers/index.js

```js
const axios = require('axios');

const URL = process.env.API_URL;
axios.defaults.headers.origin = process.env.ORIGIN; // origin 헤더 추가

const request = async (req, api) => {
  try {
    if (!req.session.jwt) { // 세션에 토큰이 없으면
      const tokenResult = await axios.post(`${URL}/token`, {
        clientSecret: process.env.CLIENT_SECRET,
      });
      req.session.jwt = tokenResult.data.token; // 세션에 토큰 저장
    }
    return await axios.get(`${URL}${api}`, {
      headers: { authorization: req.session.jwt },
    }); // API 요청
  } catch (error) {
    if (error.response?.status === 419) { // 토큰 만료시 토큰 재발급 받기
      delete req.session.jwt;
      return request(req, api);
    } // 419 외의 다른 에러면
    throw error;
  }
};

exports.getMyPosts = async (req, res, next) => {
  try {
    const result = await request(req, '/posts/my');
    res.json(result.data);
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.searchByHashtag = async (req, res, next) => {
  try {
    const result = await request(
      req, `/posts/hashtag/${encodeURIComponent(req.params.hashtag)}`,
    );
    res.json(result.data);
  } catch (error) {
    if (error.code) {
      console.error(error);
      next(error);
    }
  }
};

exports.renderMain = (req, res) => {
  res.render('main', { key: process.env.CLIENT_SECRET });
};
```

🔻 nodecat/views/main.html

```html
<!DOCTYPE html>
<html>
  <head>
    <title>프론트 API 요청</title>
  </head>
  <body>
  <div id="result"></div>
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  <script>
    axios.post('http://localhost:8002/v2/token', {
      clientSecret: '{key}', // 이 부분 이중 중괄호로 감싸야해요.
    })
      .then((res) => {
        document.querySelector('#result').textContent = JSON.stringify(res.data);
      })
      .catch((err) => {
        console.error(err);
      });
  </script>
  </body>
</html>
```

<br/>


### **🍳 프런트에서 요청 보내기**

localhost:4000에 접속하면 에러 발생

![CORS 에러](https://github.com/leekoby/leekoby.github.io/assets/118284808/e79158d4-ff33-4622-aa7d-505e199d56fb)

요청을 보내는 프런트(localhost:4000), 요청을 받는 서버(localhost:8002)가 다르면 에러 발생(서버에서 서버로 요청을 보낼때는 발생하지 않음)

`CORS`: Cross-Origin Resource Sharing 문제

POST 대신 OPTIONS 요청을 먼저 보내 서버가 도메인을 허용하는지 미리 체크

CORS는 브라우저에서 발생하는 에러 but 해결은 서버에서

<br/>

### **🍳 CORS 문제 해결 방법**

Access-Control-Allow-Origin 응답 헤더를 넣어주어야 CORS 문제 해결 가능

res.set 메서드로 직접 넣어주어도 되지만 패키지를 사용하는게 편리

```js
npm i cors
```
v2 라우터에 적용

`credentials`: true를 해야 프런트와 백엔드 간에 쿠키가 공유됨

🔻 nodebird-api/routes/v2.js

```js
const express = require('express');

const { verifyToken, apiLimiter, corsWhenDomainMatches } = require('../middlewares');
const { createToken, tokenTest, getMyPosts, getPostsByHashtag } = require('../controllers/v1');
const router = express.Router();

router.use(corsWhenDomainMatches); 
...
```

🔻 nodebird-api/middlewares/index.js

```js
const jwt = require('jsonwebtoken');
const rateLimit = require('express-rate-limit');
const cors = require('cors');
const { Domain } = require('../models');

exports.isLoggedIn = (req, res, next) => {
  if (req.isAuthenticated()) {
    next();
  } else {
    res.status(403).send('로그인 필요');
  }
};

exports.isNotLoggedIn = (req, res, next) => {
  if (!req.isAuthenticated()) {
    next();
  } else {
    const message = encodeURIComponent('로그인한 상태입니다.');
    res.redirect(`/?error=${message}`);
  }
};

exports.verifyToken = (req, res, next) => {
  try {
    res.locals.decoded = jwt.verify(req.headers.authorization, process.env.JWT_SECRET);
    return next();
  } catch (error) {
    if (error.name === 'TokenExpiredError') { // 유효기간 초과
      return res.status(419).json({
        code: 419,
        message: '토큰이 만료되었습니다',
      });
    }
    return res.status(401).json({
      code: 401,
      message: '유효하지 않은 토큰입니다',
    });
  }
};

exports.apiLimiter = rateLimit({
  windowMs: 60 * 1000, // 1분
  max: 10,
  handler(req, res) {
    res.status(this.statusCode).json({
      code: this.statusCode, // 기본값 429
      message: '1분에 열 번만 요청할 수 있습니다.',
    });
  },
});

exports.deprecated = (req, res) => {
  res.status(410).json({
    code: 410,
    message: '새로운 버전이 나왔습니다. 새로운 버전을 사용하세요.',
  });
};

exports.corsWhenDomainMatches = async (req, res, next) => { 
  //이부분 추가 미들웨어 확정패턴
  const domain = await Domain.findOne({
    where: { host: new URL(req.get('origin')).host },
  });
  if (domain) {
    cors({ 
      origin: req.get('origin'),
      credentials: true,
    })(req, res, next);
  } else {
    next();
  }
};
```

<br/>

### **🍳 CORS 적용 확인하기**

[http://localhost:4000](http://localhost:4000){:target="_blank"}에 접속하면 정상적으로 토큰이 발급됨

![CORS 적용 확인하기](https://github.com/leekoby/leekoby.github.io/assets/118284808/5bd689f5-30cb-4687-a3a7-278997c123f9)

응답 헤더를 보면 Access-Control-Allow-Origin 헤더가 들어 있음

`*`은 모든 도메인을 허용함을 의미

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/387b795c-ab88-4af9-a1ab-a926944687fe)

<br/>

### **🍳 클라이언트 도메인 검사하기**

클라이언트 환경에서는 비밀키가 노출됨

- 도메인까지 같이 검사해야 요청 인증 가능

- 호스트와 비밀키가 모두 일치할 때만 CORS를 허용

- 클라이언트의 도메인(req.get(‘origin’))과 등록된 호스트가 일치하는 지 찾음

- mew URL().host는 http같은 프로토콜을 떼어내기 위함

- cors의 인자로 origin을 주면 `*` 대신 주어진 도메인만 허용할 수 있음

🔻 nodebird-api/middlewares/index.js

```js
...
exports.corsWhenDomainMatches = async (req, res, next) => { 
  //이부분 추가 미들웨어 확정패턴
  const domain = await Domain.findOne({
    where: { host: new URL(req.get('origin')).host },
  });
  if (domain) {
    cors({ 
      origin: req.get('origin'),
      credentials: true,
    })(req, res, next);
  } else {
    next();
  }
};
...
```

<br/>

### **🍳 유용한 미들웨어 패턴 알아보기**

위의 미들웨어를 아래처럼 수정 가능

- 아래처럼 쓰면 미들웨어 위 아래로 임의의 코드를 추가할 수 있음

- 활용 가능

```js
router.use(cors());

//또는

router.use((req, res, next)=>{
  cors()(req, res, next);
})
```

<br/>

### **🍳 CORS 요청 보내기**

localhost:4000에 접속

응답 헤더의 도메인 확인

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/52ed0bdf-0205-4ac2-9895-91f96dc6eb1b)


<br/>

### **🍳 프록시 서버**

CORS 문제에 대한 또다른 해결책

- 서버-서버 간의 요청/응답에는 CORS 문제가 발생하지 않는 것을 활용

- 직접 구현해도 되지만 http-proxy-middleware같은 패키지로 손쉽게 연동 가능

![프록시 서버의 원리](https://github.com/leekoby/leekoby.github.io/assets/118284808/d72b9b28-f4bd-4ee4-8427-f569dbb58fd3)

<br/>

---

<br/>


## **💻 함께 보면 좋은 자료**

> [API 설명](https://ko.wikipedia.org/wiki/API){:target="_blank"}
{:.prompt-info}

> [JWT 토큰 설명](https://jwt.io/){:target="_blank"}
{:.prompt-info}

> [JSON WEB TOKEN 공식문서](https://www.npmjs.com/package/jsonwebtoken){:target="_blank"}
{:.prompt-info}

> [AXIOS](https://axios-http.com/kr/docs/intro){:target="_blank"}
{:.prompt-info}

> [CORS](https://www.npmjs.com/package/cors){:target="_blank"}
{:.prompt-info}

> [express-rate-limit](https://www.npmjs.com/package/express-rate-limit){:target="_blank"}
{:.prompt-info}

> [UUID](https://www.npmjs.com/package/uuid){:target="_blank"}
{:.prompt-info}

> [ms 공식문서](https://github.com/vercel/ms){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}