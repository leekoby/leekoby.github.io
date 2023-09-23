---
layout: post
title: 13. 실시간 경매 시스템 만들기
author: admin
date: 2023-05-02 00:00:00 +900
lastmod: 2023-05-02 00:00:00 +900
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

# **🌈 실시간 경매 시스템 만들기**

## **💻 프로젝트 구조 갖추기**

### **🍳 NodeAuction 프로젝트**

node-auction 폴더를 만든 후 그 안에 package.json 작성

- npm i로 필요한 패키지 설치

- 데이터베이스는 MySQL

- 시퀄라이즈 설치 및 기본 디렉터리 만듦

```sh
npm i sequelize sequelize-cli mysql2 bcrypt cookie-parser dotenv express express-session morgan multer nunjucks passport passport-local

npm i -D nodemon

npx sequelize init
```

🔻package.json

```js
{
  "name": "node-auction",
  "version": "0.0.1",
  "description": "노드 경매 시스템",
  "main": "app.js",
  "scripts": {
    "start": "nodemon app"
  },
  "author": "leekoby",
  "license": "ISC",
  "dependencies": {
    "bcrypt": "^5.1.1",
    "cookie-parser": "^1.4.6",
    "dotenv": "^16.3.1",
    "express": "^4.18.2",
    "express-session": "^1.17.3",
    "morgan": "^1.10.0",
    "multer": "^1.4.5-lts.1",
    "mysql2": "^3.6.1",
    "nunjucks": "^3.2.4",
    "passport": "^0.6.0",
    "passport-local": "^1.0.0",
    "sequelize": "^6.33.0",
    "sequelize-cli": "^6.6.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}

```

<br/>

### **🍳 모델 작성하기**

models/user.js, models/good.js, models/auctions.js 작성

- user.js: 사용자 이메일, 닉네임, 비밀번호와 자금(money)

- good.js: 상품의 이름과 사진, 시작 가격

- auction.js: 입찰가(bid)와 msg(입찰 시 전달할 메시지)

- config/config.json에 MySQL 데이터베이스 설정 작성

🔻 models/user.js

```js
const Sequelize = require('sequelize');

class User extends Sequelize.Model {
  static initiate(sequelize) {
    User.init({
      email: {
        type: Sequelize.STRING(40),
        allowNull: false,
        unique: true,
      },
      nick: {
        type: Sequelize.STRING(15),
        allowNull: false,
      },
      password: {
        type: Sequelize.STRING(100),
        allowNull: true,
      },
      money: {
        type: Sequelize.INTEGER,
        allowNull: false,
        defaultValue: 0,
      },
    }, {
      sequelize,
      timestamps: true,
      paranoid: true,
      modelName: 'User',
      tableName: 'users',
      charset: 'utf8',
      collate: 'utf8_general_ci',
    });
  }

  static associate(db) {
    db.User.hasMany(db.Auction);
  }
};

module.exports = User;
```

🔻 models/auction.js

```js
const Sequelize = require('sequelize');

class Auction extends Sequelize.Model {
  static initiate(sequelize) {
    Auction.init({
      bid: {
        type: Sequelize.INTEGER,
        allowNull: false,
        defaultValue: 0,
      },
      msg: {
        type: Sequelize.STRING(100),
        allowNull: true,
      },
    }, {
      sequelize,
      timestamps: true,
      paranoid: true,
      modelName: 'Auction',
      tableName: 'auctions',
      charset: 'utf8',
      collate: 'utf8_general_ci',
    });
  }

  static associate(db) {
    db.Auction.belongsTo(db.User);
    db.Auction.belongsTo(db.Good);
  }
};

module.exports = Auction;
```


🔻 models/good.js

```js
const Sequelize = require('sequelize');

class Good extends Sequelize.Model {
  static initiate(sequelize) {
    Good.init({
      name: {
        type: Sequelize.STRING(40),
        allowNull: false,
      },
      img: {
        type: Sequelize.STRING(200),
        allowNull: true,
      },
      price: {
        type: Sequelize.INTEGER,
        allowNull: false,
        defaultValue: 0,
      },
    }, {
      sequelize,
      timestamps: true,
      paranoid: true,
      modelName: 'Good',
      tableName: 'goods',
      charset: 'utf8',
      collate: 'utf8_general_ci',
    });
  }

  static associate(db) {
    db.Good.belongsTo(db.User, { as: 'Owner' });
    db.Good.belongsTo(db.User, { as: 'Sold' });
    db.Good.hasMany(db.Auction);
  }
};

module.exports = Good;
```

<br/>

### **🍳 데이터 베이스 생성하기**

🔻config/config.json

```js
{
  "development": {
    "username": "root",
    "password": "비밀번호",
    "database": "nodeauction",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
```

```sh
$ npx sequelize db:create
```

<br/>

### **🍳 DB 관계 설정하기**

🔻 models/index.js

```js
const Sequelize = require('sequelize');
const env = process.env.NODE_ENV || 'development';
const fs = require('fs');
const path = require('path');
const config = require('../config/config')[env];

const db = {};
const sequelize = new Sequelize(
  config.database, config.username, config.password, config,
);

db.sequelize = sequelize;

const basename = path.basename(__filename);
fs
  .readdirSync(__dirname) // 현재 폴더의 모든 파일을 조회
  .filter(file => { // 숨김 파일, index.js, js 확장자가 아닌 파일 필터링
    return (file.indexOf('.') !== 0) && (file !== basename) && (file.slice(-3) === '.js');
  })
  .forEach(file => { // 해당 파일의 모델 불러와서 init
    const model = require(path.join(__dirname, file));
    console.log(file, model.name);
    db[model.name] = model;
    model.initiate(sequelize);
  });

Object.keys(db).forEach(modelName => { // associate 호출
  if (db[modelName].associate) {
    db[modelName].associate(db);
  }
});

module.exports = db;
```

<br/>

### **🍳 passport 세팅하기**

```sh
npm i passport passport-local bcrypt
```

- passport/localStrategy.js, passport/index.js 작성(9장과 거의 동일)

- 카카오 로그인은 하지 않음

- 로그인을 위한 컨트롤러, 라우터, 미들웨어 추가 

🔻 passport/localStrategy.js

```js
const passport = require('passport');
const LocalStrategy = require('passport-local').Strategy;
const bcrypt = require('bcrypt');

const User = require('../models/user');

module.exports = () => {
  passport.use(new LocalStrategy({
    usernameField: 'email',
    passwordField: 'password',
  }, async (email, password, done) => {
    try {
      const exUser = await User.findOne({ where: { email } });
      if (exUser) {
        const result = await bcrypt.compare(password, exUser.password);
        if (result) {
          done(null, exUser);
        } else {
          done(null, false, { message: '비밀번호가 일치하지 않습니다.' });
        }
      } else {
        done(null, false, { message: '가입되지 않은 회원입니다.' });
      }
    } catch (error) {
      console.error(error);
      done(error);
    }
  }));
};
```

🔻 passport/index.js

```js
const passport = require('passport');

const local = require('./localStrategy');
const User = require('../models/user');

module.exports = () => {
  passport.serializeUser((user, done) => {
    done(null, user.id);
  });

  passport.deserializeUser((id, done) => {
    User.findOne({ where: { id } })
      .then(user => done(null, user))
      .catch(err => done(err));
  });

  local();
};
```

🔻 controllers/auth.js

```js
const bcrypt = require('bcrypt');
const passport = require('passport');
const User = require('../models/user');

exports.join = async (req, res, next) => {
  const { email, nick, password, money } = req.body;
  try {
    const exUser = await User.findOne({ where: { email } });
    if (exUser) {
      return res.redirect('/join?error=이미 가입된 이메일입니다.');
    }
    const hash = await bcrypt.hash(password, 12);
    await User.create({
      email,
      nick,
      password: hash,
      money,
    });
    return res.redirect('/');
  } catch (error) {
    console.error(error);
    return next(error);
  }
}

exports.login = (req, res, next) => {
  passport.authenticate('local', (authError, user, info) => {
    if (authError) {
      console.error(authError);
      return next(authError);
    }
    if (!user) {
      return res.redirect(`/?error=${info.message}`);
    }
    return req.login(user, (loginError) => {
      if (loginError) {
        console.error(loginError);
        return next(loginError);
      }
      return res.redirect('/');
    });
  })(req, res, next); // 미들웨어 내의 미들웨어에는 (req, res, next)를 붙입니다.
};

exports.logout = (req, res) => {
  req.logout(() => {
    res.redirect('/');
  });
};
```
🔻 routes/auth.js

```js
const express = require('express');

const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const { join, login, logout } = require('../controllers/auth');

const router = express.Router();

// POST /auth/join
router.post('/join', isNotLoggedIn, join); 

// POST /auth/login
router.post('/login', isNotLoggedIn, login);

// GET /auth/logout
router.get('/logout', isLoggedIn, logout);

module.exports = router;
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

```

🔻 .env

```js
COOKIE_SECRET=auction
```

🔻 app.js

```js
const express = require('express');
const path = require('path');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const passport = require('passport');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();
const indexRouter = require('./routes/index');
const authRouter = require('./routes/auth');
const { sequelize } = require('./models');
const passportConfig = require('./passport');

const app = express();
passportConfig();
app.set('port', process.env.PORT || 8010);
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

const sessionMiddleware = session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use('/img', express.static(path.join(__dirname, 'uploads')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(sessionMiddleware);
app.use(passport.initialize());
app.use(passport.session());

app.use('/', indexRouter);
app.use('/auth', authRouter);

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
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

### **🍳 views 파일 작성하기**

경매시스템은 회원 가입, 로그인, 경매 상품 등록, 방 참여, 경매 진행으로 이루어져있다. 

views 폴더에 layout.html, main.html, join.html, good.html public/main.css작성

[소스 코드 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch13/13.1/node-auction){:target="_blank"}

- layout.html: 전체 화면의 레이아웃(로그인 폼)
- main.html : 메인 화면을 담당(경매 목록이 있음)
- join.html: 회원가입 폼
- good.html: 상품을 업로드하는 화면(이미지 업로드 폼)
- public/main.css도 추가

<br/>

### **🍳 routes/index.js**

routes/index.js 작성

- GET /는 메인 페이지(경매 리스트) 렌더링

- GET /join은 회원가입 페이지

- GET /good은 상품 등록 페이지

- POST /good 상품 등록 라우터

🔻 routes/index.js

```js
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');

const { isLoggedIn, isNotLoggedIn } = require('../middleware');
const { renderMain, renderJoin, renderGood, createGood } = require('../controllers');

const router = express.Router();

router.use((req, res, next) => {
  res.lacals.user = req.user;
  next();
});

router.get('/', renderMain);
router.get('/join', isNotLoggedIn, renderJoin);
router.get('/good', isLoggedIn, renderGood);

try {
  fs.readFileSync('uploads');
} catch (error) {
  console.error('uploads 폴더가 없어 uploads 폴더를 생성합니다.');
  fs.mkdirSync('uploads');
}

const upload = multer({
  storage: multer.diskStorage({
    destination(req, file, cb) {
      cb(null, 'uploads/');
    },
    filename(req, file, cb) {
      const ext = path.extname(file.originalname);
      cb(null, path.basename(file.originalname, ext) + new Date().valueOf() + ext);
    },
  }),
  limits: { fileSize: 5 * 1024 * 1024 },
});
router.post('/good', isLoggedIn, upload.single('img'), createGood);

module.exports = router;
```

🔻 controllers/index.js

```js
const { Op } = require('sequelize');
const { Good } = require('../models');

exports.renderMain = async (req, res, next) => {
  try {
    const yesterday = new Date();
    yesterday.setDate(yesterday.getDate() - 1); // 어제 시간
    const goods = await Good.findAll({
      where: { SoldId: null, createdAt: { [Op.gte]: yesterday } },
    });
    res.render('main', {
      title: 'NodeAuction',
      goods,
    });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderJoin = (req, res) => {
  res.render('join', {
    title: '회원가입 - NodeAuction',
  });
};

exports.renderGood = (req, res) => {
  res.render('good', { title: '상품 등록 - NodeAuction' });
};

exports.createGood = async (req, res, next) => {
  try {
    const { name, price } = req.body;
    await Good.create({
      OwnerId: req.user.id,
      name,
      img: req.file.filename,
      price,
    });
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

<br/>

### **🍳 서버 실행하기**

[localhost:8010](http://localhost:8010/){:target="_blank"}에 접속

회원가입 후 로그인하고 상품 등록해보기

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/26dab868-f744-4969-b956-66aa13b91950)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b4a5c1c1-60f9-4fb7-b374-ce55a0c1f625)


<br/>

---

<br/>

## **💻 서버센트 이벤트 사용하기**

### **🍳 서버센트 이벤트 사용**

경매는 시간이 중요하다.

- 모든 사람이 같은 시간에 경매가 종료되어야 함

- 모든 사람에게 같은 시간이 표시되어야 함

- 클라이언트 시간은 믿을 수 없음(조작 가능)

- 따라서 서버 시간을 주기적으로 클라이언트로 내려보내줌

- 이 때 서버에서 클라이언트로 단방향 통신을 하기 때문에 서버센트 이벤트(Server Sent Events, SSE)가 적합

- 웹 소켓은 실시간으로 입찰할 때 사용

```sh
npm i sse socket.io
```

<br/>

### **🍳 서버에 서버센트 이벤트 연결**

app.js에 SSE(sse.js 작성 후) 연결

- sse.on(‘connection’)은 서버와 연결되었을 때 호출되는 이벤트

- client.send로 클라이언트에 데이터 전송 가능(책에서는 서버 시각 전송)

🔻 app.js

```js
const express = require('express');
const path = require('path');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const passport = require('passport');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();
const indexRouter = require('./routes/index');
const authRouter = require('./routes/auth');
const { sequelize } = require('./models');
const passportConfig = require('./passport');
const sse = require('./sse');
const webSocket = require('./socket');

const app = express();
passportConfig();
app.set('port', process.env.PORT || 8010);
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

const sessionMiddleware = session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use('/img', express.static(path.join(__dirname, 'uploads')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(sessionMiddleware);
app.use(passport.initialize());
app.use(passport.session());

app.use('/', indexRouter);
app.use('/auth', authRouter);

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

const server = app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});

webSocket(server, app);
sse(server);
```

🔻 sse.js

```js
const SSE = require('sse');

module.exports = (server) => {
  const sse = new SSE(server);
  sse.on('connection', (client) => { // 서버센트이벤트 연결
    setInterval(() => {
      client.send(Date.now().toString());
    }, 1000);
  });
};
```

<br/>

### **🍳 웹 소켓 코드 작성하기**

socket.js 작성하기

- 경매 방이 있기 때문에 11장에서 방에 들어가는 코드 재사용

- referer에서 방 아이디를 추출해서 socket.join

🔻 socket.js

```js
const SocketIO = require('socket.io');

module.exports = (server, app) => {
  const io = SocketIO(server, { path: '/socket.io' });
  app.set('io', io);
  io.on('connection', (socket) => { // 웹 소켓 연결 시
    const req = socket.request;
    const { headers: { referer } } = req;
    const roomId = new URL(referer).pathname.split('/').at(-1);
    socket.join(roomId);
    socket.on('disconnect', () => {
      socket.leave(roomId);
    });
  });
};
```

<br/>

### **🍳 EventSource polyfill**

SSE는 EventSource라는 객체로 사용


- IE에서는 EventSource가 지원되지 않음

- EventSource polyfill을 넣어줌(첫 번째 스크립트)

- new EventSource(‘/sse’)로 서버와 연결

- es.onmessage로 서버에서 내려오는 데이터 받음(e.data에 들어있음)

- 아랫부분은 서버 시간과 경매 종료 시간을 계산해 카운트다운

```html
  <script src="https://unpkg.com/event-source-polyfill/src/eventsource.min.js"></script>
  <script>
   ...
  </script>
```

![경매시간카운트](https://github.com/leekoby/leekoby.github.io/assets/118284808/57c3fb5d-2eab-40e8-a57c-3b587252c012)

<br/>

### **🍳 EventSource 확인해보기**

개발자 도구 Network 탭을 확인

- GET /sse가 서버센트 이벤트 접속한 요청(type이 eventsource)

- GET /sse 클릭 후 EventStream 탭을 보면 매 초마다 서버로부터 타임스탬프 데이터가 오는 것을 확인 가능

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e93db969-ee52-4e3c-8b38-d2e0ffc6a3a0)

<br/>

### **🍳 클라이언트에 웹소켓, SSE 연결하기**

auction.html에 서버 시간과 실시간 입찰 기능 추가

[소스 코드 참고](https://github.com/ZeroCho/nodejs-book/blob/master/ch13/13.2/node-auction/views/auction.html){:target="_blank"}

- 서버 시간을 받아와서 카운트다운하는 부분은 이전과 동일

- 세 번째 스크립트 태그는 입찰 시 POST /good/:id/bid로 요청을 보내는 것

- 다른 사람이 입찰했을 때 Socket.IO로 입찰 정보를 렌더링함

<br/>

### **🍳 라우터왘 컨트롤러에 추가**

- `GET /good/:id`

  - 해당 상품과 기존 입찰 정보들을 불러온 뒤 렌더링

상품 모델에 사용자 모델을 include할 때 as 속성 사용함(owner과 sold 중 어떤 관계를 사용할지 밝혀주는 것)

- `POST /good/:id/bid`

  - 클라이언트로부터 받은 입찰 정보 저장
  - 시작 가격보다 낮게 입찰했거나, 경매 종료 시간이 지났거나, 이전 입찰가보다 낮은 입찰가가 들어왔다면 반려
  - 정상 입찰가가 들어 왔다면 저장 후 해당 경매방의 모든 사람에게 입찰자, 입찰 가격, 입찰 메시지 등을 웹 소켓으로 전달
  - Good.find 메서드의 order 속성은 include될 모델의 컬럼을 정렬하는 방법(Auction 모델의 bid를 내림차순으로 정렬)

🔻 routes/index.js

```js
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');


const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const {
  renderMain, renderJoin, renderGood, createGood, renderAuction, bid,
} = require('../controllers');

const router = express.Router();

router.use((req, res, next) => {
  res.locals.user = req.user;
  next();
});

router.get('/', renderMain);

router.get('/join', isNotLoggedIn, renderJoin);

router.get('/good', isLoggedIn, renderGood);

try {
  fs.readdirSync('uploads');
} catch (error) {
  console.error('uploads 폴더가 없어 uploads 폴더를 생성합니다.');
  fs.mkdirSync('uploads');
}
const upload = multer({
  storage: multer.diskStorage({
    destination(req, file, cb) {
      cb(null, 'uploads/');
    },
    filename(req, file, cb) {
      const ext = path.extname(file.originalname);
      cb(null, path.basename(file.originalname, ext) + new Date().valueOf() + ext);
    },
  }),
  limits: { fileSize: 5 * 1024 * 1024 },
});
router.post('/good', isLoggedIn, upload.single('img'), createGood);

router.get('/good/:id', isLoggedIn, renderAuction);

router.post('/good/:id/bid', isLoggedIn, bid);

module.exports = router;
```

🔻 controllers/index.js

```js
const { Op } = require('sequelize');
const { Good, Auction, User } = require('../models');

exports.renderMain = async (req, res, next) => {
  try {
    const yesterday = new Date();
    yesterday.setDate(yesterday.getDate() - 1); // 어제 시간
    const goods = await Good.findAll({ 
      where: { SoldId: null, createdAt: { [Op.gte]: yesterday } },
    });
    res.render('main', {
      title: 'NodeAuction',
      goods,
    });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderJoin = (req, res) => {
  res.render('join', {
    title: '회원가입 - NodeAuction',
  });
};

exports.renderGood = (req, res) => {
  res.render('good', { title: '상품 등록 - NodeAuction' });
};

exports.createGood = async (req, res, next) => {
  try {
    const { name, price } = req.body;
    await Good.create({
      OwnerId: req.user.id,
      name,
      img: req.file.filename,
      price,
    });
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderAuction = async (req, res, next) => {
  try {
    const [good, auction] = await Promise.all([
      Good.findOne({
        where: { id: req.params.id },
        include: {
          model: User,
          as: 'Owner',
        },
      }),
      Auction.findAll({
        where: { GoodId: req.params.id },
        include: { model: User },
        order: [['bid', 'ASC']],
      }),
    ]);
    res.render('auction', {
      title: `${good.name} - NodeAuction`,
      good,
      auction,
    });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.bid = async (req, res, next) => {
  try {
    const { bid, msg } = req.body;
    const good = await Good.findOne({
      where: { id: req.params.id },
      include: { model: Auction },
      order: [[{ model: Auction }, 'bid', 'DESC']],
    });
    if (!good) {
      return res.status(404).send('해당 상품은 존재하지 않습니다.');
    }
    if (good.price >= bid) {
      return res.status(403).send('시작 가격보다 높게 입찰해야 합니다.');
    }
    if (new Date(good.createdAt).valueOf() + (24 * 60 * 60 * 1000) < new Date()) {
      return res.status(403).send('경매가 이미 종료되었습니다');
    }
    if (good.Auctions[0]?.bid >= bid) {
      return res.status(403).send('이전 입찰가보다 높아야 합니다');
    }
    const result = await Auction.create({
      bid,
      msg,
      UserId: req.user.id,
      GoodId: req.params.id,
    });
    // 실시간으로 입찰 내역 전송
    req.app.get('io').to(req.params.id).emit('bid', {
      bid: result.bid,
      msg: result.msg,
      nick: req.user.nick,
    });
    return res.send('ok');
  } catch (error) {
    console.error(error);
    return next(error);
  }
};
```

<br/>

### **🍳 경매 진행해보기**

서버 연결 후 경매 시작

브라우저를 두 개 띄워 각자 다른 아이디로 로그인하면 두 개의 클라이언트가 동시 접속한 효과를 얻을 수 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1eed79a0-4b2d-49d9-b6da-b6e2b46b7aa3)

<br/>

---

<br/>

## **💻 스케쥴링 구현하기**

### **🍳 스케줄러 설치하기**

경매가 생성된 지 24시간 후에 낙찰자를 정함

- 24시간 후에 낙찰자를 정하는 시스템 구현해야 함

- node-schedule 모듈 사용

```sh
npm i node-schedule
```

<br/>

### **🍳스케줄링 추가하기**

controllers/index.js에 추가

- schedule 모듈을 불러옴

- scheduleJob 메서드로 일정 예약

- 첫 번째 인수로 실행될 시각을 넣고, 두 번째 인수로 콜백 함수를 넣음

- 가장 높은 입찰을 한 사람을 찾아 상품 모델의 낙찰자 아이디에 넣어줌

- 동시에, 낙찰자의 보유 자산을 낙찰 금액만큼 제외(sequelize.literal(컬럼 – 숫자)로 숫자 줄임)

- 단점: 노드 기반으로 스케쥴링이 되므로, 노드가 종료되면 스케줄 예약도 같이 종료됨

- 서버가 어떤 에러로 종료될 지 예측하기 어려우므로 보완하기 위한 방법이 필요함

🔻 controllers/index.js

```js
const { Op } = require('sequelize');
const { Good, Auction, User, sequelize } = require('../models');
const schedule = require('node-schedule');

exports.renderMain = async (req, res, next) => {
  try {
    const yesterday = new Date();
    yesterday.setDate(yesterday.getDate() - 1); // 어제 시간
    const goods = await Good.findAll({
      where: { SoldId: null, createdAt: { [Op.gte]: yesterday } },
    });
    res.render('main', {
      title: 'NodeAuction',
      goods,
    });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderJoin = (req, res) => {
  res.render('join', {
    title: '회원가입 - NodeAuction',
  });
};

exports.renderGood = (req, res) => {
  res.render('good', { title: '상품 등록 - NodeAuction' });
};

exports.createGood = async (req, res, next) => {
  try {
    const { name, price } = req.body;
    const good = await Good.create({
      OwnerId: req.user.id,
      name,
      img: req.file.filename,
      price,
    });
    const end = new Date();
    end.setDate(end.getDate() + 1); // 하루 뒤
    const job = schedule.scheduleJob(end, async () => {
      const success = await Auction.findOne({
        where: { GoodId: good.id },
        order: [['bid', 'DESC']],
      });
      await good.setSold(success.UserId);
      await User.update({
        money: sequelize.literal(`money - ${success.bid}`),
      }, {
        where: { id: success.UserId },
      });
    });
    job.on('error', (err) => {
      console.error('스케줄링 에러', err);
    });
    job.on('success', () => {
      console.log('스케줄링 성공');
    });
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderAuction = async (req, res, next) => {
  try {
    const [good, auction] = await Promise.all([
      Good.findOne({
        where: { id: req.params.id },
        include: {
          model: User,
          as: 'Owner',
        },
      }),
      Auction.findAll({
        where: { GoodId: req.params.id },
        include: { model: User },
        order: [['bid', 'ASC']],
      }),
    ]);
    res.render('auction', {
      title: `${good.name} - NodeAuction`,
      good,
      auction,
    });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.bid = async (req, res, next) => {
  try {
    const { bid, msg } = req.body;
    const good = await Good.findOne({
      where: { id: req.params.id },
      include: { model: Auction },
      order: [[{ model: Auction }, 'bid', 'DESC']],
    });
    if (!good) {
      return res.status(404).send('해당 상품은 존재하지 않습니다.');
    }
    if (good.price >= bid) {
      return res.status(403).send('시작 가격보다 높게 입찰해야 합니다.');
    }
    if (new Date(good.createdAt).valueOf() + (24 * 60 * 60 * 1000) < new Date()) {
      return res.status(403).send('경매가 이미 종료되었습니다');
    }
    if (good.Auctions[0]?.bid >= bid) {
      return res.status(403).send('이전 입찰가보다 높아야 합니다');
    }
    const result = await Auction.create({
      bid,
      msg,
      UserId: req.user.id,
      GoodId: req.params.id,
    });
    // 실시간으로 입찰 내역 전송
    req.app.get('io').to(req.params.id).emit('bid', {
      bid: result.bid,
      msg: result.msg,
      nick: req.user.nick,
    });
    return res.send('ok');
  } catch (error) {
    console.error(error);
    return next(error);
  }
};
```

<br/>

### **🍳 서버 시작 시 이전 경매 체크하기**

서버가 시작될 때 경매 후 24시간이 지났지만 낙찰자가 없는 경매를 찾아 낙찰자 지정

checkAuction.js 작성 후 app.js에 연결

🔻 checkAuction.js

```js
const { scheduleJob } = require('node-schedule');
const { Op } = require('sequelize');
const { Good, Auction, User, sequelize } = require('./models');

module.exports = async () => {
  console.log('checkAuction');
  try {
    const yesterday = new Date();
    yesterday.setDate(yesterday.getDate() - 1); // 어제 시간
    const targets = await Good.findAll({ // 24시간이 지난 낙찰자 없는 경매들
      where: {
        SoldId: null,
        createdAt: { [Op.lte]: yesterday },
      },
    });
    targets.forEach(async (good) => {
      const t = await sequelize.transaction();
      try {
        const success = await Auction.findOne({
          where: { GoodId: good.id },
          order: [['bid', 'DESC']],
          transaction: t,
        });
        await good.setSold(success.UserId, { transaction: t });
        await User.update({
          money: sequelize.literal(`money - ${success.bid}`),
        }, {
          where: { id: success.UserId },
          transaction: t,
        });
        await t.commit();
      } catch (error) {
        await t.rollback();
      }
    });
    const ongoing = await Good.findAll({ // 24시간이 지나지 않은 낙찰자 없는 경매들
      where: {
        SoldId: null,
        createdAt: { [Op.gte]: yesterday },
      },
    });
    ongoing.forEach((good) => {
      const end = new Date(good.createdAt);
      end.setDate(end.getDate() + 1); // 생성일 24시간 뒤가 낙찰 시간
      const job = scheduleJob(end, async () => {
        const t = await sequelize.transaction();
        const success = await Auction.findOne({
          where: { GoodId: good.id },
          order: [['bid', 'DESC']],
        });
        await good.setSold(success.UserId);
        await User.update({
          money: sequelize.literal(`money - ${success.bid}`),
        }, {
          where: { id: success.UserId },
        });
      });
      job.on('error', (err) => {
        console.error('스케줄링 에러', err);
      });
      job.on('success', () => {
        console.log('스케줄링 성공');
      });
    });

  } catch (error) {
    console.error(error);
  }
};
```

🔻 app.js

```js
...

const webSocket = require('./socket');
const checkAuction = require('./checkAuction');

const app = express();
passportConfig();
checkAuction();
app.set('port', process.env.PORT || 8010);

...
```

<br/>

### **🍳 서버 시작 시 이전 경매 체크하기**

24시간을 기다리면 낙찰됨

서버가 계속 켜져 있어야 함

빠른 테스트를 위해 시간을 단축해서 해보기

<br/>

---

<br/>

## **💻 프로젝트 마무리하기**

### **🍳 낙찰 내용 보기**

GET /list 라우터 작성 후 views/list.html 작성 및 views/layout.html 수정

🔻 routes/index.js

```js
...

const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const {
  renderMain, renderJoin, renderGood, createGood, renderAuction, bid, renderList,
} = require('../controllers');

...

router.get('/list', isLoggedIn, renderList);

module.exports = router;
```

🔻 controllers/index.js

```js
...
exports.renderList = async (req, res, next) => {
  try {
    const goods = await Good.findAll({
      where: { SoldId: req.user.id },
      include: { model: Auction },
      order: [[{ model: Auction }, 'bid', 'DESC']],
    });
    res.render('list', { title: '낙찰 목록 - NodeAuction', goods });
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

🔻 views/list.html / views/layout.html

[소스코드 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch13/13.4/node-auction/views){:target="_blank"}


<br/>

### **🍳 낙찰 내역 보기**

낙찰자의 계정으로 로그인 후 [http://localhost:8010/list](http://localhost:8010/list){:target="_blank"} 에서 낙찰 목록 확인 가능

<br/>

### **🍳 운영체제의 스케줄러**

node-schedule로 등록한 스케줄은 노드 서버가 종료될 때 같이 종료됨


- 운영체제의 스케줄러를 사용하는 것이 좋음

- 윈도에서는 schtasks

- 맥과 리눅스에서는 cron 추천

- 노드에서는 이 두 명령어를 child_process를 통해 호출할 수 있음

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

> [서버센트 이벤트 설명](https://developer.mozilla.org/ko/docs/Web/API/Server-sent_events/Using_server-sent_events){:target="_blank"}
{:.prompt-info}

> [EventSource](https://developer.mozilla.org/ko/docs/Web/API/EventSource/EventSource){:target="_blank"}
{:.prompt-info}

> [시퀄라이즈 정렬](https://sequelize.org/docs/v6/core-concepts/model-querying-basics/#ordering){:target="_blank"}
{:.prompt-info}

> [node-schedule 공식 문서](https://github.com/node-schedule/node-schedule#readme){:target="_blank"}
{:.prompt-info}

> [sse 공식 문서](https://www.npmjs.com/package/sse){:target="_blank"}
{:.prompt-info}

> [schtasks](https://learn.microsoft.com/en-us/windows/win32/taskschd/schtasks){:target="_blank"}
{:.prompt-info}

> [cron](https://ko.wikipedia.org/wiki/Cron){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}