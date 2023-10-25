---
layout: post
title: 15. AWS와 GCP로 배포하기
author: admin
date: 2023-05-04 00:00:00 +900
lastmod: 2023-05-04 00:00:00 +900
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

# **🌈 AWS와 GCP로 배포하기**

## **💻 서비스 운영을 위한 패키지**

### **🍳 실 서비스 배포 준비하기**

서비스 개발 시에는 localhost로 결과를 바로 볼 수 있었음

- 혼자만 볼 수 있기에 다른 사람에게 공개하는 과정이 필요

- 9장 NodeBird 앱을 배포해볼 것임

- 배포를 위한 사전 작업 방법에 대해 알아봄

- 서버 실행 관리, 에러 내역 관리, 보안 위협 대처

- AWS와 GCP에 배포

<br/>

### **🍳 morgan**

개발용으로 설정된 익스프레스 미들웨어를 배포용으로 전환

- process.env.NODE_ENV는 배포 환경인지 개발 환경인지를 판단할 수 있는 환경 변수

- 배포 환경일 때는 combined 사용(더 많은 사용자 정보를 로그로 남김)

- NODE_ENV는 뒤에 나오는 cross-env에서 설정해줌

```js
if (process.env.NODE_ENV !== 'production') {
  app.use(morgan('combined'));
} else {
  app.use(morgan('dev'))
}
```

<br/>

### **🍳 express-session**

설정들을 배포용과 개발용으로 분기 처리

- production일 때는 proxy를 true, secure를 true로

- 단, https를 적용할 경우에만 secure를 true로 하고, 노드 앞에 다른 서버를 두었을 때 proxy를 true로 함

```js
const sessionOption = {
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
 };
if (process.env.NODE_ENV === 'production') {
  sessionOption.proxy = true;
  // sessionOption.cookie.secure = true;
}
app.use(session(sessionOption));
```

<br/>

### **🍳 sequelize**

시퀄라이즈 설정도 하드코딩 대신 process.env로 변경

- JSON 파일은 변수를 사용할 수 없으므로 JS 파일을 설정 파일로 써야 함
- config.json을 지우고 config.js 사용

🔻 config/config.js

```js
require('dotenv').config();

// app.enable('trust proxy') 를 추가해주면 좋음

module.exports = {
  development: {
    username: 'root',
    password: process.env.SEQUELIZE_PASSWORD,
    database: 'nodebird',
    host: '127.0.0.1',
    dialect: 'mysql',
  },
  test: {
    username: "root",
    password: process.env.SEQUELIZE_PASSWORD,
    database: "nodebird_test",
    host: "127.0.0.1",
    dialect: "mysql"
  },
  production: {
    username: 'root',
    password: process.env.SEQUELIZE_PASSWORD,
    database: 'nodebird',
    host: '127.0.0.1',
    dialect: 'mysql',
    logging: false,
  },
};
```

🔻 .env

```js
COOKIE_SECRET=nodebirdsecret
KAKAO_ID=5d4daf57becfd72fd9c919882552c4a6
SEQUELIZE_PASSWORD=데이터베이스 비밀번호
```

<br/>

### **🍳 cross-env**

동적으로 process.env 변경 가능

- 운영체제 상관 없이 일괄 적용 가능(맥, 윈도, 리눅스)

- package.json을 다음과 같이 수정(배포용과 개발용 스크립트 구분)

- 문제점: 윈도에서는 NODE_ENV를 위와 같이 설정할 수 없음

- 이 때 cross-env가 필요

```sh
npm i cross-env
```

🔻 package.json

```js
{
  "name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "server.js",
  "scripts": {
    "start": "cross-env NODE_ENV=production PORT=80 node server",
    "dev": "nodemon server",
    "test": "jest"
  }
}
...
```

<br/>

### **🍳 sanitize-html**

XSS(Cross Site Scripting) 공격 방어

```sh
npm i sanitize-html
```

허용하지 않은 html 입력을 막음

아래처럼 빈 문자열로 치환됨

```js
const sanitizeHtml = require('sanitize-html')

const html = "/<script>location.href = 'https://github.com'</script>"
console.log(sanitizeHtml(html)) // ''
```

<br/>

### **🍳 csrf**

CSRF(Cross Site Request Forgery) 공격 방어

```sh
npm i csurf
```

csrfToken을 생성해서 프런트로 보내주고(쿠키로)

Form 등록 시 csrfToken을 같이 받아 일치하는지 비교

```js
const csrf = require('csrf')
const csrfProtection = csrf({cookie:true})

app.get('/form',csrfProtection, (req, res) => {
  res.render('csrf', { csrfToken : req.csrfToken() });
})

app.port('/form', csrfProtection, (req, res) => {
  res.send('ok')
})
```
<br/>

### **🍳 pm2**

원활한 서버 운영을 위한 패키지

- 서버가 에러로 인해 꺼졌을 때 서버를 다시 켜 줌

- 멀티 프로세싱 지원(노드 프로세스 수를 1개 이상으로 늘릴 수 있음)

- 요청을 프로세스들에 고르게 분배

- 단점: 프로세스간 서버의 메모리 같은 자원 공유 불가

- 극복: memcached나 redis같은 메모리 DB 사용(공유 메모리를 별도 DB에 저장)

```sh
npm i pm2
```

package.json 스크립트 수정

🔻 pakage.json

```js
{
  "name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "server.js",
  "scripts": {
    "start": "cross-env NODE_ENV=production PORT=80 pm2 start server.js",
    "dev": "nodemon server",
    "test": "jest"
  },
...
}
```

```sh
npm start
```
> 리눅스나 맥에서 pm2를 실행할 때 1024번 이하의 포트를 사용하려면 권리자의 권한이 필요하다.
>
> 따라서 sudo 명령어를 앞에 붙여 실행해야 한다.
{:.prompt-info}

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4ac51fb7-11f6-468c-9707-acdf121421b5)


<br/>

### **🍳 프로세스 목록 확인하기**

pm2 list로 프로세스 목록 확인 가능

프로세스가 백그라운드로 돌아가기 때문에 콘솔에 다른 명령어 입력 가능

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/711da597-25ec-4d37-bc11-83de3381cf6b)

<br/>

### **🍳 pm2로 멀티 프로세싱하기**

`pm2 start [파일명] –i [프로세스 수]` 명령어로 멀티 프로세싱 가능

프로세스 수에 원하는 프로세스의 수 입력

0이면 CPU 코어 개수만큼 생성, -1이면 CPU 코어 개수보다 1개 적게 생성

-1은 하나의 프로세스를 노드 외의 작업 수행을 위해 풀어주는 것

🔻package.json

```js
{
  "name": "nodebird",
  "version": "0.0.1",
  "description": "익스프레스로 만드는 SNS 서비스",
  "main": "server.js",
  "scripts": {
    "start": "cross-env NODE_ENV=production PORT=80 pm2 start server.js -i 0",
    "dev": "nodemon server",
    "test": "jest"
  },
...
}
```

<br/>

### **🍳 서버 종료 후 멀티 프로세싱 하기**

pm2 kill로 프로세스 전체 종료 가능

```sh
npx pm2 kill && npm start
```

재시작하면 프로세스가 CPU 코어 개수만큼 실행됨

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ec6c25a7-36ba-4a52-9449-1232acb11b37)


```sh
npx pm2 reload all
```

현재 모든 서버들이 재시작

<br/>

### **🍳 프로세스 모니터링하기**

pm2 monit으로 프로세스 모니터링

```sh
npx pm2 monit
```

프로세스별로 로그를 실시간으로 볼 수 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/62e08f4d-9a16-436b-a139-11b8dae9c315)

```sh
npx pm2 logs --err
```
로그로 확인 

<br/>

### **🍳 winston**

console.log와 console.error를 대체하기 위한 모듈

위 두 메서드는 휘발성

로그를 파일에 기록하는 것이 좋음

윈스턴 설치 후 logger.js 작성

```sh
npm i winston
```

🔻 logger.js

```js
const { createLogger, format, transports } = require('winston');

const logger = createLogger({
  level: 'info',
  format: format.json(),
  transports: [
    new transports.File({ filename: 'combined.log' }),
    new transports.File({ filename: 'error.log', level: 'error' }),
  ],
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new transports.Console({ format: format.simple() }));
}

module.exports = logger;

```
<br/>

### **🍳 winston 메서드**


- `level`은 로그의 심각도(error, warn, info, verbose, debug, silly 순, 중요도 순)

- `info`를 고른 경우 info보다 심각한 단계 로그도 같이 기록됨

- `format`은 로그의 형식(json, label, timestamp, printf, combine, simple 등 지원)

- 기본적으로는 JSON으로 기록하지만 로그 시간을 표시하려면 `timestamp`를 쓰는 게 좋음

- `transports`는 로그 저장 방식

- `new transports.File`은 파일로 저장한다는 뜻, `new transports.Console`은 콘솔에 출력한다는 뜻

- 인자로 `filename`(파일명), `level`(심각도) 제공

<br/>

### **🍳 winston 적용하기**

🔻app.js

```js
...

dotenv.config();
const pageRouter = require('./routes/page');
const authRouter = require('./routes/auth');
const postRouter = require('./routes/post');
const userRouter = require('./routes/user');
const { sequelize } = require('./models');
const passportConfig = require('./passport');
const logger = require('./logger');

const app = express();

...

app.use('/', pageRouter);
app.use('/auth', authRouter);
app.use('/post', postRouter);
app.use('/user', userRouter);

app.use((req, res, next) => {
  const error =  new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  logger.info('hello');
  logger.error(error.message);
  next(error);
});

...

```

<br/>

### **🍳 winston 로그 확인하기**

npm run dev로 개발용 서버 실행

http://localhost:8001/abcd 에 접속

각각의 로그가 파일에 기록됨

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f6c11ece-cf89-43cc-977c-b6dafe1a1687)


파일에 로그가 저장되어 관리 가능

`winston-daily-rotate-file`이라는 패키지로 날짜별로 관리 가능

<br/>

### **🍳 helmet, hpp로 보안 관리하기**

모든 취약점을 방어해주진 않지만 실무에서 필수인 패키지

```sh
npm i helmet hpp
```

배포 환경일 때만 사용하면 됨

🔻 app.js

```js
...

const passport = require('passport');
const helmet = require('helmet');
const hpp = require('hpp');

...

if (process.env.NODE_ENV === 'production') {
  app.enable('trust proxy');
  app.use(morgan('combined'));
  app.use(
    helmet({
      contentSecurityPolicy: false,
      crossOriginEmbedderPolicy: false,
      crossOriginResourcePolicy: false,
    }),
  );
  app.use(hpp());
} else {
  app.use(morgan('dev'));
}

...

```

<br/>

### **🍳 connect-redis**

멀티 프로세스간 메모리 공유를 위해 redis 사용

connect-redis가 익스프레스와 레디스를 연결해줌

```sh
npm i redis connect-redis
```

[redislabs](https://app.redislabs.com/#/login){:target="_blank"} 접속 회원가입 후 데이터베이스 생성

<br/>

### **🍳 레디스 호스팅 생성 완료**

Endpoint와 Redis password를 복사해 .env에 붙여 넣기

endpoint에서 host와 port 분리

🔻.env

```js
COOKIE_SECRET=nodebirdsecret
KAKAO_ID=5d4daf57becfd72fd9c919882552c4a6
SEQUELIZE_PASSWORD=nodejsbook
REDIS_HOST=redis-18954.c92.us-east-1-3.ec2.cloud.redislabs.com
REDIS_PORT=18954
REDIS_PASSWORD=JwTwGgKM4P0OFGStgQDgy2AcXvZjX4dc
```

<br/>

### **🍳 connect-redis 연결하기**

app.js express-session 미들웨어 부분에 store 속성 추가

RedisStore 생성자의 인스턴스를 store 속성에 등록

이제 서버를 껐다 켜도 로그인이 유지됨

🔻 app.js

```js
...

const hpp = require('hpp');
const redis = require('redis');
const RedisStore = require('connect-redis')(session);

dotenv.config();
const redisClient = redis.createClient({
  url: `redis://${process.env.REDIS_HOST}:${process.env.REDIS_PORT}`,
  password: process.env.REDIS_PASSWORD,
  legacyMode: true
});

redisClient.connect().catch(console.error);
const pageRouter = require('./routes/page')

...

const sessionOption = {
  resave: false,
  saveUninitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false,
  },
  store: new RedisStore({ client: redisClient }),
};

...
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8f65d622-37e8-4da5-b2ab-8e32482ce39c)

<br/>

### **🍳 nvm, n**

노드 버전을 업데이트하기 위한 패키지

윈도에서는 nvm-installer, 리눅스나 맥에서는 n 패키지

<br/>

### **🍳 nvm-installer**

[링크참고](https://github.com/coreybutler/nvm-windows/releases){:target="_blank"}

nvm-setup.zip 내려받고 압축 해제 후 실행

nvm list로 노드 버전 확인, nvm instal로 버전 설치(nvm install latest로 최신 버전 설치)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9df3b423-7ce9-454a-a2ea-e66c4deb5e76)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c4a005a4-0321-48b3-aae3-11e0925c26e1)


<br/>

### **🍳 nvm-installer로 노드 버전 바꾸기**

nvm use [버전명]으로 사용하고픈 버전 입력

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/47953423-8e4c-43d6-9b3b-ed8b234360f4)

<br/>

### **🍳 맥, 리눅스에서 n으로 노드 업데이트**

n을 전역 설치

```sh
sudo npm i –g n
```

n 명령어로 현재 노드 버전 확인

n 버전으로 새 버전 설치

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1506469c-b9c2-4cc0-824b-f40b3508be8c)

<br/>

---

<br/>

## **💻 깃과 깃허브 사용하기**

### **🍳 Git으로 소스코드 관리하기**

소스 코드가 수정될 때마다 직접 업로드하는 게 성가심

협업할 때도 서로 코드가 달라서 충돌이 발생하는 경우가 발생함

Git이라는 분산형 버전 관리 시스템을 많이 사용

GitHub는 Git으로부터 업로드한 소스 코드를 서버에 저장할 수 있는 원격 저장소

여러 사람이 코드를 공동 관리할 수 있음

<br/>

### **🍳 Git 설치하기 생략**

<br/>

### **🍳 git 명령어 사용하기**

콘솔에서 git --version 입력, 제대로 나오면 설치 성공

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4e79491d-24f9-4550-8c98-09af67596cfc)


.gitignore에 git으로 소스 관리하지 않을 파일과 디렉터리 등록

🔻 .gitignore
```js
node_modules
uploads
*.log
coverage
.env
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4884f868-ad2c-4548-8655-4e38c3a05b1b)

<br/>

### **🍳 .github 사용방법 생략**

### **🍳 프로젝트에 git 설정하기**

nodebird 프로젝트로 이동 후 git init 명령어 입력

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7039663f-a903-499a-ac6c-6c39adbf70ac)

<br/>

### **🍳 모든 파일 git에 추가하기**

.gitignore에 적힌 것을 제외한 모든 파일이 git 관리 대상이 됨

점(.)은 모든 파일을 의미함

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/d68ce5e8-ed9c-4f0a-92e6-f60649f00ba5)

<br/>

### **🍳 소스 코드 커밋하기**

사용자 정보를 설정한 후 소스 코드 커밋(상태 저장)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/bf3be20f-7685-4d6f-93d5-811f10cd6fef)

<br/>

### **🍳 GitHub 주소 등록하기**

아이디와 비밀번호 부분을 자신의 것으로 대체

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2b394597-7acf-4715-a495-8a9e95708f98)


<br/>

### **🍳 GitHub에 코드 올리기**

git push origin master 입력

소스 코드가 깃허브에 올라간 것을 확인할 수 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6c9ba8c9-72cc-4cd0-98f7-cb12332a6104)

<br/>

---

<br/>

## **💻 AWS 시작하기**

### **🍳 AWS 이용하기**

[aws.amazon.com/ko](https://aws.amazon.com/ko/){:target="_blank"}에 접속해서 회원가입

카드 정보 등의 결제 정보도 입력해야 한다.

소액 결제가 이루어질 수 있는데 본인 확인 이후에 환불을 해준다고 한다.

<br/>

### **🍳 AWS 설치과정 생략**

<br/>

### **🍳 Lightsail 선택**

배포서버는 서울로 설정

Lightsail 검색하면 편함

간단하게 노드 서비스를 배포할 수 있는 Lightsail 선택

<br/>

### **🍳 Lightsail 인스턴스 생성**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ebaf17be-46b9-4ecf-bebf-b17443c993d0)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/61285d2c-e6c8-435b-ab95-f5f3472dbc0e)

<br/>

### **🍳 인스턴스 계획 선택**

3 개월은 무료이므로 인스턴스 선택, 나중에 제거해야 과금되지 않음

<br/>

### **🍳 인스턴스 생성 확인하기**

생성된 인스턴스 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f590dbbd-05f7-4097-b248-f56395a40299)


<br/>

### **🍳 인스턴스 삭제 방법**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2dc47734-e922-4252-92e7-cfbcdaec449a)

<br/>

---

<br/>

## **💻 AWS에 배포하기**

### **🍳 SSH 연결하기**

SSH를 사용하여 연결 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/fd4d7252-92cd-4728-8098-7915fa7a743c)

<br/>

### **🍳 LightSail 콘솔 열기**

콘솔 명령어로 명령 실행

실무에서는 RDS를 사용한다.

```sh
sudo apt-get update
sudo apt-get intall -y gnupg
sudo wget https //dev.mysql.com/get/mysql-apt-config_0.8.23-1_all.deb
sudo dpkg -i mysql-apt-config_0.8.23-1_all.deb # dkpg -i 이후 탭누르면 자동완성 
```

```sh
sudo apt-get update
sudo apt-get install -y mysql-server
```

```sh
sudo mysql -uroot -p
(비밀번호 입력)
(MySQL 프롬프트 접속)
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '비밀번호';
exit;
```

<br/>

### **🍳 GitHub에서 소스 내려받기**

git clone 명령어로 소스 내려받기

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/3ea0c8bd-0404-4497-b69d-e1b02437fa95)

공개 레포지토리는 비밀번호가 필요하지 않다.

비밀번호가 필요한 경우는 토큰을 발급받아야 한다.

<br/>

### **🍳 ightSail 서버 실행하기**

#### **기존의 아파치 서버 종료**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e7896892-ce4d-4658-9673-40cc3a48ed96)

#### **노드 프로젝트 실행**

npm ci 로 설치해야 좋음

```sh
cd 경로 이동
npm ci
npx sequelize db:create --env production
sudo npm i -g pm2
```

#### **.env 생성**

```sh
vim .env
```

`a` 를 누르면 입력 모드

입력하고 esc -> `:wq`

```sh
sudo NODE_ENV=production PORT=80 pm2 start server.js -i 0
```

```sh
sudo pm2 logs --err
```
서버가 실행되지 않으면 위의 명령어를 통해 어떤 에러인지 확인 후 해결

```sh
sudo pm2 reload all
```
해결 후 위 명령어로 서버 재시작

<br/>

### **🍳 LightSail 서버 접속하기**

퍼블릭 IP 확인 후 브라우저에 입력해 접속(예제에서는 13.209.84.110)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/038834e2-21b8-4550-badf-d3d9c743eb1f)


<br/>

---

<br/>

## **💻 GCP 시작하기**

### **🍳 GCP 콘솔 접속하기**

[https://console.cloud.google.com](https://console.cloud.google.com){:target="_blank"}에 접속(구글 로그인 필요)

서비스 약관 동의

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ee7da575-9890-4bf5-9307-f6bba0fb22d6)

<br/>

### **🍳 새 프로젝트 생성하기**

프로젝트 이름을 node-deploy로 이름 설정, 만들기 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4d199d78-db69-4515-b96e-28be1839061b)

<br/>

### **🍳 GCP 계정 등록하기**

정보를 입력하고 계속 버튼으로 진행

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8a775f84-491c-40f5-8cae-c0b26d9c3187)

<br/>

### **🍳 무료 평가판 시작하기**

300$ 어치의 무료 크레딧 제공

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7c572482-1d8c-4df1-9c77-26ca86860ccf)

<br/>

### **🍳 결제 사용 설정하기**

VM 인스턴스 화면에서 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8a45c430-0bbc-4e51-ac5c-851e27abf717)

<br/>

### **🍳 VM 인스턴스 만들기**

VM 인스턴스 화면에서 기다리다 보면 VM 인스턴스가 활성화 됨

만들기 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/cb554a14-60d0-4c64-aa78-d4178f5d64da)

머신 유형을 초소형으로 변경

- 영역은 us-east1, us-central-1, us-west1만 무료

- 운영체제는 Ubuntu 18.04LTS, e2-micro

- 방화벽은 http와 https 트래픽 둘 다 허용

- 만들기 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1961a1a3-616f-4cc8-8e20-0dad02adca33)

<br/>

### **🍳 VM 인스턴스 확인하기**

인스턴스 생성 후 조금 기다리면 인스턴스가 준비됨

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/0cd7ceb7-32fb-4f98-863d-d8eb27346477)


<br/>

---

<br/>

## **💻 GCP에 배포하기**

### **🍳 SSH 실행하기**

SSH 버튼을 눌러 SSH 실행

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/43c29d37-d817-4e53-a6ae-458bd44a77e1)

<br/>

### **🍳 배포를 위한 프로그램 설치하기**

SSH에 명령어 입력

```sh
sudo su
```

git clone 명령어로 소스 내려받기

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f8b14403-3d80-4908-a54c-01d50cd51a38)


```sh
cd 경로 이동
npm ci
npx sequelize db:create --env production
sudo npm i -g pm2
sudo NODE_ENV=production PORT=80 pm2 start server.js -i 0
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/196692ea-e2c1-41a7-b05e-bbfe20345eb0)

<br/>

### **🍳 서버 실행하기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c2809ac9-1fa9-4911-b9b1-2cde8061f448)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/150b0e02-a56a-428c-9e6d-258100402edb)


<br/>

### **🍳  외부 IP로 접속하기**

외부 IP 확인 후 브라우저에 입력해 접속

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6b68e275-d9ab-49a9-a502-300e46f1e605)

<br/>

### **🍳 추가로 알아둘 점**

https로 접속하고 싶다면 인증서를 발급받아야 함

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/255f07c5-cfb9-4cf1-8b72-c6a369f41801)

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

> [cross-env](https://www.npmjs.com/package/cross-env){:target="_blank"}
{:.prompt-info}

> [pm2](https://pm2.keymetrics.io/){:target="_blank"}
{:.prompt-info}

> [sanitize-html](https://www.npmjs.com/package/sanitize-html){:target="_blank"}
{:.prompt-info}

> [csurf](https://www.npmjs.com/package/csurf){:target="_blank"}
{:.prompt-info}

> [helmet](https://www.npmjs.com/package/helmet){:target="_blank"}
{:.prompt-info}

> [hpp](https://www.npmjs.com/package/hpp){:target="_blank"}
{:.prompt-info}

> [redis](https://redis.io/){:target="_blank"}
{:.prompt-info}

> [connect-redis](https://www.npmjs.com/package/connect-redis){:target="_blank"}
{:.prompt-info}

> [rate-limit-redis](https://www.npmjs.com/package/rate-limit-redis){:target="_blank"}
{:.prompt-info}

> [n](https://www.npmjs.com/package/n){:target="_blank"}
{:.prompt-info}

> [nvm](https://github.com/coreybutler/nvm-windows){:target="_blank"}
{:.prompt-info}

> [깃 설명](){:target="_blank"}
{:.prompt-info}

> [GCP](https://cloud.google.com/?hl=ko){:target="_blank"}
{:.prompt-info}

> [AWS](https://aws.amazon.com/ko/){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}