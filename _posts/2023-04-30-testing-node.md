---
layout: post
title: 11. 노드 서비스 테스트하기
author: admin
date: 2023-04-30 00:00:00 +900
lastmod: 2023-04-30 00:00:00 +900
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

# **🌈 노드 서비스 테스트하기**

## **💻 테스트 준비하기**

### **🍳 테스트를 하는 이유**

자신이 만든 서비스가 제대로 동작하는지 테스트해야 함

- 기능이 많다면 수작업으로 테스트하기 힘듦

- 프로그램이 프로그램을 테스트할 수 있도록 자동화함

- 테스트 환경을 최대한 실제 환경과 비슷하게 흉내냄

- 아무리 철저하게 테스트해도 에러를 완전히 막을 수는 없음


테스트를 하면 좋은 점

- 허무한 에러로 인해 프로그램이 고장나는 것은 막을 수 있음

- 한 번 발생한 에러는 테스트로 만들어두면 같은 에러가 발생하지 않게 막을 수 있음

- 코드를 수정할 때 프로그램이 자동으로 어떤 부분이 고장나는 지 알려줌

<br/>

### **🍳 Jest 설치하기**

```sh
npm i –D jest
```

🔻 pakage.json

```js
...
{
  "name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "app.js",
  "scripts": {
    "start": "nodemon app",
    "test": "jest"  // <- 이 부분을 작성해줌
  },
  ...
```

Nodebird 프로젝트를 그대로 사용함

<br/>

### **🍳 테스트 실행해보기**

```sh
npm test
```
테스트용 파일은 파일명에 test나 spec이 있으면 됨

npm test로 test나 spec 파일들을 테스트함.

아무런 테스트를 작성하지 않고 실행해 봄

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9fa47565-8dbf-4ae7-aedd-7ee05fa144c3)


routes 폴더 안에 middlewares.test.js 생성

테스트를 아무것도 작성하지 않았으므로 에러 발생(테스트 실패)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4127edae-261a-49b7-8c4e-da2f786cdc27)


<br/>

### **🍳 첫 테스트 코드 작성하기**

middlewares.test.js 작성하기

test 함수의 첫 번째 인수로 테스트에 대한 설명

두 번째 인수인 함수에는 테스트 내용을 적음

`expect` 함수의 인수로 실제 코드를, `toEqual` 함수의 인수로는 예상되는 결괏값을

expect와 toEqual의 인수가 일치하면 테스트 통과

🔻 middlewares/index.test.js

```js
test('1 + 1 은 2 입니다.', () => {
  expect(1 + 1).toEqual(2);
});
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/23c975e4-80c9-42c2-bd0f-a4a7b8e90d1b)

<br/>

### **🍳 실패하는 경우**

두 인수를 다르게 작성하면 실패(메시지를 살펴볼 것)

🔻 middlewares/index.test.js

```js
test('1 + 1 은 2 입니다.', () => {
  expect(1 + 1).toEqual(3);
});
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/0b7120fd-5532-4110-9cad-8105bb60b005)

<br/>

---

<br/>

## **💻 유닛(단위) 테스트**

### **🍳 middlewares 테스트하기**

`toBeCalled`로 함수 호출 테스트 

`toBeCalledTimes`로 몇번 호출할지 정할 수 있음

`toBeCalledWith`로 함수의 호출여부와 파라미터를 테스트할 수 있다.



🔻 middlewares/index.test.js

```js
const { isLoggedIn, isNotLoggedIn } = require('./');

//그룹화 -> describe
describe('isLoggedIn', () => {
  const res = {
    status: jest.fn(() => res),
    send: jest.fn(),
  };
  const next = jest.fn();

  test('로그인 되어있으면 isLoggedIn이 next를 호출해야 함', () => {
    const req = {
      isAuthenticated: jest.fn(() => true),
    };
    isLoggedIn(req, res, next);
    expect(next).toBeCalledTimes(1);
  });

  test('로그인 되어있지 않으면 isLoggedIn이 에러를 응답해야 함', () => {
    const req = {
      isAuthenticated: jest.fn(() => false),
    };
    isLoggedIn(req, res, next);
    expect(res.status).toBeCalledWith(403);
    expect(res.send).toBeCalledWith('로그인 필요');
  });
});

describe('isNotLoggedIn', () => {
  const res = {
    redirect: jest.fn(),
  };
  const next = jest.fn();

  test('로그인 되어있으면 isNotLoggedIn이 에러를 응답해야 함', () => {
    const req = {
      isAuthenticated: jest.fn(() => true),
    };
    isNotLoggedIn(req, res, next);
    const message = encodeURIComponent('로그인한 상태입니다.');
    expect(res.redirect).toBeCalledWith(`/?error=${message}`);
  });

  test('로그인 되어있지 않으면 isNotLoggedIn이 next를 호출해야 함', () => {
    const req = {
      isAuthenticated: jest.fn(() => false),
    };
    isNotLoggedIn(req, res, next);
    expect(next).toHaveBeenCalledTimes(1);
  });
});

```

<br/>

### **🍳 컨트롤러 테스트**

🔻 controllers/user.js

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

follow 함수는 async 함수이므로 await를 붙여야 함수가 전부 실행 완료된 후 expect 함수가 실행된다. 

🔻 controllers/user.test.js

```js
const { follow } = require('./user');

describe('follow', () => {
  const req = {
    user: { id: 1 },
    params: { id: 2 }
  };

  const res = {
    status: jest.fn(() => res),
    send: jest.fn()
  };

  const next = jest.fn();

  test('사용자를 찾아 팔로잉을 추가하고 success를 응답해야함', async () => {
    await follow(req, res, next);
    expect(res.send).toBeCalledWith('success');
  });

  test('사용자를 못 찾으면 res.status(404).send(no user)를 호출함', async () => {
    await follow(req, res, next);
    expect(res.status).toBeCalledWith(404);
    expect(res.send).toBeCalledWith('no user');
  });

  test('DB에서 에러가 발생하면 next(error)를 호출함', async () => {
    await follow(req, res, next);
    expect(next).toBeCalledWith(message);
  });
});
```

이 테스트 코드는 실패함

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b5c8df2b-f1ec-439b-9a12-ca1593602192)

그 이유는 바로 User 모델 떄문임

follow 컨트롤러 안에는 User라는 모델이 들어있음 

근데 이 모델은 실제 데이터베이스와 연결되어 있으므로 테스트 환경에서는 사용할 수 없음 

따라서 모킹을 사용해야함 

🔻 controllers/user.test.js

```js
jest.mock('../models/user');
const User = require('../models/user');
const { follow } = require('./user');

describe('follow', () => {
  const req = {
    user: { id: 1 },
    params: { id: 2 }
  };

  const res = {
    status: jest.fn(() => res),
    send: jest.fn()
  };

  const next = jest.fn();

  test('사용자를 찾아 팔로잉을 추가하고 success를 응답해야함', async () => {
    User.findOne.mockReturnValue({
      addFollowing(id) {
        return Promise.resolve(true);
      }
    });
    await follow(req, res, next);
    expect(res.send).toBeCalledWith('success');
  });

  test('사용자를 못 찾으면 res.status(404).send(no user)를 호출함', async () => {
    User.findOne.mockReturnValue(null);

    await follow(req, res, next);
    expect(res.status).toBeCalledWith(404);
    expect(res.send).toBeCalledWith('no user');
  });

  test('DB에서 에러가 발생하면 next(error)를 호출함', async () => {
    const message = 'DB에러';
    User.findOne.mockReturnValue(Promise.reject(message));
    await follow(req, res, next);
    expect(next).toBeCalledWith(message);
  });
});
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/375f10dd-f055-433b-9b14-f74d493b6b85)


<br/>

### **🍳 서비스로 분리하기**

🔻 services/user.js

```js
const User = require('../models/user');

exports.follow = async (userId, followingId) => {
  const user = await User.findOne({ where: { id: userId } });
  if (user) {
    await user.addFollowing(parseInt(followingId, 10));
    return 'ok';
  } else {
    return 'no user';
  }
};
```

🔻 controllers/user.js

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

🔻 services/user.test.js

```js
jest.mock('../models/user');
const User = require('../models/user');
const { follow } = require('./user');

describe('follow', () => {
  test('사용자를 찾아 팔로잉을 추가하고 ok를 반환함', async () => {
    User.findOne.mockReturnValue({
      addFollowing(id) {
        return Promise.resolve(true);
      }
    });
    const result = await follow(1, 2);
    expect(result).toEqual('ok');
  });

  test('사용자를 못 찾으면 no user를 반환함', async () => {
    User.findOne.mockReturnValue(null);
    const result = await follow(1, 2);
    expect(result).toEqual('no user');
  });

  test('DB에서 에러가 발생하면 Throw', async () => {
    const message = 'DB에러';
    User.findOne.mockReturnValue(Promise.reject(message));
    try {
      await follow(1, 2);
    } catch (err) {
      expect(err).toEqual(message);
    }
  });
});
```

🔻 controllers/user.test.js

```js
jest.mock('../services/user');
const { follow } = require('./user');
const { follow: followService } = require('../services/user');

describe('follow', () => {
  const req = {
    user: { id: 1 },
    params: { id: 2 }
  };

  const res = {
    status: jest.fn(() => res),
    send: jest.fn()
  };

  const next = jest.fn();

  test('사용자를 찾아 팔로잉을 추가하고 success를 응답해야함', async () => {
    followService.mockReturnValue('ok');
    await follow(req, res, next);
    expect(res.send).toBeCalledWith('success');
  });

  test('사용자를 못 찾으면 res.status(404).send(no user)를 호출함', async () => {
    followService.mockReturnValue(null);
    await follow(req, res, next);
    expect(res.status).toBeCalledWith(404);
    expect(res.send).toBeCalledWith('no user');
  });

  test('DB에서 에러가 발생하면 next(error)를 호출함', async () => {
    const message = 'DB에러';
    followService.mockReturnValue(Promise.reject(message));
    await follow(req, res, next);
    expect(next).toBeCalledWith(message);
  });
});
```

<br/>

---

<br/>

## **💻 테스트 커버리지**

### **🍳 테스트 커버리지란**

전체 코드 중에서 테스트되고 있는 코드의 비율

테스트되지 않는 코드의 위치도 알려줌

- "coverage": "jest --coverage"
- Stmts: 구문
- Branch: 분기점
- Funcs: 함수
- Lines: 줄 수

🔻 pakage.json
```
{
  "name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "app.js",
  "scripts": {
    "start": "nodemon app",
    "test": "jest",
    "coverage": "jest --coverage"
  },
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9a1672eb-4279-4965-b0e2-35d0b3e5cd78)


<br/>

### **🍳 테스트 커버리지 올리기**

models/users.test.js 작성

테스트 커버리지를 상승시킬 뿐 의미는 없는 테스트

🔻models/users.test.js

```js
const Sequelize = require('sequelize');
const User = require('./user');
const config = require('../config/config')['test'];
const sequelize = new Sequelize(
  config.database, config.username, config.password, config,
);

describe('User 모델', () => {
  test('static init 메서드 호출', () => {
    expect(User.initiate(sequelize)).toBe(undefined);
  });
  test('static associate 메서드 호출', () => {
    const db = {
      User: {
        hasMany: jest.fn(),
        belongsToMany: jest.fn(),
      },
      Post: {},
    };
    User.associate(db);
    expect(db.User.hasMany).toHaveBeenCalledWith(db.Post);
    expect(db.User.belongsToMany).toHaveBeenCalledTimes(2);
  });
});
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/dedf75a9-89ca-4500-90dc-eeb3f2a2bb80)

파일명에 test 있는거 제외시킴

🔻 models/index.js

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
    return (file.indexOf('.') !== 0) && !file.includes('test') && (file !== basename) && (file.slice(-3) === '.js');
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

### **🍳 테스트 커버리지 주의점**

모든 코드가 테스트되지 않는데도 커버리지가 100%임

- 테스트 커버리지를 맹신할 필요가 없음

- 커버리지를 높이는 것이 의미는 있지만 높이는 데 너무 집착할 필요는 없음

- 필요한 부분 위주로 올바르게 테스트하는 것이 좋음

<br/>

---

<br/>

## **💻 통합 테스트**

### **🍳 통합 테스트 해보기**

라우터 하나를 통째로 테스트해 봄(여러 개의 미들웨어, 모듈을 한 번에 테스트).

app.js 분리하기
Supertest 사용

```sh
npm i -D supertest
```

app.js에서 listen 부분을 분리해야함

🔻 app.js

```js

module.exports = app;

// app.listen(app.get('port'), () => {
//   console.log(app.get('port'), '번 포트에서 대기중');
// });

```

🔻 server.js

```js
const app = require('./app');

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

🔻 pakage.json

```js
"name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "server.js",
```

<br/>

### **🍳 테스트용 DB 설정하기**

개발/배포용 DB랑 별도로 설정하는 것이 좋음

- config/config.json의 test 속성

🔻config/config.json

```js
{
  "development": {
    "username": "root",
    "password": "nodejsbook",
    "database": "nodebird",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": "nodejsbook",
    "database": "nodebird_test",
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
npx sequelize db:create --env test
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c1e5515d-f097-4fc2-8bd0-a3b4720981b1)


<br/>

### **🍳 라우터 테스트**

routes/auth.test.js 작성

- beforeAll: 모든 테스트 전에 딱 한번 실행

- afterAll : 모든 테스트가 끝나고 나서 한번만 호출

- beforEach와 afterEach는 테스트를 실행할 때마다 동작한다.
  - beforEach는 테스트 전에 동작하고,
  - afterEach는 테스트 후에 동작한다.

- request(app).post(주소)로 요청

- send로 data 전송

- done 호출에 주의

- agent 변수로 선언해서 일치시킴 ` const agent = request.agent(app);`

🔻routes/auth.test.js

```js
const request = require('supertest');
const { sequelize } = require('../models');
const app = require('../app');

beforeAll(async () => {
  await sequelize.sync();
});

describe('POST /join', () => {
  test('로그인 안 했으면 가입', (done) => {
    request(app)
      .post('/auth/join')
      .send({
        email: 'zerohch0@gmail.com',
        nick: 'zerocho',
        password: 'nodejsbook',
      })
      .expect('Location', '/')
      .expect(302, done);
  });
});

describe('POST /join', () => {
  const agent = request.agent(app);
  beforeEach((done) => {
    agent
      .post('/auth/login')
      .send({
        email: 'zerohch0@gmail.com',
        password: 'nodejsbook',
      })
      .end(done);
  });

  test('이미 로그인했으면 redirect /', (done) => {
    const message = encodeURIComponent('로그인한 상태입니다.');
    agent
      .post('/auth/join')
      .send({
        email: 'zerohch0@gmail.com',
        nick: 'zerocho',
        password: 'nodejsbook',
      })
      .expect('Location', `/?error=${message}`)
      .expect(302, done);
  });
});

describe('POST /login', () => {
  test('가입되지 않은 회원', (done) => {
    const message = encodeURIComponent('가입되지 않은 회원입니다.');
    request(app)
      .post('/auth/login')
      .send({
        email: 'zerohch1@gmail.com',
        password: 'nodejsbook',
      })
      .expect('Location', `/?error=${message}`)
      .expect(302, done);
  });

  test('로그인 수행', (done) => {
    request(app)
      .post('/auth/login')
      .send({
        email: 'zerohch0@gmail.com',
        password: 'nodejsbook',
      })
      .expect('Location', '/')
      .expect(302, done);
  });

  test('비밀번호 틀림', (done) => {
    const message = encodeURIComponent('비밀번호가 일치하지 않습니다.');
    request(app)
      .post('/auth/login')
      .send({
        email: 'zerohch0@gmail.com',
        password: 'wrong',
      })
      .expect('Location', `/?error=${message}`)
      .expect(302, done);
  });
});

describe('GET /logout', () => {
  test('로그인 되어있지 않으면 403', (done) => {
    request(app)
      .get('/auth/logout')
      .expect(403, done);
  });

  const agent = request.agent(app);
  beforeEach((done) => {
    agent
      .post('/auth/login')
      .send({
        email: 'zerohch0@gmail.com',
        password: 'nodejsbook',
      })
      .end(done);
  });

  test('로그아웃 수행', (done) => {
    agent
      .get('/auth/logout')
      .expect('Location', `/`)
      .expect(302, done);
  });
});

afterAll(async () => {
  await sequelize.sync({ force: true }); 
});
```

afterAll을 사용해서 DB 초기화를 해줌

<br/>

---

<br/>

## **💻 부하 테스트**

### **🍳 부하 테스트란**

서버가 얼마만큼의 요청을 견딜 수 있는지 테스트

- 서버가 몇 명의 동시 접속자를 수용할 수 있는지 예측하기 매우 어려움

- 실제 서비스 중이 아니라 개발 중일 때는 더 어려움

- 코드에 문제가 없더라도 서버 하드웨어 때문에 서비스가 중단될 수 있음(메모리 부족 문제 등)

- 부하 테스트를 통해 미리 예측할 수 있음

<br/>

### **🍳 Artillery 사용하기**

```sh
npm i -D artillery
```

개발 환경에서만 할 것 

새 콘솔에서 다음 명령어 입력

- Count 옵션은 가상의 사용자 수
- N 옵션은 횟수
- 100명의 사용자가 50번씩 요청을 보내는 상황
- 결과 보고서
- 사용자 생성(scenarios launched)
- 테스트 성공(scenarios completed)
- 요청 성공 횟수(requests completed)
- 초당 요청 처리 횟수(RPS sent)
- 응답 지연 속도(Request latency)
- Min: 최소, Max: 최대, median: 중앙값
- P95: 하위 95%, P99: 하위 99%
- 하위는 속도 순서를 말함
- Median과 P95가 많이 차이나지 않는 게 좋음

```sh
npx artillery quick --count 100 -n 50 http://localhost:8001
# 100명이 50번씩 http://localhost:8001에 GET 요청을 보낸다.
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b919c142-5239-45fe-8799-c15b4cefc373)

<br/>

### **🍳 여러 페이지 요청 시나리오**

loadtest.json에 사용자의 행동 흐름 작성 가능

- target: 요청 도메인
- Phases에서 duration: 몇 초 동안(60초)
- arrivalRate: 매 초 몇 명(30명)
- flow: 사용자의 이동
- get, post 등의 메서드를 나타냄
- url은 이동한 url
- json은 서버로 전송한 데이터
- 현재 GET /, POST /auth/login, GET /hashtag 순

🔻loadtest.json

```js
{
  "config": {
    "target": "http://localhost:8001",
    "http": {
      "timeout": 30
    },
    "phases": [
      {
        "duration": 30,
        "arrivalRate": 20
      }
    ]
  },
  "scenarios": [
    {
      "flow": [
        {
          "get": {
            "url": "/"
          }
        },
        {
          "post": {
            "url": "/auth/login",
            "json": {
              "email": "abc2752@naver.com",
              "password": "!leekoby11"
            },
            "followRedirect": false
          }
        },
        {
          "get": {
            "url": "/hashtag?hashtag=nodebird"
          }
        }
      ]
    }
  ]
}
```

<br/>

### **🍳 여러 페이지 요청 시나리오(실행)**

문제점 발견

- 요청 후반부가 될 수록 응답 시간이 길어짐

- 첫 응답은 4.7밀리초, 마지막 응답은 51초

- 5400개의 요청은 200 응답코드, 1800개는 302

- 서버가 지금 정도의 요청을 감당하지 못함

- 서버 사양을 업그레이드하거나, 여러 개 두거나

- 코드를 더 효율적으로 개선하는 방법 등.

- 현재는 싱글코어만 사용하므로, 클러스터링 기법 도입을 시도해볼만 함

- arrivalRate를 줄이거나 늘려서 어느 정도 수용 가능한지 체크해보는 것이 좋음

- 여러 번 테스트하여 평균치를 내보는 게 좋음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/d1eced31-c682-4253-beb2-37b01980fbc2)


<br/>

### **🍳 테스트 범위**

어떤 것을 테스트하고 어떤 것을 테스트 안 할 지 고민됨.

- 자신이 짠 코드는 최대한 많이 테스트하기

- npm을 통해 설치한 패키지는 테스트하지 않음(그걸 만든 사람의 몫임)

- 우리는 그 패키지/라이브러리를 사용하는 부분만 테스트

- 테스트하기 어려운 패키지는 모킹

- 모킹해서 통과하더라도 실제 상황에서는 에러날 수 있음을 염두에 두어야 함

- 시스템 테스트: QA처럼 테스트 목록을 두고 체크해나가면서 진행하는 테스트

- 인수 테스트: 알파 테스트/베타 테스트처럼 특정 사용자 집단이 실제로 테스트

- 다양한 종류의 테스트를 주기적으로 수행해 서비스를 안정적으로 유지하는 게 좋음

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

> [노드의 test 모듈](https://nodejs.org/api/test.html){:target="_blank"}
{:.prompt-info}

> [노드의 assert 모듈](https://nodejs.org/api/assert.html){:target="_blank"}
{:.prompt-info}

> [jest 공식문서](https://jestjs.io/){:target="_blank"}
{:.prompt-info}

> [artillery 공식문서](https://www.artillery.io/docs){:target="_blank"}
{:.prompt-info}

> [datadog 공식문서](https://docs.datadoghq.com/){:target="_blank"}
{:.prompt-info}

> [newrelic 공식문서](https://docs.newrelic.com/){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}