---
layout: post
title: 프로젝트 구조 갖추기
author: admin
date: 2023-10-04 00:00:00 +900
lastmod: 2023-10-04 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT, TYPESCRIPT + NODEJS] # 대문자로 작성
tags: [typescript, nodejs, typescript + nodejs] # 소문자로 작성
---

> 해당 포스트는 `typescript + nodejs`의 내용을 복습하며 필요한 내용을 정리한 포스트입니다.
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 TYPESCRIPT + NODEJS**

## **💻 초기 세팅**

```sh
npm init
```

package.json을 만들어서 node 프로젝트를 만들기

```sh
npm i typescript
```

tsconfig.json 생성 

- 타입스크립트 프로젝트라고 알려주는 파일, 타입스크립트 설정함.

`compilerOptions`

- 타입스크립트는 strict를 true 안하면 타입스크립트 의미가 퇴색됨

`lib` 

- 최신 문법들 다 쓸 수 있게 최대한 추가 es2015~es2020

`moduleResolution`

- classic과 node가 있는데 `node`로 설정 

```sh
npm i @types/node
```

index.ts 생성 

- ts 파일은 노드가 직접 실행할 수 없어서 자바스크립트로 바꿔서 실행

- 한 번의 트랜스파일 과정이 필요

```sh
npm i express @types/express
```

타입스크립트에서는 보통 const-require보다 import as A from ‘A’; 를 사용

`as`는 import하는 파일에서 가져오는 모듈이 export default A 이면 ‘ as’가 안 붙어도 된다.

그렇지 않다면 `as`를 붙여 줌으로써 export defalut처럼 만들 수 있다. (module.exports는 default랑 좀 다르다.)

`node_modules - @types - index.d.ts` 파일로 들어가서 코드 확인 가능하다.

커맨드 + f로 export default 찾아보고 없으면 `as`를 붙여줘야 한다.

export default가 있고 그걸 가져오고 싶으면 그냥 `import A from 'A'`를 쓴다.

tsconfig.json에서 `esModuleInterop: true`로 설정하면 `as`를 사용하지 않을 수 있지만 `true`로 사용하지 않는 것이 좋을 것 같다.

`* as`의 사용 이유는 기본적으로는 commonjs와 es2015 모듈의 차이점 때문이다.

import A from 'B'는 B의 default를 불러오는 것인데 commonjs에는 default가 없다.

module.exports는 default랑 좀 다르다.

따라서 module.exports는 `*`으로 불러와야 합니다.

<br/>

---

<br/>

```ts
// const express = require('express')
// import express = require('express');
import * as express from 'express';
const app = express();
const prod = process.env.NODE_ENV === 'production';

app.get('/', (req, res) => {
  res.send('백엔드 정상 동작');
});

app.listen(prod ? process.env.PORT : 3065, () => {
  console.log(`server is running on ${process.env.PORT}`);
});
```

### **🍳 res, res, next 타이핑**

```ts
// const express = require('express')
// import express = require('express');
import * as express from 'express';
import { Request, Response, NextFunction } from 'express';

const app = express();
const prod = process.env.NODE_ENV === 'production';

app.get('/', (req: Request, res: Response, next: NextFunction) => {
  res.send('백엔드 정상 동작');
});

app.listen(prod ? process.env.PORT : 3065, () => {
  console.log(`server is running on ${process.env.PORT}`);
});
```

윈도우 기준으로 `F12` 버튼을 이용해서 index.d.ts 파일로 접근할 수 있다. 

express의 경우 `express-serve-static-core` 까지 들어가면 전역으로 설정한 namespace Express가 존재한다.

```ts
declare global {
    namespace Express {
        // These open interfaces may be extended in an application-specific manner via declaration merging.
        // See for example method-override.d.ts (https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/method-override/index.d.ts)
        interface Request {}
        interface Response {}
        interface Locals {}
        interface Application {}
    }
}
```

이를 통해서 아래와 같이 사용할 수도 있다.

```ts
app.get('/', (req: Express.Request, res: Response, next: NextFunction) => {
  res.send('백엔드 정상 동작');
});
```

### **🍳 app**

app 의 타이핑은 아래와 같이 할 수 있다.

```ts
import { Request, Response, NextFunction, Application } from 'express';

const app: Application = express();
```

하지만 추론을 통해 타이핑이 제대로 이루어지므로 생략할 수 있다.

<br/>

---

<br/>

## **💻 ts-node와 nodemon**

```ts
import * as express from 'express';

const app = express();
const prod = process.env.NODE_ENV === 'production';

app.set('port', prod ? process.env.PORT : 3065);
app.get('/', (req, res, next) => {
  res.send('백엔드 정상 동작');
});

app.listen(app.get('port'), () => {
  console.log(`server is running on ${process.env.PORT}`);
});
```


```sh
npm i -D ts-node 
npm i -D nodemon
npx ts-node index.ts
```

npx 사용 이유는 글로벌 설치를 막기 위해서다.

글로벌 설치 단점이 package.json에 기록이 안 되서 프로젝트 인수인계할 때 따로 문서를 관리해야 합니다.

번거롭기에 글로벌 설치를 피하는 추세입니다.

그래서 dependencies나 devDependencies에 설치하고 npx명령어로 글로벌 설치 없이 실행할 수 있습니다.

ts-node는 배포용으로 쓰기에는 무리가 있다.(성능에 악영향)

ts-node는 자바스크립트 코드로 그 때 그 때 바꿉니다.

배포 시에는 클라이언트 요청 하나 올 때 처리도 버거운데 ts->js 변경 작업까지하면 쓸데없이 작업이 하나 더 늘게 된다.

배포 환경에서는 미리 `$ npx tsc` 로 타입스크립트를 자바스크립트로 컴파일 후 자바스크립트를 배포하는게 좋다.

즉, 개발 시는 ts-node로 편리하게(tsc 후 만든 js를 node로 실행하는 과정을 한 번에), 배포는 tsc로 트랜스파일 후 자바스크립트 코드로 배포

참고로 비슷하게 babel-node가 있다.

노드에서 지원하지 않는 최신 문법을 바벨을 통해서 트랜스파일을 하면 최신 문법을 사용할 수 있다.

바벨 노드도 배포 시 성능상 무리와서 배포 시에는 미리 트랜스파일 후 js코드로 배포한다.

<br/>

---

<br/>

## **💻 module resolution**

개발을 하다보면 타입이 잘못 되어 있는 경우가 있을 수 있다.

이러한 상황을 마주하면 타입핑이 어떻게 되어있는지 찾아가야 하는 상황이 올 수 있다.

이는 tsc에 `--traceResoluttion` 옵션을 주어 찾아갈 수 있다.

`$ npx tsc --traceResolution` : 개발할 때 한 번씩 테스트용(타입스크립트의 타입을 어떻게 찾는지 궁금할 때)

예를 들어, 타입핑을 기본적으로 제공하지 않는 express같은 거는 커뮤니티가 만든 @types/express를 같이 설치해야 하는데, 타입스크립트가 과연 타입핑을 어떻게 찾아올까?

```sh
npx tsc --traceResolution
```

실행 시 다음과 같은 메세지들이 출력된다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/85debe10-fecb-45a0-857d-7617245cf215){: width="500" height="500" }

모듈을 찾아가는 순서는 아래 링크를 참고 (꼭 읽어보자)

> [타입스크립트 공식문서 Module Resolution](https://www.typescriptlang.org/docs/handbook/module-resolution.html){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## **💻 express 미들웨어**

```sh
npm i morgan cors cookie-parser express-session dotenv passport hpp helmet passport-local bcrypt
```

```sh
npm i @types/morgan @types/cors @types/cookie-parser @types/express-session @types/dotenv @types/passport @types/hpp @types/helmet @types/passport-local @types/bcrypt
```

미들웨어 패키지를 설치할 때 주의할 점이 있다.

대부분의 패키지들이 타입 지원을 안해서 타입패키지를 추가로 설치해야 한다.

이 때 일반 패키지와 타입 패키지의 버전이 일치하는지 확인해야 한다.

예를 들어, 어떤 패키지가 5버전이면 타입도 5버전이여야 하는데 타입 패키지 버전은 현재 4버전 일 수도 있다.

메이저 버전(맨 앞자리)가 같아야 안정적이다. 살짝 문제가 될 수도 있다.

해결책은 2가지다.

1. 일반 패키지 다운그레이드하여 4버전을 쓰거나(버전 일치 시키기 위함)

2. 에러 발생 시 타입패키지를 커스텀마이징해서 쓰거나

🔻 tsconfig.json

```js
{
  "compilerOptions": {
    "strict": true,
    "lib": ["es2020"],
    "esModuleInterop": true,
    "moduleResolution": "node",
    "typeRoots": ["./types"]
  }
}
```

🔻 index.ts

```ts
import express from 'express';
import morgan from 'morgan';
import cors from 'cors';
import cookieParser from 'cookie-parser';
import expressSession from 'express-session';
import dotenv from 'dotenv';
import passport from 'passport';
import hpp from 'hpp';
import helmet from 'helmet';

dotenv.config();
const app = express();
const prod: boolean = process.env.NODE_ENV === 'production';

app.set('port', prod ? process.env.PORT : 3065);

if (prod) {
  app.use(hpp());
  app.use(helmet());
  app.use(morgan('combined'));
  app.use(
    cors({
      origin: /nodebird\.com$/,
      credentials: true,
    })
  );
} else {
  app.use(morgan('dev'));
  app.use(
    cors({
      origin: true,
      credentials: true,
    })
  );
}

app.use('/', express.static('uploads'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(
  expressSession({
    resave: false,
    saveUninitialized: false,
    secret: process.env.COOKIE_SECRET!,
    cookie: {
      httpOnly: true,
      secure: false, // https -> true
      domain: prod ? '.nodebird.com' : undefined,
    },
    name: 'rnbck',
  })
);
app.use(passport.initialize());
app.use(passport.session());

app.get('/', (req, res, next) => {
  res.send('react nodebird 백엔드 정상 동작!');
});

app.listen(app.get('port'), () => {
  console.log(`server is running on ${app.get('port')}`);
});
```

<br/>

---

<br/>

## **💻 시퀄라이즈**

```sh
npm i sequelize
```

타입스크립트랑 데이터베이스 쓸 때는 `Sequelize` 또는 `TypeORM` 또는 많이 쓴다.

시퀄라이즈는 특유의 문법이 있다.

`sequelize.query`로 sql쿼리문 할 수도 있다.(orm으로 하기 어려운 것들 있을 때 사용)

```sh
npm i sequelize sequelize-cli mysql2 
npx sequelize init
```

시퀄라이즈는 내부적으로 타입 지원함(@types 안 받아도 됨)

config.json -> js로 바꿔서 .env 사용

config.js 설정대로 db 생성 됨.

```sh
npx sequelize db:create
```

생성 후에는 config.ts로 변경.

sequelize cli도 js밖에 인식을 못해서 config.js로 환경설정 후 db만들고, ts파일로 변경

시퀄라이즈를 통해 model 코드를 작성할 때 각 모델과 index.ts의 순환참조로 인해 에러가 발생할 수 있다.

순환 참조가 타입핑적으로는 문제가 되진 않지만, 런타임(타입스크립트)에서 에러가 없다고 실제로 에러가 없는 것은 아니다.

실제로 실행해보면 에러가 나는 경우가 있음

- ex 순환참조, 순환 참조시 두 모듈 중 하나가 빈 객체({})로 처리되서 문제가 발생함

순환참조 시 타입같은 건 괜찮은데 실제 런타임에 실행되는 코드는 에러가 남

순환 참조를 피하기 위해서 sequelize.ts를 만들어 연결 설정

이걸 export해서 유저모델에서 가져다 쓰고 user를 다시 index에서 가져다 쓸 수 있다.

마치 임시변수 만들어서 스왑하는 느낌

<br/>

---

<br/>

## **💻 sequelize 관련 소스 코드**

### **🍳 config/config.ts**

```ts
import * as dotenv from 'dotenv';
dotenv.config();

type Config = {
  username: string;
  password: string;
  database: string;
  host: string;
  [key: string]: string;
};
interface ConfigGroup {
  development: Config;
  test: Config;
  production: Config;
}
const config: ConfigGroup = {
  development: {
    username: 'root',
    password: process.env.DB_PASSWORD!,
    database: 'react-nodebird',
    host: '127.0.0.1',
    dialect: 'mysql',
  },
  test: {
    username: 'root',
    password: process.env.DB_PASSWORD!,
    database: 'react-nodebird',
    host: '127.0.0.1',
    dialect: 'mysql',
  },
  production: {
    username: 'root',
    password: process.env.DB_PASSWORD!,
    database: 'react-nodebird',
    host: '127.0.0.1',
    dialect: 'mysql',
  },
};

export default config;
```

<br/>

### **🍳 models/index.ts**

```ts
import User, { associate as associateUser } from './user';
import Comment, { associate as associateComment } from './comment';
import Hashtag, { associate as associateHashtag } from './hashtag';
import Image, { associate as associateImage } from './image';
import Post, { associate as associatePost } from './post';

export * from './sequelize';

const db = {
  User,
  Comment,
  Hashtag,
  Image,
  Post,
};

export type dbType = typeof db;

associateUser(db);
associateComment(db);
associateHashtag(db);
associateImage(db);
associatePost(db);

```

<br/>

### **🍳 models/sequelize.ts**

```ts
import { Sequelize } from 'sequelize';
import config from '../config/config';

const env = process.env.NODE_ENV as ('production' | 'test' | 'development') || 'development';
const { database, username, password } = config[env];
const sequelize = new Sequelize(database, username, password, config[env]);

export { sequelize };
export default sequelize;
```

<br/>

### **🍳 models/user.ts**

```ts
import { 
  Model, DataTypes, BelongsToManyGetAssociationsMixin, 
  HasManyGetAssociationsMixin, BelongsToManyRemoveAssociationMixin,
  BelongsToManyAddAssociationMixin,
 } from 'sequelize';
import { dbType } from './index';
import Post from './post';
import { sequelize } from './sequelize';

class User extends Model {
  public readonly id!: number;
  public nickname!: string;
  public userId!: string;
  public password!: string;
  public readonly createdAt!: Date;
  public readonly updatedAt!: Date;

  public readonly Posts?: Post[];
  public readonly Followers?: User[];
  public readonly Followings?: User[];

  public addFollowing!: BelongsToManyAddAssociationMixin<User, number>;
  public getFollowings!: BelongsToManyGetAssociationsMixin<User>;
  public removeFollowing!: BelongsToManyRemoveAssociationMixin<User, number>;
  public getFollowers!: BelongsToManyGetAssociationsMixin<User>;
  public removeFollower!: BelongsToManyRemoveAssociationMixin<User, number>;
  public getPosts!: HasManyGetAssociationsMixin<Post>;
}

User.init({
  nickname: {
    type: DataTypes.STRING(20),
  },
  userId: {
    type: DataTypes.STRING(20),
    allowNull: false,
    unique: true,
  },
  password: {
    type: DataTypes.STRING(100),
    allowNull: false,
  }
}, {
  sequelize,
  modelName: 'User',
  tableName: 'user',
  charset: 'utf8',
  collate: 'utf8_general_ci',
});

export const associate = (db: dbType) => {
  db.User.hasMany(db.Post, { as: 'Posts' });
  db.User.hasMany(db.Comment);
  db.User.belongsToMany(db.Post, { through: 'Like', as: 'Liked' });
  db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followers', foreignKey: 'followingId' });
  db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followings', foreignKey: 'followerId' });
};

export default User;
```

<br/>

### **🍳 models/post.ts**

```ts
import {
  DataTypes, Model, BelongsToManyAddAssociationsMixin, HasManyAddAssociationsMixin,
  HasManyAddAssociationMixin, BelongsToManyAddAssociationMixin, BelongsToManyRemoveAssociationMixin,
} from 'sequelize';
import { dbType } from './index';
import { sequelize } from './sequelize';
import Hashtag from './hashtag';
import Image from './image';
import User from './user';

class Post extends Model {
  public readonly id!: number;
  public content!: string;
  public readonly createdAt!: Date;
  public readonly updatedAt!: Date;

  public UserId!: number;
  public readonly Retweet?: Post;
  public RetweetId?: number;

  public addHashtags!: BelongsToManyAddAssociationsMixin<Hashtag, number>
  public addImages!: HasManyAddAssociationsMixin<Image, number>
  public addImage!: HasManyAddAssociationMixin<Image, number>
  public addLiker!: BelongsToManyAddAssociationMixin<User, number>
  public removeLiker!: BelongsToManyRemoveAssociationMixin<User, number>
}

Post.init({
  content: {
    type: DataTypes.TEXT,
    allowNull: false,
  },
}, {
  sequelize,
  modelName: 'Post',
  tableName: 'post',
  charset: 'utf8mb4',
  collate: 'utf8mb4_general_ci',
});

export const associate = (db: dbType) => {
  db.Post.belongsTo(db.User);
  db.Post.hasMany(db.Comment);
  db.Post.hasMany(db.Image);
  db.Post.belongsTo(db.Post, { as: 'Retweet' });
  db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' });
  db.Post.belongsToMany(db.User, { through: 'Like', as: 'Likers' });
};

export default Post;
```

<br/>

### **🍳 models/comment.ts**

```ts
import { DataTypes, Model } from 'sequelize';
import { dbType } from './index';
import { sequelize } from './sequelize';

class Comment extends Model {
  public readonly id!: number;
  public content!: string;
  public readonly createdAt!: Date;
  public readonly updatedAt!: Date;
}

Comment.init({
  content: {
    type: DataTypes.TEXT,
    allowNull: false,
  }
}, {
  sequelize,
  modelName: 'Comment',
  tableName: 'comment',
  charset: 'utf8mb4',
  collate: 'utf8mb4_general_ci',
});

export const associate =(db: dbType) => {

};

export default Comment;
```

<br/>

### **🍳 models/hashtag.ts**

```ts
import { DataTypes, Model } from 'sequelize';
import { dbType } from './index';
import { sequelize } from './sequelize';

class Hashtag extends Model {
  public readonly id!: number;
  public name!: string;
  public readonly createdAt!: Date;
  public readonly updatedAt!: Date;
}

Hashtag.init({
  name: {
    type: DataTypes.STRING(20),
    allowNull: false,
  },
}, {
  sequelize,
  modelName: 'Hashtag',
  tableName: 'hashtag',
  charset: 'utf8mb4',
  collate: 'utf8mb4_general_ci',
});

export const associate = (db: dbType) => {};

export default Hashtag;
```

### **🍳 models/image.ts**

```ts
import { DataTypes, Model } from 'sequelize';
import { dbType } from './index';
import { sequelize } from './sequelize';

class Image extends Model {
  public readonly id!: number;
  public src!: number;
  public readonly createdAt!: Date;
  public readonly updatedAt!: Date;
}

Image.init({
  src: {
    type: DataTypes.STRING(200),
    allowNull: false,
  },
}, {
  sequelize,
  modelName: 'Image',
  tableName: 'image',
  charset: 'utf8',
  collate: 'utf8_general_ci',
});

export const associate = (db: dbType) => {

};

export default Image;
```

<br/>

---

<br/>

## **💻 passport 설정하기**

serializeUser, deserializeUser 각각 언제 실행되는지 정확히 알아야 한다.

serializeUser는 로그인 할 때 한 번 실행

deserializeUser는 모든 라우터, 모든 요청에 대해서 한 번씩 다 실행 됨. 매번 실행

serializeUser에서 done의 매개변수로 유저 정보를 메모리에 저장하는건데, 유저 정보 통째로 저장하면 너무 무거우니까 user id만 저장

그럼 저장한 id를 deserializeUser에서 사용

자세한 내용은 [npm - passport](https://www.npmjs.com/package/passport){:target="_blank"}에 있다.

<br/>

---

<br/>

## **💻 passport 관련 소스 코드**

### **🍳 passport/index.ts**

```ts
import passport from 'passport';

import User from '../models/user';
import local from './local';

export default () => {
  passport.serializeUser<User, number>((user, done) => {
    done(null, user.id);
  });

  passport.deserializeUser<User, number>(async (id, done) => {
    try {
      const user = await User.findOne({
        where: { id },
      });
      if (!user) {
        return done(new Error('no user'));
      }
      return done(null, user); // req.user
    } catch (err) {
      console.error(err);
      return done(err);
    }
  });

  local();
}
```

<br/>

### **🍳 passport/local.ts**

```ts
import passport from 'passport';
import bcrypt from 'bcrypt';
import { Strategy } from 'passport-local';

import User from '../models/user';

export default () => {
  passport.use('local', new Strategy({
    usernameField: 'userId',
    passwordField: 'password',
  }, async (userId, password, done) => {
    try {
      const user = await User.findOne({ where: { userId } });
      if (!user) {
        return done(null, false, { message: '존재하지 않는 사용자입니다!' });
      }
      const result = await bcrypt.compare(password, user.password);
      if (result) {
        return done(null, user);
      }
      return done(null, false, { message: '비밀번호가 틀립니다.' });
    } catch (err) {
      console.error(err);
      return done(err);
    }
  }))
};
```

<br/>

---

<br/>

## **📚 레퍼런스**

> [Node.js에 TypeScript 적용하기(feat. NodeBird)](https://www.inflearn.com/course/nodejs-typescript-%EC%A0%81%EC%9A%A9/dashboard){:target="_blank"}
{:.prompt-info}
