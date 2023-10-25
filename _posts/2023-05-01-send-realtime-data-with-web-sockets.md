---
layout: post
title: 12. 웹 소켓으로 실시간 데이터 전송하기
author: admin
date: 2023-05-01 00:00:00 +900
lastmod: 2023-05-01 00:00:00 +900
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

# **🌈 웹 소켓으로 실시간 데이터 전송하기**

## **💻 웹 소켓 이해하기**

### **🍳 웹 소켓 이해하기**

**웹 소켓: 실시간 양방향 데이터 전송을 위한 기술**

- ws 프로토콜 사용 -> 브라우저가 지원해야 함

- 최신 브라우저는 대부분 웹 소켓을 지원함

- 노드는 ws나 Socket.IO같은 패키지를 통해 웹 소켓 사용 가능

**웹 소켓 이전에는 폴링이라는 방식을 사용했음**

- HTTP가 클라이언트에서 서버로만 요청이 가기 때문에 주기적으로 서버에 요청을 보내 업데이트가 있는지 확인함

- 웹 소켓은 연결도 한 번만 맺으면 되고, HTTP와 포트 공유도 가능하며, 성능도 매우 좋음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7223b6f6-741d-45c4-9d1b-8bf98b677bfd)


<br/>

### **🍳 서버센트 이벤트 SSE(Server Sent Events)**

- EventSource라는 객체를 사용

- 처음에 한 번만 연결하면 서버가 클라이언트에 지속적으로 데이터를 보내줌

- 클라이언트에서 서버로는 데이터를 보낼 수 없음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5bf677e2-df7d-44e2-82e8-deb3e0a3e3a6)

<br/>

---

<br/>

## **💻 ws 모듈로 웹 소켓 사용하기**

### **🍳 기본 파일 작성**

패키지를 설치하고 .env와 app.js, routes/index.js 파일 작성

```sh
 npm i cookie-parser dotenv express express-session morgan nunjucks && npm i -D nodemon
```
```sh
npm i ws
```

🔻 .env

```js
COOKIE_SECRET=gifchat
```

웹 소켓을 익스프레스에 연결하기

🔻 app.js

```js
const express = require('express');
const path = require('path');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();

const webSocket = require('./socket');
const indexRouter = require('./routes');

app = express();

app.set('port', process.env.POST || 8005);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
  resave: false,
  saveUnitialized: false,
  secret: process.env.COOKIE_SECRET,
  cookie: {
    httpOnly: true,
    secure: false
  }
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

const server = app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 보트에서 대기중');
});


webSocket(server);
```

🔻 socket.js 

```js
const WepSocket = require('ws');

module.exports = (server) => {
  const wss = new WepSocket.Server({ server });

  wss.on('connection', (ws, req) => {
    const ip = req.headers['x-forwarded-for'] || req.socket.remoteAddress;
    console.log('새로운 클라이언트 접속 ', ip);

    ws.on('message', (message) => {
      console.log(message.toString());
    });
    ws.on('error', console.error);
    ws.on('close', () => {
      console.log('클라이언트 접속 해제 ', ip);
      clearInterval(ws.interval);
    });
    ws.interval = setInterval(() => {
      if (ws.readyState === ws.OPEN) {
        ws.send('서버에서 클라이언트로 메세지를 보냅니다.');
      }
    }, 3000);
  });
};
```

ws 모듈을 불러옴

- `new Websocket.Server({ server })` 로 익스프레스 서버와 연결

- `connection` 이벤트는 서버와 연결될 때 실행되는 이벤트

- `req.headers[‘x-forwarded-for’] || req.connection.remoteAddress`는 클라이언트의 IP를 알아내는 유명한 방법

- `message`, `error`, `close` 이벤트는 각각 메시지가 올 때, 에러 발생할 때, 서버 연결 종료할 때 호출

- `ws.OPEN`은 연결 상태가 열려있다는 뜻(연결되었다는 뜻)

- `ws.send`로 메시지 전송(3초마다 보내고 있음)

<br/>

### **🍳 프런트엔드에서 메시지 답장하기**

index.html를 작성하고 스크립트 작성

- `new WebSocket은` 최신 브라우저에서 지원

- 인수로 서버의 주소를 입력

- `onopen` 이벤트리스너는 서버와 연결되었을 때 호출

- `onmessage` 이벤트리스너는 서버에서 메시지가 올 때 호출

- `event.data`에 서버 메시지 내용이 들어 있음

- `webSocket.send`로 서버로 메시지 전달 가능

🔻views/index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>GIF 채팅방</title>
  </head>
  <body>
    <div>F12를 눌러 console 탭과 network 탭을 확인하세요.</div>
    <script>
      const webSocket = new WebSocket('ws://localhost:8005');
      webSocket.onopen = function () {
        console.log('서버와 웹소켓 연결 성공!');
      };
      webSocket.onmessage = function (event) {
        console.log(event.data);
        webSocket.send('클라이언트에서 서버로 답장을 보냅니다');
      };
    </script>
  </body>
</html>
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7ac3978f-67d9-4a13-81c0-ea4cef052d1e)


<br/>

### **🍳 서버 실행하기**

서버 실행 후 [http://localhost:8005 접속](http://localhost:8005){:target="_blank"}

F12 console 탭 실행

접속하는 순간부터 노드의 콘솔과 브라우저의 콘솔에 3초마다 메시지 찍힘

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/bab6733a-2120-4e13-929c-737896d0987b)


![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ea23e972-ea26-4567-8aaf-09911d0e61bc)

<br/>

### **🍳 Network 요청 확인하기**

개발자 도구의 Network 탭 열기

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/24ff3aa5-bc70-4f9a-9cb0-67f4d47a2078)

<br/>

### **🍳 다른 브라우저로도 연결하기**

다른 브라우저에서 [http://localhost:8005](http://localhost:8005){:target="_blank"}에 접속

접속한 브라우저(클라이언트)가 두 개라, 서버가 받는 메시지의 양도 두 배가 됨


<br/>

---

<br/>


## **💻 Socket.IO 사용하기**

### **🍳 Socket.IO 설치하기**

```sh
npm i socket.io
```

- ws 패키지 대신 Socket.IO 연결

- Socket.IO 패키지를 불러와 익스프레스 서버와 연결. 두 번째 인수는 클라이언트와 연결할 수 있는 경로(/socket.io)

- `connection` 이벤트는 서버와 연결되었을 때 호출, 콜백으로 소켓 객체(socket) 제공

- `socket.request`로 요청 객체에 접근 가능, socket.id로 소켓 고유 아이디 확인 가능

- `disconnect` 이벤트는 연결 종료 시 호출, error는 에러 발생 시 호출

- `reply`는 사용자가 직접 만들 이벤트로 클라이언트에서 reply 이벤트 발생 시 서버에 전달됨

- `socket.emit`으로 메시지 전달. 첫 번째 인수는 이벤트 명, 두 번째 인수가 메시지

🔻socket.js

```js
const SocketIO = require('socket.io');

module.exports = (server) => {
  const io = SocketIO(server, { path: '/socket.io' });

  io.on('connection', (socket) => { // 웹소켓 연결 시
    const req = socket.request;
    const ip = req.headers['x-forwarded-for'] || req.socket.remoteAddress;
    console.log('새로운 클라이언트 접속!', ip, socket.id, req.ip);
    socket.on('disconnect', () => { // 연결 종료 시
      console.log('클라이언트 접속 해제', ip, socket.id);
      clearInterval(socket.interval);
    });
    socket.on('error', (error) => { // 에러 시
      console.error(error);
    });
    socket.on('reply', (data) => { // 클라이언트로부터 메시지
      console.log(data);
    });
    socket.interval = setInterval(() => { // 3초마다 클라이언트로 메시지 전송
      socket.emit('news', 'Hello Socket.IO'); // emit('키', '값')
    }, 3000);
  });
};
```

<br/>

### **🍳 클라이언트에서 메시지 주고 받기**

index.html 수정

- `/socket.io/socket.io.js` 스크립트를 넣어주어야 함(io 객체 제공)

- connect 메서드로 서버 주소로 연결하고 서버의 설정과 같은 path 입력(/socket.io)

- 서버 주소가 `http 프로토콜`임에 유의

- news 이벤트 리스너로 서버에서 오는 news 이벤트 대기

- socket.emit(‘reply’, 메시지)로 reply 이벤트 발생

🔻views/index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>GIF 채팅방</title>
  </head>
  <body>
    <div>F12를 눌러 console 탭과 network 탭을 확인하세요.</div>
    <script src="/socket.io/socket.io.js"></script>
    <script>
      const socket = io.connect('http://localhost:8005', {
        path: '/socket.io',
        transports: ['websocket'], // 웹소켓만 사용하고 싶을 때
      });
      socket.on('news', function (data) {
        console.log(data);
        socket.emit('reply', 'Hello Node.JS');
      });
    </script>
  </body>
</html>
```

<br/>

### **🍳 서버 실행하기**

서버 실행 후 [http://localhost:8005](http://localhost:8005){:target="_blank"}에 접속

개발자 도구 Network 탭을 보면 웹소켓과 폴링 연결 둘 다 있음 확인 가능

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/29d554d7-af2d-45c2-9945-dbf5fdfe1320)


- Socket.IO는 먼저 폴링 방식으로 연결 후(웹 소켓을 지원하지 않는 브라우저를 위해), 웹 소켓을 사용할 수 있다면 웹 소켓으로 업그레이드

- 웹 소켓만 사용하고 싶다면 `transports` 옵션을 다음과 같이 주면 됨

```html
 transports: ['websocket'],
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4f200354-95a5-452f-ba12-dfc78d4306cc)

<br/>

---

<br/>


## **💻 실시간 GIF 채팅방 만들기**

### **🍳 프로젝트 구조 갖추기**

필요 패키지 설치 후 스키마 작성

```sh
npm i mongoose multer axios color-hash@2
```

- color-hash는 익명 닉네임에 컬러를 줄 때 사용

<br/>

### **🍳 스키마 생성하기**

🔻schemas/room.js

```js
const mongoose = requrie('mongoose');

const { Schema } = mongoose;

const roomSchema = new Schema({
  title: {
    type: String,
    required: true,
  },
  max: {
    tpye: Number,
    required: true,
    default: 10,
    min: 2,
  },
  owner: {
    type: String,
    required: true,
  },
  password: String,
  createdAt: {
    type: Date,
    default: Date.now()
  }
});

module.exports = mongoose.model('Room', roomSchema);
```

🔻schemas/chat.js

```js
const mongoose = require('mongoose');

const { Schema } = mongoose;

const chatSchema = new Schema({
  room: {
    type: ObjectId,
    required: true,
    ref: 'Room'
  },
  user: {
    type: String,
    required: true
  },
  chat: String,
  gif: String,
  createdAt: {
    type: Date,
    default: Date.now()
  }
});

module.exports = mongoose.model('Chat', chatSchema);
```

<br/>

### **🍳 스키마 연결하기**

스키마를 index.js와 연결

- 익스프레스와 몽구스를 연결

- .env 파일에 비밀키 입력

🔻.env

```js
COOKIE_SECRET=gifchat
MONGO_ID=root
MONGO_PASSWORD=nodejsbook
```

<br/>

🔻schemas/index.js

```js
const mongoose = require('mongoose');

const { MONGO_ID, MONGO_PASSWORD, NODE_ENV } = process.env;
const MONGO_URL = `mongodb://${MONGO_ID}:${MONGO_PASSWORD}@localhost:27017/admin`;

const connect = () => {
  if (NODE_ENV !== 'production') {
    mongoose.set('debug', true);
  }
  mongoose.connect(MONGO_URL, {
    dbName: 'gifchat',
    useNewUrlParser: true,
  }).then(() => {
    console.log("몽고디비 연결 성공");
  }).catch((err) => {
    console.error("몽고디비 연결 에러", err);
  });
};

mongoose.connection.on('error', (error) => {
  console.error('몽고디비 연결 에러', error);
});
mongoose.connection.on('disconnected', () => {
  console.error('몽고디비 연결이 끊겼습니다. 연결을 재시도합니다.');
  connect();
});

module.exports = connect;
```

<br/>

🔻app.js

```js
const express = require('express');
const path = require('path');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const ColorHash = require('color-hash').default;

dotenv.config();
const webSocket = require('./socket');
const indexRouter = require('./routes');
const connect = require('./schemas');

const app = express();
app.set('port', process.env.PORT || 8005);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});
connect();
```

<br/>

### **🍳 프런트엔드 파일 작성**

[소스코드 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch12/12.4/gif-chat){:target="_blank"}

- views/layout.html, public/main.css, views/main.html, views/room.html, views/chat.html 참고

- main.html의 코드에서 io.connect의 주소가 달라졌다는 점에 주목

- 주소의 /room은 네임스페이스(같은 네임스페이스끼리만 데이터 전달 가능)

- socket에는 newRoom(새 방 생성 시 목록에 방 추가 이벤트)과 removeRoom(방 폭파 시 목록에서 방 제거 이벤트) 이벤트 연결

- chat.html에서는 /chat 네임스페이스에 연결

- join 이벤트(방에 참가할 때 들어왔다는 시스템 메시지 등록)와 exit 이벤트(방에서 나갈 때 나갔다는 시스템 메시지 등록) 연결

<br/>

### **🍳 socket.js에 소켓 이벤트 연결**

socket.js 수정


- app.set(‘io’, io);로 라우터에서 io 객체를 쓸 수 있게 저장(req.app.get(‘io’)로 접근 가능)

- io.of는 네임스페이스에 접근하는 메서드

- 각각의 네임스페이스에 이벤트를 따로 걸어줄 수 있음

- req.headers.referer에 요청 주소가 들어 있음

- 요청 주소에서 방 아이디를 추출하여 socket.join으로 방 입장

- socket.leave로 방에서 나갈 수 있음

- socket.join과 leave는 Socket.IO에서 준비해둔 메서드

🔻socket.js

```js
const SocketIO = require('socket.io');

module.exports = (server, app) => {
  const io = SocketIO(server, { path: '/socket.io' });
  app.set('io', io);
  const room = io.of('/room');
  const chat = io.of('/chat');

  room.on('connection', (socket) => {
    console.log('room 네임스페이스에 접속');
    socket.on('disconnect', () => {
      console.log('room 네임스페이스 접속 해제');
    });
  });

  chat.on('connection', (socket) => {
    console.log('chat 네임스페이스에 접속');

    socket.on('join', (data) => { // data는 브라우저에서 보낸 방 아이디
      socket.join(data); // 네임스페이스 아래 존재하는 방에 접속
    });

    socket.on('disconnect', () => {
      console.log('chat 네임스페이스 접속 해제');
      // socket.leave(roomId);
    });
  });
};
```

<br/>

### **🍳 방 개념 이해하기**

Socket.IO에서는 io 객체 아래에 네임스페이스와 방이 있음

- 기본 네임스페이스는 /

- 방은 네임스페이스의 하위 개념

- 같은 네임스페이스, 같은 방 안에서만 소통할 수 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/67b7d430-c416-4a56-a014-9d0bf4e7f304)

<br/>

### **🍳 color-hash 적용하기**

익명 채팅이므로 방문자에게 고유 컬러 아이디 부여

- 세션에 컬러 아이디 저장(req.session.color)

🔻 app.js

```js
const express = require('express');
const path = require('path');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const ColorHash = require('color-hash').default;

dotenv.config();
const webSocket = require('./socket');
const indexRouter = require('./routes');
const connect = require('./schemas');

const app = express();
app.set('port', process.env.PORT || 8005);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});
connect();

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

app.use((req, res, next) => {
  if (!req.session.color) {
    const colorHash = new ColorHash();
    req.session.color = colorHash.hex(req.sessionID);
    console.log(req.session.color, req.sessionID);
  }
  next();
});

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

const server = app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});

webSocket(server, app);
```

<br/>

### **🍳 라우트와 컨트롤러 만들기**

🔻 routes/index.js

```js
const express = require('express');
const { renderMain, renderRoom, createRoom, enterRoom, removeRoom } = require('../controllers');

const router = express.Router();

router.get('/', renderMain);

router.get('/room', renderRoom);

router.post('/room', createRoom);

router.get('/room/:id', enterRoom);

router.delete('/room/:id', removeRoom);

module.exports = router;
```

<br/>

🔻 controllers/index.js

```js
const Room = require('../schemas/room');
const Chat = require('../schemas/chat');

exports.renderMain = async (req, res, next) => {
  try {
    const rooms = await Room.find({});
    res.render('main', { rooms, title: 'GIF 채팅방' });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderRoom = (req, res) => {
  res.render('room', { title: 'GIF 채팅방 생성' });
};

exports.createRoom = async (req, res, next) => {
  try {
    const newRoom = await Room.create({
      title: req.body.title,
      max: req.body.max,
      owner: req.session.color,
      password: req.body.password,
    });
    const io = req.app.get('io');
    io.of('/room').emit('newRoom', newRoom);
    if (req.body.password) { // 비밀번호가 있는 방이면
      res.redirect(`/room/${newRoom._id}?password=${req.body.password}`);
    } else {
      res.redirect(`/room/${newRoom._id}`);
    }
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.enterRoom = async (req, res, next) => {
  try {
    const room = await Room.findOne({ _id: req.params.id });
    if (!room) {
      return res.redirect('/?error=존재하지 않는 방입니다.');
    }
    if (room.password && room.password !== req.query.password) {
      return res.redirect('/?error=비밀번호가 틀렸습니다.');
    }
    const io = req.app.get('io');
    const { rooms } = io.of('/chat').adapter;
    console.log(rooms, rooms.get(req.params.id), rooms.get(req.params.id));
    if (room.max <= rooms.get(req.params.id)?.size) {
      return res.redirect('/?error=허용 인원이 초과하였습니다.');
    }
    return res.render('chat', {
      room,
      title: room.title,
      chats: [],
      user: req.session.color,
    });
  } catch (error) {
    console.error(error);
    return next(error);
  }
};

exports.removeRoom = async (req, res, next) => {
  try {
    await Room.deleteOne({ _id: req.params.id });
    await Chat.deleteMany({ room: req.params.id });
    res.send('ok');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

<br/>

---

<br/>


## **💻 미들웨어와 소켓 연결하기**

### **🍳 socket.io에서 세션 사용하기**

app.js 수정 후 Socket.IO 미들웨어로 연결

io.use로 익스프레스 미들웨어를 Socket.io에서 사용 가능

🔻app.js

```js
const express = require('express');
const path = require('path');
const morgan = require('morgan');
const cookieParser = require('cookie-parser');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const ColorHash = require('color-hash').default;

dotenv.config();
const webSocket = require('./socket');
const indexRouter = require('./routes');
const connect = require('./schemas');

const app = express();
app.set('port', process.env.PORT || 8005);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});
connect();

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
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(sessionMiddleware);

app.use((req, res, next) => {
  if (!req.session.color) {
    const colorHash = new ColorHash();
    req.session.color = colorHash.hex(req.sessionID);
    console.log(req.session.color, req.sessionID);
  }
  next();
});

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

const server = app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});

webSocket(server, app, sessionMiddleware);
```

<br/>

🔻socket.js

```js
const SocketIO = require('socket.io');
const { removeRoom } = require('./services');

module.exports = (server, app, sessionMiddleware) => {
  const io = SocketIO(server, { path: '/socket.io' });
  app.set('io', io);
  const room = io.of('/room');
  const chat = io.of('/chat');

  const wrap = middleware => (socket, next) => middleware(socket.request, {}, next);
  chat.use(wrap(sessionMiddleware));

  room.on('connection', (socket) => {
    console.log('room 네임스페이스에 접속');
    socket.on('disconnect', () => {
      console.log('room 네임스페이스 접속 해제');
    });
  });

  chat.on('connection', (socket) => {
    console.log('chat 네임스페이스에 접속');

    socket.on('join', (data) => {
      socket.join(data);
      socket.to(data).emit('join', {
        user: 'system',
        chat: `${socket.request.session.color}님이 입장하셨습니다.`,
      });
    });

    socket.on('disconnect', async () => {
      console.log('chat 네임스페이스 접속 해제');
      const { referer } = socket.request.headers; // 브라우저 주소가 들어있음
      const roomId = new URL(referer).pathname.split('/').at(-1);
      const currentRoom = chat.adapter.rooms.get(roomId);
      const userCount = currentRoom?.size || 0;
      if (userCount === 0) { // 유저가 0명이면 방 삭제
        await removeRoom(roomId); // 컨트롤러 대신 서비스를 사용
        room.emit('removeRoom', roomId);
        console.log('방 제거 요청 성공');
      } else {
        socket.to(roomId).emit('exit', {
          user: 'system',
          chat: `${socket.request.session.color}님이 퇴장하셨습니다.`,
        });
      }
    });
  });
};
```


<br/>

### **🍳 방 입장, 퇴장 메시지 전송하기**

to(방아이디).emit(이벤트, 메시지)로 특정 방에 데이터 전송

- 사용자가 0명이면 방 폭파 기능도 추가

- socket.adapter.rooms[방아이디]에 방에 들어있는 소켓 아이디 목록이 나옴

- .length로 방 인원 파악 가능(정확하지는 않음)

- 방 폭파 기능은 서비스로 따로 구현

- 컨트롤러가 아니라 서비스를 사용하는 이유는 웹소캣에는 req, res, next가 없기 때문

- 재사용이 용이하게 핵심 로직만 분리 

🔻 services/index.js

```js
const Room = require('../schemas/room');
const Chat = require('../schemas/chat');

exports.removeRoom = async (roomId) => {
  try {
    await Room.deleteOne({ _id: roomId });
    await Chat.deleteMany({ room: roomId });
  } catch (error) {
    throw error;
  }
};
```

🔻 controllers/index.js

```js
const Room = require('../schemas/room');
const Chat = require('../schemas/chat');
const { removeRoom: removeRoomService } = require('../services');

exports.renderMain = async (req, res, next) => {
  try {
    const rooms = await Room.find({});
    res.render('main', { rooms, title: 'GIF 채팅방' });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderRoom = (req, res) => {
  res.render('room', { title: 'GIF 채팅방 생성' });
};

exports.createRoom = async (req, res, next) => {
  try {
    const newRoom = await Room.create({
      title: req.body.title,
      max: req.body.max,
      owner: req.session.color,
      password: req.body.password,
    });
    const io = req.app.get('io');
    io.of('/room').emit('newRoom', newRoom);
    if (req.body.password) { // 비밀번호가 있는 방이면
      res.redirect(`/room/${newRoom._id}?password=${req.body.password}`);
    } else {
      res.redirect(`/room/${newRoom._id}`);
    }
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.enterRoom = async (req, res, next) => {
  try {
    const room = await Room.findOne({ _id: req.params.id });
    if (!room) {
      return res.redirect('/?error=존재하지 않는 방입니다.');
    }
    if (room.password && room.password !== req.query.password) {
      return res.redirect('/?error=비밀번호가 틀렸습니다.');
    }
    const io = req.app.get('io');
    const { rooms } = io.of('/chat').adapter;
    console.log(rooms, rooms.get(req.params.id), rooms.get(req.params.id));
    if (room.max <= rooms.get(req.params.id)?.size) {
      return res.redirect('/?error=허용 인원이 초과하였습니다.');
    }
    return res.render('chat', {
      room,
      title: room.title,
      chats: [],
      user: req.session.color,
    });
  } catch (error) {
    console.error(error);
    return next(error);
  }
};

exports.removeRoom = async (req, res, next) => {
  try {
    await removeRoomService(req.params.id);
    res.send('ok');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

<br/>

---

<br/>

## **💻 채팅 구현하기**

### **🍳 채팅 소켓 이벤트 리스너 붙이기**

chat 이벤트 리스너를 추가함. 채팅 메시지가 웹 소켓으로 전송될 때 호출됨

event.data.user(채팅 발송자)에 따라 다르게 렌더링

🔻views/chat.html

[html 코드 참고](https://github.com/ZeroCho/nodejs-book/blob/master/ch12/12.6/gif-chat/views/chat.html){:target="_blank"}

<br/>

### **🍳 방에 접속하는 라우터 만들기**

접속 가능한 경우 채팅을 불러와 렌더링

🔻 controllers/index.js

```js
const Room = require('../schemas/room');
const Chat = require('../schemas/chat');
const { removeRoom: removeRoomService } = require('../services'); 

exports.renderMain = async (req, res, next) => {
  try {
    const rooms = await Room.find({});
    res.render('main', { rooms, title: 'GIF 채팅방' });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderRoom = (req, res) => {
  res.render('room', { title: 'GIF 채팅방 생성' });
};

exports.createRoom = async (req, res, next) => {
  try {
    const newRoom = await Room.create({
      title: req.body.title,
      max: req.body.max,
      owner: req.session.color,
      password: req.body.password,
    });
    const io = req.app.get('io');
    io.of('/room').emit('newRoom', newRoom);
    if (req.body.password) { // 비밀번호가 있는 방이면
      res.redirect(`/room/${newRoom._id}?password=${req.body.password}`);
    } else {
      res.redirect(`/room/${newRoom._id}`);
    }
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.enterRoom = async (req, res, next) => {
  try {
    const room = await Room.findOne({ _id: req.params.id });
    if (!room) {
      return res.redirect('/?error=존재하지 않는 방입니다.');
    }
    if (room.password && room.password !== req.query.password) {
      return res.redirect('/?error=비밀번호가 틀렸습니다.');
    }
    const io = req.app.get('io');
    const { rooms } = io.of('/chat').adapter;
    console.log(rooms, rooms.get(req.params.id), rooms.get(req.params.id));
    if (room.max <= rooms.get(req.params.id)?.size) {
      return res.redirect('/?error=허용 인원이 초과하였습니다.');
    }
    const chats = await Chat.find({ room: room._id }).sort('createdAt');
    return res.render('chat', {
      room,
      title: room.title,
      chats,
      user: req.session.color,
    });
  } catch (error) {
    console.error(error);
    return next(error);
  }
};

exports.removeRoom = async (req, res, next) => {
  try {
    await removeRoomService(req.params.id);
    res.send('ok');
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.sendChat = async (req, res, next) => {
  try {
    const chat = await Chat.create({
      room: req.params.id,
      user: req.session.color,
      chat: req.body.chat,
    });
    req.app.get('io').of('/chat').to(req.params.id).emit('chat', chat);
    res.send('ok');
  } catch (error) {
    console.error(error);
    next(error);
  }
}
```

🔻 routes/index.js

```js
const express = require('express');
const {
  renderMain, renderRoom, createRoom, enterRoom, removeRoom, sendChat,
} = require('../controllers');

const router = express.Router();

router.get('/', renderMain);

router.get('/room', renderRoom);

router.post('/room', createRoom);

router.get('/room/:id', enterRoom);

router.delete('/room/:id', removeRoom);

router.post('/room/:id/chat', sendChat);

module.exports = router;
```

<br/>

### **🍳  GIF 전송 구현**

🔻 views/chat.html

[소스 코드 참고](https://github.com/ZeroCho/nodejs-book/blob/master/ch12/12.7/gif-chat/views/chat.html){:target="_blank"}

<br/>

- 이미지 업로드이기 때문에 multer 사용

- 이미지 저장 후 파일 경로를 chat 데이터에 뿌림

- 이미지를 제공할 static 폴더 연결

🔻 routes/index.js

```js
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');

const {
  renderMain, renderRoom, createRoom, enterRoom, removeRoom, sendChat, sendGif,
} = require('../controllers');

const router = express.Router();

router.get('/', renderMain);

router.get('/room', renderRoom);

router.post('/room', createRoom);

router.get('/room/:id', enterRoom);

router.delete('/room/:id', removeRoom);

router.post('/room/:id/chat', sendChat);

try {
  fs.readdirSync('uploads');
} catch (err) {
  console.error('uploads 폴더가 없어 uploads 폴더를 생성합니다.');
  fs.mkdirSync('uploads');
}
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
router.post('/room/:id/gif', upload.single('gif'), sendGif);

module.exports = router;
```

<br/>

🔻 controllers/index.js

```js
const Room = require('../schemas/room');
const Chat = require('../schemas/chat');
const { removeRoom: removeRoomService } = require('../services');

exports.renderMain = async (req, res, next) => {
  try {
    const rooms = await Room.find({});
    res.render('main', { rooms, title: 'GIF 채팅방' });
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.renderRoom = (req, res) => {
  res.render('room', { title: 'GIF 채팅방 생성' });
};

exports.createRoom = async (req, res, next) => {
  try {
    const newRoom = await Room.create({
      title: req.body.title,
      max: req.body.max,
      owner: req.session.color,
      password: req.body.password,
    });
    const io = req.app.get('io');
    io.of('/room').emit('newRoom', newRoom);
    if (req.body.password) { // 비밀번호가 있는 방이면
      res.redirect(`/room/${newRoom._id}?password=${req.body.password}`);
    } else {
      res.redirect(`/room/${newRoom._id}`);
    }
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.enterRoom = async (req, res, next) => {
  try {
    const room = await Room.findOne({ _id: req.params.id });
    if (!room) {
      return res.redirect('/?error=존재하지 않는 방입니다.');
    }
    if (room.password && room.password !== req.query.password) {
      return res.redirect('/?error=비밀번호가 틀렸습니다.');
    }
    const io = req.app.get('io');
    const { rooms } = io.of('/chat').adapter;
    console.log(rooms, rooms.get(req.params.id), rooms.get(req.params.id));
    if (room.max <= rooms.get(req.params.id)?.size) {
      return res.redirect('/?error=허용 인원이 초과하였습니다.');
    }
    const chats = await Chat.find({ room: room._id }).sort('createdAt');
    return res.render('chat', {
      room,
      title: room.title,
      chats,
      user: req.session.color,
    });
  } catch (error) {
    console.error(error);
    return next(error);
  }
};

exports.removeRoom = async (req, res, next) => {
  try {
    await removeRoomService(req.params.id);
    res.send('ok');
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.sendChat = async (req, res, next) => {
  try {
    const chat = await Chat.create({
      room: req.params.id,
      user: req.session.color,
      chat: req.body.chat,
    });
    req.app.get('io').of('/chat').to(req.params.id).emit('chat', chat);
    res.send('ok');
  } catch (error) {
    console.error(error);
    next(error);
  }
};

exports.sendGif = async (req, res, next) => {
  try {
    const chat = await Chat.create({
      room: req.params.id,
      user: req.session.color,
      gif: req.file.filename,
    });
    req.app.get('io').of('/chat').to(req.params.id).emit('chat', chat);
    res.send('ok');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

🔻 app.js

```js
...
app.use(express.static(path.join(__dirname, 'public')));
app.use('/gif', express.static(path.join(__dirname, 'uploads')));
app.use(express.json());
...

```

<br/>

### **🍳 GIF 채팅 화면**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/46247347-dd6b-495a-9b2a-3fe49548c364)

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

> [웹소켓](https://ko.wikipedia.org/wiki/%EC%9B%B9%EC%86%8C%EC%BC%93){:target="_blank"}
{:.prompt-info}

> [ws 공식 문서](https://www.npmjs.com/package/ws){:target="_blank"}
{:.prompt-info}

> [socket.io 공식 문서](https://socket.io/){:target="_blank"}
{:.prompt-info}

> [proxy-addr 공식 문서](https://www.npmjs.com/package/proxy-addr){:target="_blank"}
{:.prompt-info}

> [color-hash 공식 문서](https://www.npmjs.com/package/color-hash){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}