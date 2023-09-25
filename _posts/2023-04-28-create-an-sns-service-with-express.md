---
layout: post
title: 9. 익스프레스로 SNS 서비스 만들기
author: admin
date: 2023-04-28 00:00:00 +900
lastmod: 2023-04-28 00:00:00 +900
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

# **🌈 익스프레스로 SNS 서비스 만들기**

### **🍳 npm init**

폴더를 만들고 package.json 파일 생성해서 직접 작성 또는 `npm init`

🔻 pakage.json

```js
{
  "name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "leekoby",
  "license": "MIT",
}
```
<br/>

### **🍳 sequelize 설치**

```sh
npm i sequelize mysql2 sequelize-cli
npx sequelize init
```

<br/>

### **🍳 폴더 구조 설정**

views(템플릿 엔진), routes(라우터), public(정적 파일), passport(패스포트) 폴더 생성

- app.js와 .env 파일도 생성

![폴더 구조](https://github.com/leekoby/leekoby.github.io/assets/118284808/ac5b4ea2-ac8f-40b5-af7d-e766a28495ed){: width="500" height="500" }

<br/>

### **🍳 패키지와 nodemon 설치**

npm 패키지 설치 후 nodemon도 설치

- nodemon은 서버 코드가 변경되었을 때 자동으로 서버를 재시작해줌

```sh
npm i express cookie-parser express-session morgan multer dotenv nunjucks
npm i -D nodemeon
```

🔻 pakage.json
```js
{
  "name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "app.js",
  "scripts": {
    "start": "nodemon app",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "leekoby",
  "license": "MIT",
  "dependencies": {
    "mysql2": "^3.6.1",
    "sequelize": "^6.33.0",
    "sequelize-cli": "^6.6.1"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}

```

### **🍳 app.js 작성**

노드 서버의 핵심인 app.js 파일 작성

- [소스 코드 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch9){:target="_blank"}

- .env 추가

🔻 .env

```js
COOKIE_SECRET=cookiesecret
```

🔻 app.js 초기 세팅

```js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config(); // process.env

const pageRouter = require('./routes/page');

const app = express();

app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extented: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false // https 적용할 때 true로 변경
  }
}));

app.use('/', pageRouter);

app.use((req, res, next) => { //404 not found
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  next(error);
});

app.use((err, req, res, next) => {//에러처리 미들웨어
  res.locals.message = err.message;
  res.locals.error = process.env.NODE_ENV !== 'production' ? err : {}; // 사용자 화면에 로그를 표시하는건 안좋음 
  //에러로그만 하는 서비스한테 넘김 
  res.satuts(err.status || 500);
  res.render('error');
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

### **🍳 라우터 생성**

- [소스 코드 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch9){:target="_blank"}

- routes/page.js: 템플릿 엔진을 렌더링하는 라우터

- views/layout.html: 프론트 엔드 화면 레이아웃(로그인/유저 정보 화면)

- views/main.html: 메인 화면(게시글들이 보임)

- views/profile.html: 프로필 화면(팔로잉 관계가 보임)

- views/error.html: 에러 발생 시 에러가 표시될 화면

- public/main.css: 화면 CSS

npm start로 서버 실행 후 [http://localhost:8001](http://localhost:8001/) 접속

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/d6a3ca21-81a4-4918-a71c-50c8949bf5a0)

🔻 routes/page.js

```js
const express = require('express');
const router = express.Router();
const { renderProfile, renderJoin, renderMain } = require('../controllers/page');

router.use((req, res, next) => {
  res.locals.user = null;
  res.locals.followerCount = 0;
  res.locals.followingCount = 0;
  res.locals.followingIdList = [];
  next();
});

router.get('/profile', renderProfile);
router.get('/join', renderJoin);
router.get('/', renderMain);

module.exports = router;
```

🔻 controller/page.js

```js
exports.renderProfile = (req, res, next) => {
  // 서비스를 호출 
  res.render('profile', { title: '내 정보 - NodeBird' });
};

exports.renderJoin = (req, res, next) => {
  res.render('join', { title: '회원 가입 - NodeBird' });
};

exports.renderMain = (req, res, next) => {
  res.render('main', {
    title: 'NodeBird',
    twits: []
  });

};
// 라우터 -> 컨트롤러 -> 서비스(요쳥, 응답 모른다)
```

<br/>

---

<br/>

## **💻 데이터베이스 세팅하기**


- [소스 코드 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch9/9.1/nodebird){:target="_blank"}

### **🍳 모델 생성**

- models/user.js: 사용자 테이블과 연결됨

- provider: 카카오 로그인인 경우 kakao, 로컬 로그인(이메일/비밀번호)인 경우 local

- snsId: 카카오 로그인인 경우 주어지는 id

- models/post.js: 게시글 내용과 이미지 경로를 저장(이미지는 파일로 저장)

- models/hashtag.js: 해시태그 이름을 저장(나중에 태그로 검색하기 위해서)

🔻models/index.js

```js
const Sequelize = require('sequelize');
const env = process.env.NODE_ENV || 'development';
const config = require('../config/config')[env];
const User = require('./user');
const Post = require('./post');
const Hashtag = require('./hashtag');

const db = {};
const sequelize = new Sequelize(
  config.database, config.username, config.password, config,
);

db.sequelize = sequelize;
db.User = User;
db.Post = Post;
db.Hashtag = Hashtag;

User.init(sequelize);
Post.init(sequelize);
Hashtag.init(sequelize);

User.associate(db);
Post.associate(db);
Hashtag.associate(db);

module.exports = db;
```

개선 작업 후

🔻models/index.js

```js
const Sequelize = require('sequelize');
const fs = require('fs');
const path = require('path');
const env = process.env.NODE_ENV || 'development';
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

🔻models/user.js

```js
const Sequelize = require('sequelize');

class User extends Sequelize.Model {
  static initiate(sequelize) {
    User.init({
      email: {
        type: Sequelize.STRING(40),
        allowNull: true,
        unique: true,
      },
      nick: {
        type: Sequelize.STRING(15),
        allowNull: false,
      },
      password: {
        type: Sequelize.STRING(100),
        allowNull: true
      },
      provider: {
        type: Sequelize.ENUM('local', 'kakao'),
        allowNull: false,
        defaultValue: 'local'
      },
      snsId: {
        type: Sequelize.STRING(30),
        allowNull: true,
      },
    }, {
      sequelize,
      timestamps: true, //createdAt, updatedAt
      underscored: false, //created_at, updated_at
      modelName: 'User', //javascript
      tableName: 'users', //db
      paranoid: true, // deletedAt 유저삭제일 // soft delete
      charset: 'utf8',
      collate: 'utf8_general_ci',
    });
  };

  static associatite(db) {
    db.User.hasMany(db.Post);
    db.User.belongsToMany(db.User, { // 팔로워
      foreignKey: 'followingId',
      as: 'Followers',
      through: 'Follow',
    });
    db.User.belongsToMany(db.User, { // 팔로잉
      foreignKey: 'followerId',
      as: 'Followings',
      through: 'Follow',
    });

  }

}
module.exports = User;

```


<br/>

### **🍳 associate 작성하기**

모델간의 관계들 associate에 작성

- 1대다: `hasMany`와 `belongsTo`

- 다대다: `belongsToMany`

- `foreignKey`: 외래키

- `as`: 컬럼에 대한 별명

- `through`: 중간 테이블명

🔻models/hastag.js

```js
const Sequelize = require('sequelize');

class Hashtag extends Sequelize.Model {
  static initiate(sequelize) {
    Hashtag.init({
      title: {
        type: Sequelize.STRING(15),
        allowNull: false,
        unique: true,
      }
    }, {
      sequelize,
      timestamps: true,
      underscored: false,
      paranoid: false,
      modelName: "Hashtag",
      tableName: 'hashtags',
      charset: 'utf8mb4',
      collate: 'utf8mb4_general_ci'
    });
  }

  static associatite(db) {
    db.Hashtag.belongsToMany(db.Post, { through: 'PostHashtag' });
  }

}
module.exports = Hashtag;
```

🔻 models/post.js

```js
const Sequelize = require('sequelize');

class Post extends Sequelize.Model {
  static initiate(sequelize) {
    Post.init({
      content: {
        type: Sequelize.STRING(140),
        allowNull: false,
      },
      img: {
        type: Sequelize.STRING(200),
        allowNull: true,
      }
    }, {
      sequelize,
      timestamps: true,
      underscored: false,
      paranoid: false,
      modelName: 'Post',
      tableName: 'posts',
      charset: 'utf8mb4',
      collate: 'utf8mb4_general_ci'
    });

  };

  static associatite(db) {
    db.Post.belongsTo(db.User);
    db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' });
  }

}
module.exports = Post;
```

🔻 models/user.js

```js
const Sequelize = require('sequelize');

class User extends Sequelize.Model {
  static initiate(sequelize) {
    User.init({
      email: {
        type: Sequelize.STRING(40),
        allowNull: true,
        unique: true,
      },
      nick: {
        type: Sequelize.STRING(15),
        allowNull: false,
      },
      password: {
        type: Sequelize.STRING(100),
        allowNull: true
      },
      provider: {
        type: Sequelize.ENUM('local', 'kakao'),
        allowNull: false,
        defaultValue: 'local'
      },
      snsId: {
        type: Sequelize.STRING(30),
        allowNull: true,
      },
    }, {
      sequelize,
      timestamps: true, //createdAt, updatedAt
      underscored: false, //created_at, updated_at
      modelName: 'User', //javascript
      tableName: 'users', //db
      paranoid: true, // deletedAt 유저삭제일 // soft delete
      charset: 'utf8',
      collate: 'utf8_general_ci',
    });
  };

  static associatite(db) {
    db.User.hasMany(db.Post);
    db.User.belongsToMany(db.User, { // 팔로워
      foreignKey: 'followingId',
      as: 'Followers',
      through: 'Follow',
    });
    db.User.belongsToMany(db.User, { // 팔로잉
      foreignKey: 'followerId',
      as: 'Followings',
      through: 'Follow',
    });

  }

}
module.exports = User;
```

<br/>

### **🍳 팔로잉-팔로워 다대다 관계**

User(다):User(다)

- 다대다 관계이므로 중간 테이블(Follow) 생성됨

- 모델 이름이 같으므로 구분 필요함(as가 구분자 역할, foreignKey는 반대 테이블 컬럼의 프라이머리 키 컬럼)

- 시퀄라이즈는 as 이름을 바탕으로 자동으로 addFollower, getFollowers, addFollowing, 
getFollowings 메서드 생성

![같은 테이블 간 N:M 관계](https://github.com/leekoby/leekoby.github.io/assets/118284808/70048ea2-1761-4a60-a9b8-0ae8a2f552bb)

<br/>

### **🍳 시퀄라이즈 설정하기**

시퀄라이즈 설정은 config/config.json에서

- 개발환경용 설정은 development 아래에

🔻 config/config.json

```js
{
  "development": {
    "username": "root",
    "password": "비밀번호",
    "database": "DB이름",
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

설정 파일 작성 후 데이터베이스 생성

```sh
npx sequelize db:create
```

<br/>

### **🍳 모델과 서버 연결하기**

sequelize.sync()가 테이블 생성

🔻app.js

```js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

const { sequelize } = require('./models');

dotenv.config(); // process.env

const pageRouter = require('./routes/page');

const app = express();

app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true
});

sequelize.sync().then(() => {
  console.log('데이터베이스 연결 성공');
}).catch((err) => {
  console.error(err);
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extented: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false // https 적용할 때 true로 변경
  }
}));

app.use('/', pageRouter);

app.use((req, res, next) => { //404 not found
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  next(error);
});

app.use((err, req, res, next) => {//에러처리 미들웨어
  res.locals.message = err.message;
  res.locals.error = process.env.NODE_ENV !== 'production' ? err : {}; // 사용자 화면에 로그를 표시하는건 안좋음 
  //에러로그만 하는 서비스한테 넘김 
  res.status(err.status || 500);
  res.render('error');
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

### **🍳 모델과 서버 연결하기**

npm start로 서버 실행 시 콘솔에 SQL문이 표시됨

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5fac2f21-b0e1-4a01-8fb5-dd28eee3dd61)

<br/>

---

<br/>

## **💻 Passport 모듈로 로그인**

### **🍳 패스포트 설치하기**

로그인 과정을 쉽게 처리할 수 있게 도와주는 Passport 설치하기

- 비밀번호 암호화를 위한 `bcrypt`도 같이 설치

- 설치 후 app.js와도 연결

- `passport.initialize()`: 요청 객체에 passport 설정을 심음

- `passport.session()`: req.session 객체에 passport 정보를 저장
  - express-session 미들웨어에 의존하므로 이보다 더 뒤에 위치해야 함

```sh
npm i passport passport-local passport-kakao bcrypt
```

🔻app.js

```js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const passport = require('passport');

dotenv.config(); // process.env
const pageRouter = require('./routes/page');
const authRouter = require('./routes/auth');
const { sequelize } = require('./models');
const passportConfig = require('./passport');

const app = express();
passportConfig(); // 패스포트 설정

app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true
});

sequelize.sync().then(() => {
  console.log('데이터베이스 연결 성공');
}).catch((err) => {
  console.error(err);
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json()); //req.body 를 ajax json 요청으로부터
app.use(express.urlencoded({ extented: false })); // req.body 폼으로 부터 
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false // https 적용할 때 true로 변경
  }
}));

app.use(passport.initialize()); // 반드시 express session 이후에 작성 
//req.user, req.login, req.isAuthentificate, req.logout
app.use(passport.session()); // connect.sid 라는 이름으로 세션 쿠키가 브라우저로 전송

app.use('/', pageRouter);
app.use('/auth', authRouter);

app.use((req, res, next) => { //404 not found
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  next(error);
});

app.use((err, req, res, next) => {//에러처리 미들웨어
  res.locals.message = err.message;
  res.locals.error = process.env.NODE_ENV !== 'production' ? err : {}; // 사용자 화면에 로그를 표시하는건 안좋음 
  //에러로그만 하는 서비스한테 넘김 
  res.status(err.status || 500);
  res.render('error');
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

### **🍳 패스포트 모듈 작성**

passport/index.js 작성

`passport.serializeUser`
- req.session 객체에 어떤 데이터를 저장할 지 선택
- 사용자 정보를 다 들고 있으면 메모리를 많이 차지하기 때문에 사용자의 아이디만 저장

`passport.deserializeUser`
- req.session에 저장된 사용자 아이디를 바탕으로 DB 조회로 사용자 정보를 얻어낸 후 req.user에 저장

![serializeUser와 deserializeUser의 관계](https://github.com/leekoby/leekoby.github.io/assets/118284808/8f9284d1-aa1b-4905-94f5-03978bb74150)

🔻passport/index.js

```js
const passport = require('passport');
const local = require('./localStrategy');
const kakao = require('./kakaoStrategy');
const User = require('../models/user');

module.exports = () => {
  passport.serializeUser((user, done) => {
    done(null, user.id);
  });

  passport.deserializeUser(((id, done) => {
    User.findOne({ where: { id } })
      .then((user) => done(null, user))
      .catch(err => done(err));
  }));

  local();
};
```

<br/>

### **🍳 로컬 로그인 구현하기**

#### **로그인 과정**

1. `/auth/login` 라우터를 통해 로그인 요청이 들어옴
2. 라우터에서 `passport.authenticate` 메서드 호출
3. 로그인 전략 수행(LocalStrategy)
4. 로그인 성공 시 사용자 정보 객체와 함께 req.login 호출
5. req.login 메서드가 `passport.serializeUser` 호출
6. req.session에 사용자 아이디만 저장해서 세션 생성
7. express-session에 설정한 대로 브라우저에 connect.sid 세션 쿠키 전송
8. 로그인 완료 

#### **로그인 이후 과정**

1. 요청이 들어옴
2. 라우터에 요청이 도달하기 전에  passport.session() 미들웨어가 `passport.deserializeUser` 메서드 호출
3. connect.sid 세션 쿠키를 읽고 세션 객체를 찾아서 req.session으로 만듦
4. req.session에 저장된 아이디로 데이터베이스에서 사용자 조회
5. 조회된 사용자 정보를 req.user에 저장
6. 라우터에서 req.user 객체 사용 가능

<br/>

#### **로컬 로그인 구현하기**

passport-local 패키지 필요

- 로컬 로그인 전략 수립
- 로그인에만 해당하는 전략이므로 회원가입은 따로 만들어야 함
- 사용자가 로그인했는지, 하지 않았는지 여부를 체크하는 미들웨어도 만듦

🔻 middlewares/index.js

```js
exports.isLoggedIn = (req, res, next) => {
  if (req.isAuthenticated()) { //패스포트 통해서 로그인 했는지
    next();
  } else {
    res.status(403).send('로그인 필요');
  }
};

exports.isNotLoggedIn = (req, res, next) => {
  if (!req.isAuthenticated()) { //패스포트 통해서 로그인 안 했는지
    next();
  } else {
    const message = encodeURIComponent('로그인한 상태입니다.');
    req.redirect(`/?error=${message}`);
  }
};
```

🔻 routes/page.js

```js
const express = require('express');
const router = express.Router();
const { renderProfile, renderJoin, renderMain } = require('../controllers/page');
const { isLoggedIn, isNotLoggedIn } = require('../middlewares');

router.use((req, res, next) => {
  res.locals.user = req.user;
  res.locals.followerCount = 0;
  res.locals.followingCount = 0;
  res.locals.followingIdList = [];
  next();
});

router.get('/profile', isLoggedIn, renderProfile);
router.get('/join', isNotLoggedIn, renderJoin);
router.get('/', renderMain);

module.exports = router;
```

#### **회원가입 라우터**

routes/auth.js 작성

- `bcrypt.hash`로 비밀번호 암호화

- hash의 두 번째 인수는 암호화 라운드

- 라운드가 높을수록 안전하지만 오래 걸림

- 적당한 라운드를 찾는 게 좋음

- error 쿼리스트링으로 1회성 메시지

#### **로그인 라우터**

routes/auth.js 작성

- `passport.authenticate(‘local’)`: 로컬 전략
  - 전략을 수행하고 나면 authenticate의 콜백 함수 호출됨

- `authError`: 인증 과정 중 에러,

- `user`: 인증 성공 시 유저 정보

- `info`: 인증 오류에 대한 메시지

- 인증이 성공했다면 req.login으로 세션에 유저 정보 저장

🔻routes/auth.js

```js
const express = require('express');
const router = express.Router();

const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const { join, login, logout } = require('../controllers/auth.js');

//POST /auth/join 
router.post('/join', isNotLoggedIn, join);

//post /auth/join
router.post('/login', isNotLoggedIn, login);

//get /auth/logout
router.get('/logout', isLoggedIn, logout);

module.exports = router;
```

🔻 controllers/auth.js

```js
const bcrypt = require('bcrypt');
const passport = require('passport');
const User = require('../models/user');

exports.join = async (req, res, next) => {
  const { nick, email, password } = req.body;

  try {
    const exUser = await User.findOne({ where: { email } });
    if (exUser) {
      return res.redirect(`/join?error=exist`);
    }
    const hash = await bcrypt.hash(password, 12);
    await User.create({
      email,
      nick,
      password: hash,
    });
    return res.redirect('/'); //302
  } catch (error) {
    console.error(error);
    next(error);
  }

};

//POST /auth/login
exports.login = (req, res, next) => {
  passport.authenticate('local', (authError, user, info) => {
    if (authError) { //서버실패
      console.error(authError);
      return next(authError);
    }
    if (!user) { //로직 실패
      return res.redirect(`/?loginError=${info.message}`);
    }
    return req.login(user, (loginError) => { //로그인 성공
      if (loginError) {
        console.error(loginError);
        return next(loginError);
      }
      return res.redirect('/');
    });
  })(req, res, next);// 미들웨어 내의 미들웨어에는 (req, res, next)를 붙입니다.
};

exports.logout = (req, res, next) => {
  req.logout(() => {
    res.redirect('/');
  });

};
```

#### **로컬 전략 작성**

passport/localStrategy.js 작성

- `usernameField` 와 `passwordField` 가 input 태그의 name(body-parser의 req.body)

- 사용자가 DB에 저장되어있는지 확인한 후 있다면 비밀번호 비교(`bcrypt.compare`)

- 비밀번호까지 일치한다면 로그인

🔻passport/localStrategy.js

```js
const passport = require('passport');
const { Strategy: LocalStrategy } = require('passport-local');
const bcrypt = require('bcrypt');
const User = require('../models/user');

module.exports = () => {
  passport.use(new LocalStrategy({
    usernameField: 'email', //req.body.email
    passwordField: 'password', //req.body.password
    passReqToCallback: false
  }, async (email, password, done) => { // done(서버실패, 성공유저, 로직실패)
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

![done과 passport.authenticate의 관계](https://github.com/leekoby/leekoby.github.io/assets/118284808/810c962a-92bc-4b99-b341-6f0d1c47909b)

### **🍳 카카오 로그인 구현**

#### **카카오 로그인 구현**

passport/kakaoStrategy.js 작성

- clientID에 카카오 앱 아이디 추가

- callbackURL: 카카오 로그인 후 카카오가 결과를 전송해줄 URL

- accessToken, refreshToken: 로그인 성공 후 카카오가 보내준 토큰(여기선 사용하지 않음)

- profile: 카카오가 보내준 유저 정보

- profile의 정보를 바탕으로 회원가입

🔻passport/kakaoStrategy.js

```js
const passport = require('passport');
const { Strategy: kakaoStrategy } = require('passport-kakao');
const User = require('../models/user');

module.exports = () => {
  passport.use(new kakaoStrategy({
    clientID: process.env.KAKAO_ID,
    callbackURL: '/auth/kakao/callback',
  }, async (accessToken, refreshToken, profile, done) => {
    console.log('profile', profile);
    try {
      const exUser = await User.findOne({ where: { snsId: profile.id, provider: 'kakao' } });
      if (exUser) { //로그인
        done(null, exUser);
      } else {//회원가입
        const newUser = await User.create({
          email: profile._json?.kakao_account?.email,
          nick: profile.displayName,
          provider: 'kakao'
        });
        done(null, newUser);
      }
    } catch (error) {
      console.error(error);
      done(error);
    }
  }));
};
```
<br/>

#### **카카오 로그인용 라우터 만들기**

회원가입과 로그인이 전략에서 동시에 수행됨

- passport.authenticate(‘kakao’)만 하면 됨

- /kakao/callback 라우터에서는 인증 성공 시(res.redirect)와 실패 시(failureRedirect) 리다이렉트할 경로 지정

🔻routes/auth.js

```js
const express = require('express');
const router = express.Router();
const passport = require('passport');

const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const { join, login, logout } = require('../controllers/auth.js');

//POST /auth/join 
router.post('/join', isNotLoggedIn, join);

//post /auth/join
router.post('/login', isNotLoggedIn, login);

//get /auth/logout
router.get('/logout', isLoggedIn, logout);

//get /auth/kakao
router.get('/kakao', passport.authenticate('kakao'));

//get /auth/kakao/callback
router.get('/kakao/callback', passport.authenticate('kakao', {
  failureRedirect: `/?loginError=카카오 로그인 실패`
}), (req, res) => {
  res.redirect('/');
});

module.exports = router;
```

🔻app.js

```js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const passport = require('passport');

dotenv.config();
const pageRouter = require('./routes/page');
const authRouter = require('./routes/auth');
const { sequelize } = require('./models');
const passportConfig = require('./passport');

const app = express();
passportConfig(); // 패스포트 설정
app.set('port', process.env.PORT || 8001);
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

app.use('/', pageRouter);
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

#### **카카오 로그인 앱 만들기**

[KAKAO DEVELOPER](https://developers.kakao.com/){:target="_blank"} 접속하여 회원가입 

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ecfe26b1-d10e-46e9-9771-dcbdfa2824bf)

REST API 키를 저장해서 .env에 저장

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c795936d-9046-4774-bc15-fde6bb73b821)

🔻.env
```js
COOKIE_SECRET=nodebirdsecret
KAKAO_ID=5d4daf57becfd72fd9c919882552c4a6
```

<br/>

#### **카카오 웹 플랫폼 추가**

웹 플랫폼을 추가해야 callbackURL 등록할 수 있음

http://localhost:8001 등록

![카카오 웹 플랫폼 추가](https://github.com/leekoby/leekoby.github.io/assets/118284808/39f65aac-c4f5-4b3b-9bc7-dbd8a1cc4e7e)

<br/>

#### **카카오 동의항목 설정**

이메일, 생일 등의 정보를 얻기 위해 동의항목 설정

![카카오 동의항목 설정](https://github.com/leekoby/leekoby.github.io/assets/118284808/c3ac619f-da69-4375-820d-d074b8b6d67d)

<br/>

#### **카카오 로그인 시도**

카카오톡 로그인 버튼을 누르면 카카오 로그인 창으로 전환

계정 동의 후 다시 NodeBird 서비스로 리다이렉트

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a778697c-dbfd-4a68-a82e-23f1f0421ba4)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2eeeb3dc-b44e-4ad8-9877-4d72c8968faa)

<br/>

---

<br/>

## **💻 Multer 모듈로 이미지 업로드 구현하기**

### **🍳  이미지 업로드 구현**

form 태그의 enctype이 multipart/form-data

- body-parser로는 요청 본문을 해석할 수 없음

- multer 패키지 필요

```sh
npm i multer
```

이미지를 먼저 업로드하고, 이미지가 저장된 경로를 반환할 것임

게시글 form을 submit할 때는 이미지 자체 대신 경로를 전송

<br/>

#### **이미지 업로드 라우터 구현**

fs.readdir, fs.mkdirSync로 upload 폴더가 없으면 생성

multer() 함수로 업로드 미들웨어 생성

`storage`: diskStorage는 이미지를 서버 디스크에 저장(destination은 저장 경로, filename은 저장 파일명)

`limits`는 파일 최대 용량(5MB)

`upload.single(‘img’)`: 요청 본문의 img에 담긴 이미지 하나를 읽어 설정대로 저장하는 미들웨어
저장된 파일에 대한 정보는 req.file 객체에 담김

<br/>

#### **게시글 등록**

upload2.none()은 multipart/formdata 타입의 요청이지만 이미지는 없을 때 사용

- 게시글 등록 시 아까 받은 이미지 경로 저장

- 게시글에서 해시태그를 찾아서 게시글과 연결(post.addHashtags)

- findOrCreate는 기존에 해시태그가 존재하면 그걸 사용하고, 없다면 생성하는 시퀄라이즈 메서드

🔻 routes/post.js

```js
const express = require('express');
const router = express.Router();
const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const fs = require('fs');
const path = require('path');
const multer = require('multer');
const { afterUploadImage, uploadPost } = require('../controllers/post');

try {
  fs.readdirSync('uploads');
} catch (error) {
  console.error('uploads 폴더가 없어 uploads 폴더를 생성합니다.');
  fs.mkdirSync('uploads');
}

const upload = multer({
  storage: multer.diskStorage({
    destination(req, res, cb) {
      cb(null, 'uploads/');
    },
    filename(req, file, cb) {
      console.log(file);
      const ext = path.extname(file.originalname);
      cb(null, path.basename(file.originalname, ext) + Date.now() + ext);
    }
  }),
  limits: { fileSize: 5 * 1024 * 1024 }
});

router.post('/img', isLoggedIn, upload.single('img'), afterUploadImage);

const upload2 = multer();

router.post('/', isLoggedIn, upload2.none(), uploadPost);

module.exports = router;
```

<br/>

#### **메인 페이지에 게시글 보여주기**

메인 페이지(/) 요청 시 게시글을 먼저 조회한 후 템플릿 엔진 렌더링

- include로 관계가 있는 모델을 합쳐서 가져올 수 있음

- Post와 User는 관계가 있음 (1대다)

- 게시글을 가져올 때 게시글 작성자까지 같이 가져오는 것

🔻 controllers/page.js

```js
const { User, Post } = require('../models');

exports.renderProfile = (req, res) => {
  res.render('profile', { title: '내 정보 - NodeBird' });
};

exports.renderJoin = (req, res) => {
  res.render('join', { title: '회원가입 - NodeBird' });
};

exports.renderMain = async (req, res, next) => {
  try {
    const posts = await Post.findAll({
      include: {
        model: User,
        attributes: ['id', 'nick'],
      },
      order: [['createdAt', 'DESC']],
    });
    res.render('main', {
      title: 'NodeBird',
      twits: posts,
    });
  } catch (err) {
    console.error(err);
    next(err);
  }
};
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/db77ea40-c8a1-405e-af9e-b8d511d45e8d)

<br/>

---

<br/>

## **💻 프로젝트 마무리하기**

### **🍳 팔로잉 기능 구현**

POST /:id/follow 라우터 추가

- /사용자아이디/follow

- 사용자 아이디는 req.params.id로 접근

- user.addFollowing(사용자아이디)로 팔로잉하는 사람 추가

🔻routes/user.js

```js
const express = require('express');

const { isLoggedIn } = require('../middlewares');
const { follow } = require('../controllers/user');

const router = express.Router();

// POST /user/:id/follow
router.post('/:id/follow', isLoggedIn, follow);

module.exports = router;

```

deserializeUser 수정

- req.user.Followers로 팔로워 접근 가능

- req.user.Followings로 팔로잉 접근

- 단, 목록이 유출되면 안 되므로 팔로워/팔로잉 숫자만 프런트로 전달

🔻passport/index.js

```js
const passport = require('passport');
const local = require('./localStrategy');
const kakao = require('./kakaoStrategy');
const User = require('../models/user');

module.exports = () => {
  passport.serializeUser((user, done) => {
    console.log('serialize');
    done(null, user.id);
  });

  passport.deserializeUser((id, done) => {
    console.log('deserialize');
    User.findOne({
      where: { id },
      include: [{
        model: User,
        attributes: ['id', 'nick'],
        as: 'Followers',
      }, {
        model: User,
        attributes: ['id', 'nick'],
        as: 'Followings',
      }],
    })
      .then(user => {
        console.log('user', user);
        done(null, user);
      })
      .catch(err => done(err));
  });

  local();
  kakao();
};
```

🔻controller/user.js

```js
const User = require('../models/user');

exports.follow = async (req, res, next) => {
  try {
    const user = await User.findOne({ where: { id: req.user.id } });
    if (user) { // req.user.id가 followerId, req.params.id가 followingId
      await user.addFollowing(parseInt(req.params.id, 10));
      res.send('success');
    } else {
      res.status(404).send('no user');
    }
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

🔻routes/page.js

```js
const express = require('express');
const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const {
  renderProfile, renderJoin, renderMain, renderHashtag,
} = require('../controllers/page');

const router = express.Router();

router.use((req, res, next) => {
  res.locals.user = req.user;
  res.locals.followerCount = req.user?.Followers?.length || 0;
  res.locals.followingCount = req.user?.Followings?.length || 0;
  res.locals.followingIdList = req.user?.Followings?.map(f => f.id) || [];
  next();
});

router.get('/profile', isLoggedIn, renderProfile);

router.get('/join', isNotLoggedIn, renderJoin);

router.get('/', renderMain);

router.get('/hashtag', renderHashtag);

module.exports = router;

```

<br/>

### **🍳 해시태그 검색 기능 추가**

GET /hashtag 라우터 추가

- 해시태그를 먼저 찾고(hashtag)

- hashtag.getPosts로 해시태그와 관련된 게시글을 모두 찾음

- 찾으면서 include로 게시글 작성자 모델도 같이 가져옴

🔻routes/page.js

```js
const express = require('express');
const { isLoggedIn, isNotLoggedIn } = require('../middlewares');
const {
  renderProfile, renderJoin, renderMain, renderHashtag,
} = require('../controllers/page');

const router = express.Router();

router.use((req, res, next) => {
  res.locals.user = req.user;
  res.locals.followerCount = req.user?.Followers?.length || 0;
  res.locals.followingCount = req.user?.Followings?.length || 0;
  res.locals.followingIdList = req.user?.Followings?.map(f => f.id) || [];
  next();
});

router.get('/profile', isLoggedIn, renderProfile);

router.get('/join', isNotLoggedIn, renderJoin);

router.get('/', renderMain);

router.get('/hashtag', renderHashtag);

module.exports = router;

```

<br/>

### **🍳 업로드한 이미지 제공하기**

express.static 미들웨어로 uploads 폴더에 저장된 이미지 제공

- 프런트엔드에서는 /img/이미지명 주소로 이미지 접근 가능

🔻app.js

```js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const passport = require('passport');

dotenv.config(); // process.env
const pageRouter = require('./routes/page');
const authRouter = require('./routes/auth');
const postRouter = require('./routes/post');
const userRouter = require('./routes/user');
const { sequelize } = require('./models');
const passportConfig = require('./passport');

const app = express();
passportConfig(); // 패스포트 설정

app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true
});

sequelize.sync({ force: false }).then(() => {
  console.log('데이터베이스 연결 성공');
}).catch((err) => {
  console.error(err);
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use('/img', express.static(path.join(__dirname, 'uploads')));
app.use(express.json()); //req.body 를 ajax json 요청으로부터
app.use(express.urlencoded({ extented: false })); // req.body 폼으로 부터 
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false // https 적용할 때 true로 변경
  }
}));

app.use(passport.initialize()); // 반드시 express session 이후에 작성 
//req.user, req.login, req.isAuthentificate, req.logout
app.use(passport.session()); // connect.sid 라는 이름으로 세션 쿠키가 브라우저로 전송

app.use('/', pageRouter);
app.use('/auth', authRouter);
app.use('/post', postRouter);
app.use('/user', userRouter);


app.use((req, res, next) => { //404 not found
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  next(error);
});

app.use((err, req, res, next) => {//에러처리 미들웨어
  res.locals.message = err.message;
  res.locals.error = process.env.NODE_ENV !== 'production' ? err : {}; // 사용자 화면에 로그를 표시하는건 안좋음 
  //에러로그만 하는 서비스한테 넘김 
  res.status(err.status || 500);
  res.render('error');
});

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

<br/>

### **🍳 프로젝트 화면**

서버를 실행하고 http://localhost:8001 접속

![프로젝트 화면](https://github.com/leekoby/leekoby.github.io/assets/118284808/55470633-84d6-4b53-8f16-f39c5e38c938)


<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

>[Passport 공식문서](https://www.passportjs.org/){:target="_blank"}
{:.prompt-info}

>[Passport-local 공식문서](https://www.passportjs.org/packages/passport-local/){:target="_blank"}
{:.prompt-info}

>[Passport-kakao 공식문서](https://www.passportjs.org/packages/passport-kakao/){:target="_blank"}
{:.prompt-info}

>[bcrypt 공식문서](https://www.npmjs.com/package/bcrypt){:target="_blank"}
{:.prompt-info}

>[카카오 로그인](https://developers.kakao.com/docs/latest/ko/kakaologin/common){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}