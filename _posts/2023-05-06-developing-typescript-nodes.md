---
layout: post
title: 17. 타입스크립트 노드 개발
author: admin
date: 2023-05-06 00:00:00 +900
lastmod: 2023-05-06 00:00:00 +900
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

# **🌈 타입스크립트 노드 개발**

## **💻 타입스크립트 기본 문법**

타입스크립트는 자바스크립트에 명시적으로 타입이 추가된 언어

타입스크립트 코드는 `tsc` 라는 컴파일러를 통해 자바스크립트 코드로 변환할 수 있다. 

노드는 자바스크립트만 실행할 수 있으므로, 타입스크립트 코드를 자바스크립트 코드로 변환해야만 실행 가능하다.

타입스크립트 패키지를 설치하면 함께 설치된다. 

```sh
npm init -y
npm i typescript
npx tsc --init
```

tsconfig.json이 생성되는데 주석을 제외한 부분만 살펴보자

🔻 tsconfig.json

```js
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs", // 노드이기 떄문에          
    "esModuleInterop": true,                            
    "forceConsistentCasingInFileNames": true,    
    "strict": true,                
    "skipLibCheck": true                            
  }
}
```

`target` 

- 결과물의 문법을 어떤 버전의 자바스크립트 코드로 만들어 낼지 

`module`

- 결과물의 모듈 시스템을 어떤 종류로 할지
- 노드라면 commonjs
- 최신 브라우저에서는 es2022 

`esModuleInterop`

- CommonJS 모듈도 ECMAScript 모듈처럼 인식하게 해줌
- true 로 설정해주면 됨

`forceConsistentCasingInFileNames`

- true이면 모듈을 import할 때 파일명의 대소문자가 정확히 일치해야 한다.

`strict`   

- 엄격한 타입 검사를 할지 
- true로 하지 않으면 타입스크립트를 쓰는 의미가 퇴색됨

`skipLibCheck`

- true이면 모든 라이브러리의 타입을 검사하는 대신 내가 직접적으로 사용하는 라이브러리의 타입만 검사해 시간 절약

### **🍳 테스트 해보기**

🔻 index.ts

```ts
let a = 'hello' 
a = 'world'
```

```sh
npx tsc
```

🔻 index.js

```js
"use strict";
let a = 'hello';
a = 'world';
```

### **🍳 테스트 해보기 2**

🔻 index.ts

```ts
let a = 'hello' 
a = 123
```

```sh
npx tsc
```

🔻 터미널

```sh
$ npx tsc
index.ts:2:1 - error TS2322: Type 'number' is not assignable to type 'string'.

2 a = 123;
```

하지만 에러가 발생하더라도 index.js 는 그대로 생성된다.

즉, 타입스크립트에서 타입 검사가 실패해도 변환은 이루어진다.

결과물을 만들어내지 않고 타입 검사만 하고 싶을 때는 아래 명령어를 사용한다.

```sh
npx tsc --noEmit
```

<br/>

### **🍳 명시적 타입 붙여보기**

🔻 compare.js

```js
let a = true;
const b = { hello: 'world' };

function add(x, y) { return x + y };
const minus = (x, y) => x - y;

```

🔻 index.ts

```ts
let a: boolean = true;
const b: { hello: string } = { hello: 'world' };

function add(x: number, y: number): number {
  return x + y;
}
const minus = (x: number, y: number): number => x - y;

```

만약 compare.js와 같이 index.ts를 작성하고 noEmit으로 실행시키면 다음과 같은 결과가 나온다.

```sh
$ npx tsc --noEmit
index.ts:4:14 - error TS7006: Parameter 'x' implicitly has an 'any' type.

4 function add(x, y) {
               ~

index.ts:4:17 - error TS7006: Parameter 'y' implicitly has an 'any' type.

4 function add(x, y) {
                  ~

index.ts:7:16 - error TS7006: Parameter 'x' implicitly has an 'any' type.

7 const minus = (x, y) => x - y;
                 ~

index.ts:7:19 - error TS7006: Parameter 'y' implicitly has an 'any' type.

7 const minus = (x, y) => x - y;
                    ~

Found 4 errors in the same file, starting at: index.ts:4
```

<br/>

### **🍳 책에서 사용하는 몇 가지 타입 예시**

🔻 type.ts

```ts
let a: string | number = 'hello'; // 유니언 타이핑
a = 123;

let arr: string[] = []; // 배열 타이핑
arr.push('hello');

interface Inter {
  hello: string;
  world?: number; // 있어도 그만 없어도 그만인 속성
} // 객체를 인터페이스로 타이핑할 수 있음
const b: Inter = { hello: 'interface' };

type Type = {
  hello: string;
  func?: (param?: boolean) => void; // 함수는 이런 식으로 타이핑함
}
const c: Type = { hello: 'type' };

interface Merge {
  x: number,
}
interface Merge {
  y: number, // 두번 선언되었지만 하나로 합쳐짐
}
const m: Merge = { x: 1, y: 2 };

export { a }; // 타입스크립트 ECMAScript 모듈을 사용
```

<br/>

### **🍳 노드 타이핑하기**

🔻 node.ts

```ts
import fs from 'fs';

fs.readFile('pakage.json');
```

```sh
$ npx tsc --noEmit

node.ts:1:16 - error TS2307: Cannot find module 'fs' or its corresponding type declarations.
1 import fs from 'fs';
                 ~~~~
Found 1 error in node.ts:1 
```

'fs'에서 에러가 발생한다.

fs 모듈의 타입 정의를 찾을 수 없다. 

노트의 타입은 `@types/node` 패키지를 따로 설치해야 사용 가능하다.

```sh
$ npm i -D @types/node

$ npx tsc --noEmit
error TS2554: Expected 2-3 arguments, but got 1.      
  fs.readFile('pakage.json');     
  node_modules/@types/node/fs.d.ts:2742:9   
  2742 callback: (err: NodeJS.ErrnoException | null, data: Buffer) => void,
  An argument for 'callback' was not provided.

Found 1 error in node.ts:3 
```

패키지를 설치하고 다시 실행하면 TS2554 에러가 발생한다. 인자로 2~3개의 값을 전달해야하는데 한 개만 넣었다는 뜻

궁금한 타입에 커서를 올리고 `F12` 를 올리면 타입의 정의를 볼 수 있다.

정의를 참고해서 수정하면 에러가 사라지는걸 볼 수 있다.

🔻 node.ts 

```ts
import fs from 'fs';

fs.readFile('pakage.json', (err, result) => {
  console.log(result);
});
```

프로미스로 코드를 바꿔보면 

🔻 node.ts 

```ts
import fs from 'fs/promises';

fs.readFile('pakage.json', (err, result) => {
  console.log(result);
});
```

다음과 같은 에러가 발생한다.

```sh
node.ts:3:28 - error TS2769: No overload matches this call.
node.ts:3:29 - error TS7006: Parameter 'err' implicitly has an 'any' type.
node.ts:3:34 - error TS7006: Parameter 'result' implicitly has an 'any' type.
```

이 역시 잘 못된 함수 사용법 때문에 발생하는 에러다.

🔻 node.ts

```ts
import fs from 'fs/promises';

fs.readFile('pakage.json')
  .then((result) => {
    //result는 Buffer 타입이다.
    console.log(result);
  })
  .catch(console.error);
```

<br/>

---

<br/>

## **💻 커뮤니티 타입 정의 적용하기**

11장의 프로젝트를 기반으로 진행한다.

[11장 프로젝트 소스 코드 참고](https://github.com/ZeroCho/nodejs-book/tree/master/ch11/11.5/nodebird){:target="_blank"}

우선 타입스크립트를 설치하고 tsconfig.json 파일을 만든다.

```sh
npm i typescript
```

🔻 tsconfig.json

```js
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "allowJs": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  }
}

```

### **🍳 app.js server.js 수정하기**

하나씩 기존 코드를 바꿔보도록하자.

app.js 와 server.js를 .ts 파일 확장자로 변경하고

모듈 시스템을 CommonJS에서 ECMAScript 모듈로 변경한다.

🔻 server.ts

```ts
import app from './app';

app.listen(app.get('port'), () => {
  console.log(app.get('port'), '번 포트에서 대기중');
});
```

🔻 app.ts

```ts
import express from 'express';
import cookieParser from 'cookie-parser';
import morgan from 'morgan';
import path from 'path';
import session from 'express-session';
import nunjucks from 'nunjucks';
import dotenv from 'dotenv';
import passport from 'passport';

dotenv.config();
import pageRouter from './routes/page';
import authRouter from './routes/auth';
import postRouter from './routes/post';
import userRouter from './routes/user';
import { sequelize } from './models';
import passportConfig from './passport';

const app = express();
passportConfig(); // 패스포트 설정
app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
  express: app,
  watch: true,
});

sequelize
  .sync({ force: false })
  .then(() => {
    console.log('데이터베이스 연결 성공');
  })
  .catch((err) => {
    console.error(err);
  });

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use('/img', express.static(path.join(__dirname, 'uploads')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(
  session({
    resave: false,
    saveUninitialized: false,
    secret: process.env.COOKIE_SECRET,
    cookie: {
      httpOnly: true,
      secure: false,
    },
  })
);
app.use(passport.initialize());
app.use(passport.session());

app.use('/', pageRouter);
app.use('/auth', authRouter);
app.use('/post', postRouter);
app.use('/user', userRouter);

app.use((req, res, next) => {
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  next(error);
});

app.use((err, req, res, next) => {
  console.error(err);
  res.locals.message = err.message;
  res.locals.error = process.env.NODE_ENV !== 'production' ? err : {};
  res.status(err.status || 500);
  res.render('error');
});

export default app;
```

이렇게 수정해주면 설치한 패키지에 타입 정의가 없다는 에러가 나오므로 해당 패키지들의 타입들을 설치해줘야 한다.

```sh
npm i -D @types/bcrypt @types/cookie-parser @types/express @types/express-session @types/morgan @types/nunjucks @types/passport @types/sequelize @types/node
```

많은 에러가 사라졌지만 여전히 남은 에러들이 있다. 

그 중 app.ts에서 

secret: process.env.COOKIE_SECRET 이 부분에 에러가 발생하는데 

타입스크립트는 app.ts를 실행시켜본 것이 아니기 때문에 process.env 가 있는지 확실 할 수 없어서 string | undefined로 타입추론을 한다. 

하지만 우리는 이미 존재할 것으로 확신하기 때문에 느낌표를 뒤에 붙여서 해결할 수 있다. 이 말은 타입스크립트에게 이건 절대 undefined가 아니라고 말해주는 것과 같다.(최소한으로만 쓰는 것이 좋다.)

```ts
app.use(
  session({
    resave: false,
    saveUninitialized: false,
    secret: process.env.COOKIE_SECRET!,
    cookie: {
      httpOnly: true,
      secure: false,
    },
  })
);
```

여기까지 수정한 상태로 tsc 명령어를 실행해보자 

```sh
npx tsc
```

아직 에러는 존재하지만 js 파일은 생성된다. 

노드 서버를 실행해서 잘 돌아가는지 확인한다.

```sh
npm start
```

<br/>

---

<br/>

## **💻 라이브러리 코드 타이핑하기**

### **🍳 passport 수정하기**

🔻 passport/index.ts

```ts
import passport from 'passport';
import local from './localStrategy';
import kakao from './kakaoStrategy';
import User from '../models/user';

export default () => {
  passport.serializeUser((user, done) => {
    done(null, user.id);
  });

  passport.deserializeUser((id: number, done) => {
    User.findOne({
      where: { id },
      include: [
        {
          model: User,
          attributes: ['id', 'nick'],
          as: 'Followers',
        },
        {
          model: User,
          attributes: ['id', 'nick'],
          as: 'Followings',
        },
      ],
    })
      .then((user) => done(null, user))
      .catch((err) => done(err));
  });

  local();
  kakao();
};
```

🔻 passport/localStrategy.ts

```ts
import passport from 'passport';
import { Strategy as LocalStrategy } from 'passport-local';
import bcrypt from 'bcrypt';
import User from '../models/user';

export default () => {
  passport.use(
    new LocalStrategy(
      {
        usernameField: 'email',
        passwordField: 'password',
      },
      async (email, password, done) => {
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
      }
    )
  );
};
```

🔻 passport/kakaoStrategy.ts

```ts
import passport from 'passport';
import { Strategy as KakaoStrategy } from 'passport-kakao';

import User from '../models/user';

export default () => {
  passport.use(
    new KakaoStrategy(
      {
        clientID: process.env.KAKAO_ID,
        callbackURL: '/auth/kakao/callback',
        clientSecret: '',
      },
      async (accessToken, refreshToken, profile, done) => {
        console.log('kakao profile', profile);
        try {
          const exUser = await User.findOne({
            where: { snsId: profile.id, provider: 'kakao' },
          });
          if (exUser) {
            done(null, exUser);
          } else {
            const newUser = await User.create({
              email: profile._json && profile._json.kaccount_email,
              nick: profile.displayName,
              snsId: profile.id,
              provider: 'kakao',
            });
            done(null, newUser);
          }
        } catch (error) {
          console.error(error);
          done(error);
        }
      }
    )
  );
};
```

```sh
npm i -D @types/passport-local @types/passport-kakao
```

<br/>

### **🍳 Error 해결**

🔻 app.ts

```ts
app.use((req, res, next) => {
  const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
  error.status = 404;
  next(error);
});
```

app.ts 파일의 error.status에서 에러가 발생하는 것을 볼 수 있다. 

Error 에서 `F12`를 눌러보면 lib으로 이동하게 되는데 코드를 보면 다음과 같다.

```ts
interface Error {
    name: string;
    message: string;
    stack?: string;
}
```

여기서 status라는 것이 없기 때문에 에러가 발생한다.

이를 해결하는 방법은 global로 같은 이름의 interface를 선언하여 합치는 방법이 있다.

🔻 types/index.d.ts

```ts
declare global {
  interface Error {
    status?: number;
  }
}
```
<br/>

### **🍳 user.id 해결**

에러나는 부분을 파고 들어가보면 다음과 같이 정의되어 있다.

interface User가 빈 객체로 되어있어 바꿔서 써야한다.

```ts
declare global {
    namespace Express {
        // tslint:disable-next-line:no-empty-interface
        interface AuthInfo {}
        // tslint:disable-next-line:no-empty-interface
        interface User {}
```

🔻 types/index.d.ts

```ts
import IUser from '../models/user';

declare global {
  interface Error {
    status?: number;
  }

  namespace Express {
    interface User extends IUser {}
  }
}
export {}
```

kakaoStrategy.ts 도 이전과 같은 방법으로 process.env를 수정하고 `@types/passport-kakao` 에서 누락된 필수 속성을 추가해주자

🔻 kakaoStrategy.ts

```ts
export default () => {
  passport.use(
    new KakaoStrategy(
      {
        clientID: process.env.KAKAO_ID!,
        callbackURL: '/auth/kakao/callback',
        clientSecret: '',
      },
      async (accessToken, refreshToken, profile, done) => {
```

### **🍳 시퀄라이즈 타입 작성하기**

공식문서를 참고해서 타이핑해주자.

### **🍳 hashtag.ts**

🔻 models/hashtag.ts

```ts
import Sequelize, {
  Model, CreationOptional, InferAttributes, InferCreationAttributes,
} from 'sequelize';
import Post from './post';

class Hashtag extends Model<InferAttributes<Hashtag>, InferCreationAttributes<Hashtag>> {
  declare id: CreationOptional<number>;
  declare title: string;
  declare createdAt: CreationOptional<Date>;
  declare updatedAt: CreationOptional<Date>;

  static initiate(sequelize: Sequelize.Sequelize) {
    Hashtag.init({
      id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
      },
      title: {
        type: Sequelize.STRING(15),
        allowNull: false,
        unique: true,
      },
      createdAt: Sequelize.DATE,
      updatedAt: Sequelize.DATE,
    }, {
      sequelize,
      timestamps: true,
      underscored: false,
      modelName: 'Hashtag',
      tableName: 'hashtags',
      paranoid: false,
      charset: 'utf8mb4',
      collate: 'utf8mb4_general_ci',
    });
  }

  static associate() {
    Hashtag.belongsToMany(Post, { through: 'PostHashtag' });
  }
}

export default Hashtag;
```

declare를 사용해 추가한 속성은 js로 변환할 때 사라진다. 단순히 타입 표기용

`CreationOptional` 타입은 create 작업 시에는 필요 없는 속성을 표시하는 역할

<br/>

### **🍳 post.ts**

🔻 models/post.ts

```ts
import Sequelize, {
  CreationOptional, InferAttributes, InferCreationAttributes, Model,
} from 'sequelize';
import User from './user';
import Hashtag from './hashtag';

class Post extends Model<InferAttributes<Post>, InferCreationAttributes<Post>> {
  declare id: CreationOptional<number>;
  declare content: string;
  declare img: string;
  declare createdAt: CreationOptional<Date>;
  declare updatedAt: CreationOptional<Date>;

  static initiate(sequelize: Sequelize.Sequelize) {
    Post.init({
      id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
      },
      content: {
        type: Sequelize.STRING(140),
        allowNull: false,
      },
      img: {
        type: Sequelize.STRING(200),
        allowNull: true,
      },
      createdAt: Sequelize.DATE,
      updatedAt: Sequelize.DATE,
    }, {
      sequelize,
      timestamps: true,
      underscored: false,
      modelName: 'Post',
      tableName: 'posts',
      paranoid: false,
      charset: 'utf8mb4',
      collate: 'utf8mb4_general_ci',
    });
  }

  static associate() {
    Post.belongsTo(User);
    Post.belongsToMany(Hashtag, { through: 'PostHashtag' });
  }
}

export default Post;

```

<br/>

### **🍳 user.ts**

🔻 models/user.ts

```ts
import Sequelize, {
  CreationOptional, InferAttributes, InferCreationAttributes, Model,
} from 'sequelize';
import Post from './post';

class User extends Model<InferAttributes<User>, InferCreationAttributes<User>> {
  declare id: CreationOptional<number>;
  declare email: string;
  declare nick: string;
  declare password: CreationOptional<string>;
  declare provider: CreationOptional<string>;
  declare snsId: CreationOptional<string>;
  declare createdAt: CreationOptional<Date>;
  declare updatedAt: CreationOptional<Date>;
  declare deletedAt: CreationOptional<Date>;

  static initiate(sequelize: Sequelize.Sequelize) {
    User.init({
      id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
      },
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
        allowNull: true,
      },
      provider: {
        type: Sequelize.ENUM('local', 'kakao'),
        allowNull: false,
        defaultValue: 'local',
      },
      snsId: {
        type: Sequelize.STRING(30),
        allowNull: true,
      },
      createdAt: Sequelize.DATE,
      updatedAt: Sequelize.DATE,
      deletedAt: Sequelize.DATE,
    }, {
      sequelize,
      timestamps: true,
      underscored: false,
      modelName: 'User',
      tableName: 'users',
      paranoid: true,
      charset: 'utf8',
      collate: 'utf8_general_ci',
    });
  }

  static associate() {
    User.hasMany(Post);
    User.belongsToMany(User, {
      foreignKey: 'followingId',
      as: 'Followers',
      through: 'Follow',
    });
    User.belongsToMany(User, {
      foreignKey: 'followerId',
      as: 'Followings',
      through: 'Follow',
    });
  }
}

export default User;
```

<br/>

### **🍳 index.ts**

🔻 models/index.ts

```ts
import Sequelize from 'sequelize';
import configObj from '../config/config';
import User from './user';
import Post from './post';
import Hashtag from './hashtag';

const env = process.env.NODE_ENV as 'production' | 'test' || 'development';
const config = configObj[env];

export const sequelize = new Sequelize.Sequelize(
  config.database, config.username, config.password, config,
);

User.initiate(sequelize);
Post.initiate(sequelize);
Hashtag.initiate(sequelize);

User.associate();
Post.associate();
Hashtag.associate();

export { User, Post, Hashtag };
```

config 에서도 mysql을 사용하고 있음을 명시해주자.

🔻 config/config.ts

```ts
import dotenv from 'dotenv';

dotenv.config();

export default {
  development: {
    username: 'root',
    password: 'nodejsbook',
    database: 'nodebird',
    host: '127.0.0.1',
    dialect: 'mysql' as const,
  },
  test: {
    username: 'root',
    password: 'nodejsbook',
    database: 'nodebird_test',
    host: '127.0.0.1',
    dialect: 'mysql' as const,
  },
  production: {
    username: 'root',
    password: 'nodejsbook',
    database: 'nodebird',
    host: '127.0.0.1',
    dialect: 'mysql' as const,
    logging: false,
  },
};

```

<br/>

---

<br/>


## **💻 내가 작성한 코드 타이핑하기**

### **🍳 middleware**

err, req, res에 타이핑을 해야한다. express를 불러와 타이핑할 수 있다.

🔻 middlewares/index.ts

```ts
import { Request, Response, NextFunction } from 'express';

const isLoggedIn = (req : Request, res : Response, next : NextFunction) => {
  if (req.isAuthenticated()) {
    next();
  } else {
    res.status(403).send('로그인 필요');
  }
};

const isNotLoggedIn = (req : Request, res : Response, next : NextFunction) => {
  if (!req.isAuthenticated()) {
    next();
  } else {
    const message = encodeURIComponent('로그인한 상태입니다.');
    res.redirect(`/?error=${message}`);
  }
};
export { isLoggedIn, isNotLoggedIn };

```

@types/express 패키지를 살펴보면 더 좋은 방법을 찾을 수 있다. 

🔻 middlewares/index.ts

```ts
import { RequestHandler } from 'express';

const isLoggedIn: RequestHandler = (req, res, next) => {
  if (req.isAuthenticated()) {
    next();
  } else {
    res.status(403).send('로그인 필요');
  }
};

const isNotLoggedIn: RequestHandler = (req, res, next) => {
  if (!req.isAuthenticated()) {
    next();
  } else {
    const message = encodeURIComponent('로그인한 상태입니다.');
    res.redirect(`/?error=${message}`);
  }
};
export { isLoggedIn, isNotLoggedIn };
```

<br/>

### **🍳 controllers**

🔻 controllers/auth.ts

```ts
import bcrypt from 'bcrypt';
import passport from 'passport';
import User from '../models/user';
import { RequestHandler } from 'express';

const join: RequestHandler = async (req, res, next) => {
  const { email, nick, password } = req.body;
  try {
    const exUser = await User.findOne({ where: { email } });
    if (exUser) {
      return res.redirect('/join?error=exist');
    }
    const hash = await bcrypt.hash(password, 12);
    await User.create({
      email,
      nick,
      password: hash,
    });
    return res.redirect('/');
  } catch (error) {
    console.error(error);
    return next(error);
  }
}

const login: RequestHandler = (req, res, next) => {
  passport.authenticate('local', (authError, user, info) => {
    if (authError) {
      console.error(authError);
      return next(authError);
    }
    if (!user) {
      return res.redirect(`/?loginError=${info.message}`);
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

const logout: RequestHandler = (req, res) => {
  req.logout(() => {
    res.redirect('/');
  });
};

export { login, join, logout };
```

🔻 controllers/page.ts

```ts
import { RequestHandler } from 'express';
import User from '../models/user';
import Post from '../models/post';
import Hashtag from '../models/hashtag';

const renderProfile: RequestHandler = (req, res) => {
  res.render('profile', { title: '내 정보 - NodeBird' });
};

const renderJoin: RequestHandler = (req, res) => {
  res.render('join', { title: '회원가입 - NodeBird' });
};

const renderMain: RequestHandler = async (req, res, next) => {
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
}

const renderHashtag: RequestHandler = async (req, res, next) => {
  const query = req.query.hashtag as string;
  if (!query) {
    return res.redirect('/');
  }
  try {
    const hashtag = await Hashtag.findOne({ where: { title: query } });
    let posts: Post[] = [];
    if (hashtag) {
      posts = await hashtag.getPosts({ include: [{ model: User }] });
    }

    return res.render('main', {
      title: `${query} | NodeBird`,
      twits: posts,
    });
  } catch (error) {
    console.error(error);
    return next(error);
  }
};

export { renderHashtag, renderProfile, renderMain, renderJoin };

```

🔻 controllers/post.ts

```ts
import { RequestHandler } from 'express';
import Post from '../models/post';
import Hashtag from '../models/hashtag';

const afterUploadImage: RequestHandler = (req, res) => {
  console.log(req.file);
  res.json({ url: `/img/${req.file?.filename}` });
};

const uploadPost: RequestHandler = async (req, res, next) => {
  try {
    const post = await Post.create({
      content: req.body.content,
      img: req.body.url,
      UserId: req.user?.id,
    });
    const hashtags: string[] = req.body.content.match(/#[^\s#]*/g);
    if (hashtags) {
      const result = await Promise.all(
        hashtags.map(tag => {
          return Hashtag.findOrCreate({
            where: { title: tag.slice(1).toLowerCase() },
          })
        }),
      );
      await post.addHashtags(result.map(r => r[0]));
    }
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};

export { afterUploadImage, uploadPost };

```

🔻 controllers/user.ts

```ts
import { RequestHandler } from 'express';
import User from '../models/user';

const follow: RequestHandler = async (req, res, next) => {
  try {
    const user = await User.findOne({ where: { id: req.user?.id } });
    if (user) {
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

export { follow };
```

<br/>

### **🍳 app.ts**

🔻 app.ts

```ts
import express, { ErrorRequestHandler } from 'express';

...

const errorHandler: ErrorRequestHandler = (err, req, res, next) => {
  console.error(err);
  res.locals.message = err.message;
  res.locals.error = process.env.NODE_ENV !== 'production' ? err : {};
  res.status(err.status || 500);
  res.render('error');
};
app.use(errorHandler);
```

<br/>

### **🍳 models**

🔻 models/hashtag.ts

```ts
import Sequelize, {
  Model, CreationOptional, InferAttributes, InferCreationAttributes, 
  BelongsToManyGetAssociationsMixin,
} from 'sequelize';
import Post from './post';

class Hashtag extends Model<InferAttributes<Hashtag>, InferCreationAttributes<Hashtag>> {
  declare id: CreationOptional<number>;
  declare title: string;
  declare createdAt: CreationOptional<Date>;
  declare updatedAt: CreationOptional<Date>;

  declare getPosts: BelongsToManyGetAssociationsMixin<Post>;

  static initiate(sequelize: Sequelize.Sequelize) {
   
...

```

🔻 models/user.ts

```ts
import Sequelize, {
  CreationOptional, InferAttributes, InferCreationAttributes, Model,
  BelongsToManyAddAssociationMixin,
  NonAttribute,
} from 'sequelize';
import Post from './post';

class User extends Model<InferAttributes<User>, InferCreationAttributes<User>> {
  declare id: CreationOptional<number>;
  declare email: string;
  declare nick: string;
  declare password: CreationOptional<string>;
  declare provider: CreationOptional<string>;
  declare snsId: CreationOptional<string>;
  declare createdAt: CreationOptional<Date>;
  declare updatedAt: CreationOptional<Date>;
  declare deletedAt: CreationOptional<Date>;

  declare Followers?: NonAttribute<User[]>;
  declare Followings?: NonAttribute<User[]>;
  declare addFollowing: BelongsToManyAddAssociationMixin<User, number>;

  static initiate(sequelize: Sequelize.Sequelize) {
    User.init({
      id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
      },
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
        allowNull: true,
      },
      provider: {
        type: Sequelize.ENUM('local', 'kakao'),
        allowNull: false,
        defaultValue: 'local',
      },
      snsId: {
        type: Sequelize.STRING(30),
        allowNull: true,
      },
      createdAt: Sequelize.DATE,
      updatedAt: Sequelize.DATE,
      deletedAt: Sequelize.DATE,
    }, {
      sequelize,
      timestamps: true,
      underscored: false,
      modelName: 'User',
      tableName: 'users',
      paranoid: true,
      charset: 'utf8',
      collate: 'utf8_general_ci',
    });
  }

  static associate() {
    // User.hasMany(Post);
    User.belongsToMany(User, {
      foreignKey: 'followingId',
      as: 'Followers',
      through: 'Follow',
    });
    User.belongsToMany(User, {
      foreignKey: 'followerId',
      as: 'Followings',
      through: 'Follow',
    });
  }
}

export default User;
```

🔻 models/post.ts

```ts
import Sequelize, {
  CreationOptional, InferAttributes, InferCreationAttributes, Model,
  BelongsToManyAddAssociationsMixin, ForeignKey,
} from 'sequelize';
import User from './user';
import Hashtag from './hashtag';

class Post extends Model<InferAttributes<Post>, InferCreationAttributes<Post>> {
  declare id: CreationOptional<number>;
  declare content: string;
  declare img: string;
  declare createdAt: CreationOptional<Date>;
  declare updatedAt: CreationOptional<Date>;

  declare UserId: ForeignKey<User['id']>;
  declare addHashtags: BelongsToManyAddAssociationsMixin<Hashtag, number>;

  static initiate(sequelize: Sequelize.Sequelize) {
    Post.init({
      id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
      },
      content: {
        type: Sequelize.STRING(140),
        allowNull: false,
      },
      img: {
        type: Sequelize.STRING(200),
        allowNull: true,
      },
      createdAt: Sequelize.DATE,
      updatedAt: Sequelize.DATE,
    }, {
      sequelize,
      timestamps: true,
      underscored: false,
      modelName: 'Post',
      tableName: 'posts',
      paranoid: false,
      charset: 'utf8mb4',
      collate: 'utf8mb4_general_ci',
    });
  }

  static associate() {
    Post.belongsTo(User);
    Post.belongsToMany(Hashtag, { through: 'PostHashtag' });
  }
}

export default Post;
```

<br/>

### **🍳 routes**

🔻 routes/auth.ts

```ts
import express from 'express';
import passport from 'passport';

import { isLoggedIn, isNotLoggedIn } from '../middlewares';
import { join, login, logout } from '../controllers/auth';

const router = express.Router();

// POST /auth/join
router.post('/join', isNotLoggedIn, join); 

// POST /auth/login
router.post('/login', isNotLoggedIn, login);

// GET /auth/logout
router.get('/logout', isLoggedIn, logout);

// GET /auth/kakao
router.get('/kakao', passport.authenticate('kakao'));

// GET /auth/kakao/callback
router.get('/kakao/callback', passport.authenticate('kakao', {
  failureRedirect: '/?loginError=카카오로그인 실패',
}), (req, res) => {
  res.redirect('/'); // 성공 시에는 /로 이동
});

export default router;
```
🔻 routes/page.ts

```ts
import express from 'express';
import { isLoggedIn, isNotLoggedIn } from '../middlewares';
import {
  renderProfile, renderJoin, renderMain, renderHashtag,
} from '../controllers/page';

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

export default router;
```

🔻 routes/post.ts

```ts
import express from 'express';
import multer from 'multer';
import path from 'path';
import fs from 'fs';

import { afterUploadImage, uploadPost } from '../controllers/post';
import { isLoggedIn } from '../middlewares';

const router = express.Router();

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
      cb(null, path.basename(file.originalname, ext) + Date.now() + ext);
    },
  }),
  limits: { fileSize: 5 * 1024 * 1024 },
});

// POST /post/img
router.post('/img', isLoggedIn, upload.single('img'), afterUploadImage);

// POST /post
const upload2 = multer();
router.post('/', isLoggedIn, upload2.none(), uploadPost);

export default router;
```
🔻 routes/user.ts

```ts
import express from 'express';

import { isLoggedIn } from '../middlewares';
import { follow } from '../controllers/user';

const router = express.Router();

// POST /user/:id/follow
router.post('/:id/follow', isLoggedIn, follow);

export default router;
```

<br/>

### **🍳 tsconfig.json**

```js
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "allowJs": false,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  }
}
```

<br/>

---

<br/>


## **💻 함께 보면 좋은 자료**

> [타입스크립트 공식문서](https://www.typescriptlang.org/docs/){:target="_blank"}
{:.prompt-info}

> [타입스크립트 핸드북 한글 문서](https://typescript-kr.github.io/){:target="_blank"}
{:.prompt-info}

> [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped){:target="_blank"}
{:.prompt-info}

> [sequelize typescript](https://sequelize.org/docs/v6/other-topics/typescript/){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}