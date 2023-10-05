---
layout: post
title: 라우터, 시퀄라이즈
author: admin
date: 2023-10-05 00:00:00 +900
lastmod: 2023-10-05 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT, TYPESCRIPT + NODEJS] # 대문자로 작성
tags: [typescript, nodejs, typescript + nodejs] # 소문자로 작성
---

> 해당 포스트는 [Node.js에 TypeScript 적용하기(feat. NodeBird)](https://www.inflearn.com/course/nodejs-typescript-%EC%A0%81%EC%9A%A9/dashboard){:target="_blank"} 강의로 `typescript + nodejs`의 내용을 복습하며 필요한 내용을 정리한 포스트입니다.
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 라우터와 시퀄라이즈**

## **💻 User**

타입스크립트를 포함한 익스프레스의 라우터와 시퀄라이즈를 구현해보자.

### **🍳 routes/middleware.ts**

이 파일에는 현재 사용자의 인증 여부를 파악하는 함수를 만든다.

```ts
import {Request, Response, NextFunction} from 'express';

const isLoggedIn = (req: Request, res: Response, next: NextFunction) => {
    if (req.isAuthenticated()) next();
    else res.status(401).send('로그인이 필요합니다!');
};

const isNotLoggedIn = (req: Request, res: Response, next: NextFunction) => {
    if (!req.isAuthenticated()) next();
    else res.status(401).send('로그인한 사용자는 접근할 수 없습니다.');
};

export { isLoggedIn, isNotLoggedIn };
```

`isAuthenticated()` 메소드는 로그인 판단 여부를 확인하는 기능을 한다. 

isLoggedIn 변수의 req에 담겨 있는 유저의 정보가 로그인되어 있으면 이 메소드는 true를 반환하고, next()를 호출해서 다음 작업을 실행하게 한다.

로그인이 유효하지 않다면 401에러(클라이언트는 해당 리소스에 접근할 자격이 없음)를 반환하게 된다.

여기서 각 변수의 매개변수에 `Request`, `Response`, `NextFunction`의 타입 정의를 직접 했다. 

라우터와 직접 연결되어 있으면 알아서 타입 추론이 되기 때문에 타이핑을 할 필요가 없지만, 라우터와 분리되는 순간 타이핑을 해야 한다.

타입스크립트 입장에서 위의 변수들은 그저 하나의 함수일 뿐이기 때문에 매개변수와 리턴값(위의 함수들은 리턴값이 없어서 여기서는 제외)을 직접 타이핑해야 한다.

### **🍳 routes/user.ts**

user.ts에는 사용자 정보와 관련된 라우터들을 작성한다.

```ts
import * as express from 'express';
import * as bcrypt from 'bcrypt';
import { isLoggedIn } from './middleware';
import User from '../models/user';

const router = express.Router();

// 사용자 정보 불러오기
router.get('/', isLoggedIn, (req, res) => {     
    // get은 req, res에 타입 정의가 되어있기 때문에 따로 타입핑 안해도 됨
    const user = req.user!.toJSON() as User;
    delete user.password;
    return res.json(user);
});

// 회원가입
router.post('/', async(req, res, next) => {
    try {
        const exUser = await User.findOne({      
          // 먼저 회원이 가입되어 있는지 여부 확인
            where: {
                userId: req.body.userId
            }
        });
        if (exUser) return res.status(403).send('이미 사용 중인 아이디입니다.');    
        // 아이디 중복 체크
        const hashedPassword = await bcrypt.hash(req.body.password, 12);    
        // 두 번째 매개변수 숫자 클수록 암호화 보안 증가, 그러나 암호화 시간 증가, 컴퓨터 성능에 따라 조절 잘해야 함
        const newUser = await User.create({     
          // 중복된 아이디 아니라면 새로운 유저 생성
            nickname: req.body.nickname,
            userId: req.body.userId,
            password: hashedPassword
        });
        return res.status(200).json(newUser);
    } catch(err) {
        console.error(err);
        next(err);
    }
});
```

여기서 router.get()의 user 변수를 보면 req.user 뒤에 `!` 를 붙이고 있다. 

이는 타입스크립트의 한계 때문에 적어야 하는 것인데, `!`가 없다면 개체가 `undefined`인 것 같습니다.ts(2532) 와 같은 에러 문구가 뜬다.

실제로는 isLoggedIn 함수를 통과해야 user 변수를 실행할 수 있다. 

즉, isLoggedIn 미들웨어를 통과했다는 말은 req.user가 존재한다는 것을 증명한다는 말이다.

하지만 타입스크립트는 타입을 추론만 할 뿐 실제 로직이 어떻게 돌아가는지 모르기 때문에 req.user가 있는지 없는지 알 수 없어서 에러가 나는 것이다.

<br/>

### **🍳 files와 typeRoots 옵션**

위처럼 수정한다해도 아직 완벽히 해결된 것은 아니다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/16ab9749-350b-4db4-9b96-cad6ae1241f1)

### **🍳 tsconfig.json**

```js
{
  "ts-node": {
    "files": true
  },
  "compilerOptions": {
    "strict": true,
    "lib": ["es2020"],
    "esModuleInterop": true,
    "moduleResolution": "node",
  },
  "files": ["index.ts", "./types/index.ts"] // 등록한 것만 컴파일 되도록
}
```

<br/>

### **🍳 types/index.ts**

```ts
import User from '../models/user';

declare global {
  namespace Express {
    interface Request {
      user?: User;
    }
  }
}
```

files 옵션을 사용해서 원하는 것만 컴파일되도록 등록할 수 있다.

하지만 아직 오류가 계속 남아있는 상태다.

<br/>

### **🍳 typeRoot**

```js
{
  "ts-node": {
    "files": true
  },
  "compilerOptions": {
    "strict": true,
    "lib": ["es2020"],
    "esModuleInterop": true,
    "moduleResolution": "node",
    "typeRoots": ["./types"]
  }
}
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/76711941-c810-45ab-8c95-630813648254)


typeRoots 추가했더니 user의 타입 오류는 없어졌지만 충돌이 발생한다.

<br/>

### **🍳 ambient module**

```ts
import User from '../models/user';

declare global {
  namespace Express {
    interface Request {
      user?: User;
    }
  }
}
```

위의 코드를 아래처럼 바꿔주면 된다.

```js
import User from '../models/user';

declare module "express-serve-static-core" {
    interface Request {
        user?: User;
    }
}
```

<br/>

### **🍳 routes/user.ts 로그인 라우터 추가**

```ts
router.post('/login', isNotLoggedIn, (req, res, next) => {
  passport.authenticate('local', (err: Error, user: User, info: { message: string }) => {
    // 타입 추론이 제대로 되지 않을 때는 직접 지정
    if (err) {
      console.error(err);
      return next(err);
    }
    if (info) {
      return res.status(401).send(info.message);
    }
    return req.login(user, async (loginErr: Error) => {
      // 타입 추론이 제대로 되지 않을 때는 직접 지정
      try {
        if (loginErr) {
          return next(loginErr);
        }
        const fullUser = await User.findOne({
          where: { id: user.id },
          include: [
            {
              model: Post,
              as: 'Posts',
              attributes: ['id'],
            },
            {
              model: User,
              as: 'Followings',
              attributes: ['id'],
            },
            {
              model: User,
              as: 'Followers',
              attributes: ['id'],
            },
          ],
          attributes: {
            exclude: ['password'],
            // 내 정보 불러오는 것이기 때문에 비밀번호 제외한 정보 다 불러오기
          },
        });
        return res.json(fullUser);
      } catch (e) {
        console.error(e);
        return next(e);
      }
    });
  })(req, res, next);
});

```

라우터 안에 보면 `passport.authenticat()`라는 메소드가 보인다. 

`passport.authenticat()`는 req에 있는 유저의 데이터로 인증하는 미들웨어다.

인증이 성공한다면 `req.user` 속성이 인증된 사용자로 설정된다. 

이 메소드는 두 개의 매개 변수를 가진다. 

첫 번째는 `strategy`이고 두 번째는 `콜백함수`다. 

여기서 strategy는 리퀘스트를 `인증`하는 기능이고, 리퀘스트는 인증 메커니즘을 구현함으로써 수행된다. 

인증 메커니즘은 리퀘스트의 암호나 아이디를 인코딩하는 방법이다. 

strategy의 기본 값은 `local`이다. 

여기서도 strategy에 `local`을 적었다. 

로컬은 username과 password를 확인하고 이를 통해 콜백 함수를 실행한다.

> [passport - authentication - middleware](https://www.passportjs.org/concepts/authentication/middleware/){:target="_blank"}
{:.prompt-info}

> [[번역] `passport-local`에 대해 알아야 하는 모든 것](https://velog.io/@jakeseo_me/%EB%B2%88%EC%97%AD-passport-local%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EC%95%BC-%ED%95%98%EB%8A%94-%EB%AA%A8%EB%93%A0-%EA%B2%83){:target="_blank"}
{:.prompt-info}

<br/>

### **🍳 routes/user.ts 로그아웃 라우터 추가**

```ts
// 로그아웃
router.post('/logout', isLoggedIn, (req, res) => {
  req.logout();
  req.session!.destroy(() => {
    res.send('logout 성공');
  });
});
```

<br/>

### **🍳 routes/user.ts 특정 사용자 불러오는 라우터 추가**

🔻 routes/user.ts

```ts
// 특정 사용자 정보 불러오기
interface IUser extends User {
  PostCount: number;
  FollowingCount: number;
  FollowerCount: number;
}
router.get('/:id', async (req, res, next) => {
  try {
    const user = await User.findOne({
      where: { id: parseInt(req.params.id, 10) },
      include: [
        {
          model: Post,
          as: 'Posts',
          attributes: ['id'],
        },
        {
          model: User,
          as: 'Followings',
          attributes: ['id'],
        },
        {
          model: User,
          as: 'Followers',
          attributes: ['id'],
        },
      ],
      attributes: ['id', 'nickname'],
      // 남의 정보 가져오는 것이기 때문에 아이디와 닉네임만
    });
    if (!user) {
      return res.status(404).send('no user');
    }
    const jsonUser = user.toJSON() as IUser;
    jsonUser.PostCount = jsonUser.Posts ? jsonUser.Posts.length : 0;
    jsonUser.FollowingCount = jsonUser.Followings ? jsonUser.Followings.length : 0;
    jsonUser.FollowerCount = jsonUser.Followers ? jsonUser.Followers.length : 0;
    return res.json(jsonUser);
  } catch (err) {
    console.error(err);
    return next(err);
  }
});
```

🔻 models/user.ts

```ts
import { DataTypes, Model } from 'sequelize';
import sequelize from './sequelize';
import { dbType } from '.';
import Post from './post';

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
}
```

jsonUser 변수를 보면, 해당 유저의 sns에 해당하는 포스트 개수와 팔로잉, 팔로워 수를 가져와야 하는데 이와 관련된 코드들이 User클래스에서 작성하지 않았었다. 

models/user.ts의 User 클래스에 추가로 작성해주고, routes/user.ts에 인터페이스 extends를 통해 확장할 수 있게 했다.

이 속성들은 여기서 한 번만 사용되기 때문에 오히려 다른 곳에 쓰는 것이 더 가독성이 떨어질 수도 있기 때문이라고 생각한다. 

api 엔드포인트를 `/:id` 이런 식으로 한 이유는 `:id` 같은 키워드를 주소에 넣어주면 나중에 로그를 볼 때 주소만 보고도 어떤 데이터에 작업을 했는지 더 쉽게 알아볼 수 있기 때문이라고 한다. 이런 사소한 부분들이 유지보수하는데 꽤 도움이 될 것 같다.

<br/>

### **🍳 routes/user.ts 사용자를 팔로잉하는 사람들의 정보를 불러오는 라우터**

```ts
router.get('/:id/followings', isLoggedIn, async (req, res, next) => {
  try {
    // 항상 먼저 해당 사용자가 존재하는지 먼저 찾아보기!
    const user = await User.findOne({
      where: { id: parseInt(req.params.id, 10) || (req.user && req.user.id) || 0 },
    });
    if (!user) return res.status(404).send('No user');
    // 그러고 나서 팔로워 찾기
    const followings = await User.getFollowings({
      attributes: ['id', 'nickname'],
    });
  } catch (err) {
    console.error(err);
    return next(err);
  }
});
```

followings 변수를 보면 `User.getFollowings()` 라는 메소드가 있다. 

이 메소드는 기존에 만들지 않았던 부분이기 때문에 User클래스에 추가해야 한다.

🔻 models/user.ts

```ts
import {
  BelongsToManyAddAssociationMixin,
  BelongsToManyGetAssociationsMixin,
  BelongsToManyRemoveAssociationMixin,
  DataTypes,
  HasManyGetAssociationsMixin,
  Model,
} from 'sequelize';
import { dbType } from '.';
import Post from './post';
import { sequelize } from './sequelize';

class User extends Model {
  public readonly id!: number;
  // !를 붙이는 이유? 반드시 존재한다는 것을 시퀄라이즈에 확신시키는 것
  public nickname!: string;
  public userId!: string;
  public password!: string;
  public readonly createAt!: Date;
  // 시퀄라이즈 내에서 자체적으로 수정하기 때문에 readonly로
  public readonly updateAt!: Date;

  public readonly Posts?: Post[];
  public readonly Followers?: User[];
  public readonly Followings?: User[];

  public addFollowing!: BelongsToManyAddAssociationMixin<User, number>;
  public getFollowings!: BelongsToManyGetAssociationsMixin<User>; //s 주의
  public removeFollowings!: BelongsToManyRemoveAssociationMixin<User, number>;
  // remove는 제네릭이 두 개 필요함 {모델, FK의 타입}
  public getFollowers!: BelongsToManyGetAssociationsMixin<User>;
  public removeFollowers!: BelongsToManyRemoveAssociationMixin<User, number>;
  public getPost!: HasManyGetAssociationsMixin<Post>;
}

User.init(
  {
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
    },
  },
  {
    // 시퀄라이즈로 모델과 연동
    sequelize,
    modelName: 'User',
    tableName: 'user',
    charset: 'utf8', // 한글 인식 가능하도록
    collate: 'utf8_general_ci',
  }
);

// 모델간 관계 형성
export const associate = (db: dbType) => {
  db.User.hasMany(db.Post, { as: 'Posts' });
  db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followings', foreignKey: 'followerId' });
  // as가 가리키는 것과 foreignKey가 가리키는 것은 서로 반대
  db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followers', foreignKey: 'followingId' });
};

export default User;
```

public으로 선언하고 `!`를 붙여서 타입에러를 해결한 것 같다. 그런데 public 대신 static을 사용하면 타입에러가 발생하지 않는데 의도적으로 확신시키는 것보다는 static으로 선언하는 것이 나은 방법이 아닌가 생각된다. 

코드를 봐도 인스턴스에서 메소드를 사용하는 것이 아니라, User 클래스에서 바로 이름을 호출하기 때문에 static을 사용하는 것이 맞는 것 같다.

getFollowings 타입 형식인 `BelongsToManyGetAssociationsMixin` 은 뭘까..? 

```ts
(alias) type BelongsToManyGetAssociationsMixin<TModel> = (options?: BelongsToManyGetAssociationsMixinOptions | undefined) => Promise<TModel[]>
import BelongsToManyGetAssociationsMixin
The getAssociations mixin applied to models with belongsToMany. An example of usage is as follows:


User.belongsToMany(Role, { through: UserRole });

interface UserInstance extends Sequelize.Instance<UserInstance, UserAttributes>, UserAttributes {
 getRoles: Sequelize.BelongsToManyGetAssociationsMixin<RoleInstance>;
 // setRoles...
 // addRoles...
 // addRole...
 // createRole...
 // removeRole...
 // removeRoles...
 // hasRole...
 // hasRoles...
 // countRoles...
}
```

BelongToMany는 데이터베이스 n:m 관계 설정시 사용한다. 

하지만 데이터베이스에서 이를 직접 구현할 수 없기 때문에 이러한 관계를 일대다 관계로 분리해야 한다. 

<br/>

### **🍳 routes/user.ts 완성하기**

```ts
import express from 'express';
import { Request } from 'express';
import bcrypt from 'bcrypt';
import passport from 'passport';

import { isLoggedIn, isNotLoggedIn } from './middleware';
import User from '../models/user';
import Post from '../models/post';
import Image from '../models/image';

const router = express.Router();

// 사용자 정보 불러오기
router.get('/', isLoggedIn, (req, res) => {
  // get은 req, res에 타입 정의가 되어있기 때문에 따로 타입핑 안해도 됨
  const user = req.user;
  return res.json({ ...user, password: null });
});

// 회원가입
router.post('/', async (req, res, next) => {
  try {
    const exUser = await User.findOne({
      // 먼저 회원이 가입되어 있는지 여부 확인
      where: {
        userId: req.body.userId,
      },
    });
    if (exUser) {
      return res.status(403).send('이미 사용 중인 아이디입니다.');
      // 아이디 중복 체크
    }
    const hashedPassword = await bcrypt.hash(req.body.password, 12);
    // 두 번째 매개변수 숫자 클수록 암호화 보안 증가, 그러나 암호화 시간 증가, 컴퓨터 성능에 따라 조절 잘해야 함
    const newUser = await User.create({
      // 중복된 아이디 아니라면 새로운 유저 생성
      nickname: req.body.nickname,
      userId: req.body.userId,
      password: hashedPassword,
    });
    return res.status(200).json(newUser);
  } catch (error) {
    console.error(error);
    next(error);
  }
});

// 로그인
router.post('/login', isNotLoggedIn, (req, res, next) => {
  passport.authenticate('local', (err: Error, user: User, info: { message: string }) => {
    // 타입추론이 any라면 직접 정의하는 것이 좋음
    if (err) {
      console.error(err);
      return next(err);
    }
    if (info) {
      return res.status(401).send(info.message);
    }
    return req.login(user, async (loginErr: Error) => {
      try {
        if (loginErr) {
          return next(loginErr);
        }
        const fullUser = await User.findOne({
          where: { id: user.id },
          include: [
            {
              model: Post,
              as: 'Posts',
              attributes: ['id'],
            },
            {
              model: User,
              as: 'Followings',
              attributes: ['id'],
            },
            {
              model: User,
              as: 'Followers',
              attributes: ['id'],
            },
          ],
          attributes: {
            exclude: ['password'],
            // 내 정보 불러오는 것이기 때문에 비밀번호 제외한 정보 다 불러오기
          },
        });
        return res.json(fullUser);
      } catch (e) {
        console.error(e);
        return next(e);
      }
    });
  })(req, res, next);
});

// 로그아웃
router.post('/logout', isLoggedIn, (req, res) => {
  req.logout(() => {});
  req.session!.destroy(() => {
    res.send('logout 성공');
  });
});

// 특정 사용자 정보 불러오기
interface IUser extends User {
  // 한 번만 쓰이는 경우 그 파일에만, 여러번 사용되면 type 파일에 모아두기(개인 코딩 스타일)
  PostCount: number;
  FollowingCount: number;
  FollowerCount: number;
}

router.get('/:id', async (req, res, next) => {
  /**
   * 왜 '/:id' 이런 식?
   * :id처럼 주소에 넣어주면 나중에 로그를 볼 때 주소만 보고도 어떤 데이터에 작업을 했는지 알아볼 수 있기 때문
   */
  try {
    const user = await User.findOne({
      where: { id: parseInt(req.params.id, 10) },
      include: [
        {
          model: Post,
          as: 'Posts',
          attributes: ['id'],
        },
        {
          model: User,
          as: 'Followings',
          attributes: ['id'],
        },
        {
          model: User,
          as: 'Followers',
          attributes: ['id'],
        },
      ],
      attributes: ['id', 'nickname'],
      // 남의 정보 가져오는 것이기 때문에 아이디와 닉네임만
    });
    if (!user) {
      return res.status(404).send('no user');
    }
    const jsonUser = user.toJSON() as IUser;
    jsonUser.PostCount = jsonUser.Posts ? jsonUser.Posts.length : 0;
    jsonUser.FollowingCount = jsonUser.Followings ? jsonUser.Followings.length : 0;
    jsonUser.FollowerCount = jsonUser.Followers ? jsonUser.Followers.length : 0;
    return res.json(jsonUser);
  } catch (err) {
    console.error(err);
    return next(err);
  }
});

router.get<any, any, any, { limit: string; offset: string }>(
  '/:id/followings',
  isLoggedIn,
  async (req: Request<any, any, any, { limit: string; offset: string }>, res, next) => {
    try {
      // 항상 먼저 해당 사용자가 존재하는지 먼저 찾아보기!
      const user = await User.findOne({
        where: { id: parseInt(req.params.id, 10) || (req.user && req.user.id) || 0 },
      });
      if (!user) return res.status(404).send('no user');
      // 그러고 나서 팔로잉 찾기
      const followings = await user.getFollowings({
        attributes: ['id', 'nickname'],
        limit: parseInt(req.query.limit, 10),
        offset: parseInt(req.query.offset, 10),
      });
      return res.json(followings);
    } catch (e) {
      console.error(e);
      return next(e);
    }
  }
);

router.get<any, any, any, { limit: string; offset: string }>(
  '/:id/followers',
  isLoggedIn,
  async (req: Request<any, any, any, { limit: string; offset: string }>, res, next) => {
    try {
      // 항상 먼저 해당 사용자가 존재하는지 먼저 찾아보기!
      const user = await User.findOne({
        where: { id: parseInt(req.params.id, 10) || (req.user && req.user.id) || 0 },
      });
      if (!user) return res.status(404).send('no user');
      // 그러고 나서 팔로워 찾기
      const followers = await user.getFollowers({
        attributes: ['id', 'nickname'],
        limit: parseInt(req.query.limit, 10),
        offset: parseInt(req.query.offset, 10),
      });
      return res.json(followers);
    } catch (e) {
      console.error(e);
      return next(e);
    }
  }
);

// 팔로워 삭제
router.delete('/:id/follower', isLoggedIn, async (req, res, next) => {
  try {
    const me = await User.findOne({
      where: { id: req.user!.id },
    });
    await me!.removeFollower(parseInt(req.params.id, 10));
    res.send(req.params.id);
  } catch (e) {
    console.error(e);
    next(e);
  }
});

// 팔로우하기
router.post('/:id/follow', isLoggedIn, async (req, res, next) => {
  try {
    const me = await User.findOne({
      where: { id: req.user!.id },
    });
    await me!.addFollowing(parseInt(req.params.id, 10));
    res.send(req.params.id);
  } catch (e) {
    console.error(e);
    next(e);
  }
});

// 팔로우 취소
router.delete('/:id/follow', isLoggedIn, async (req, res, next) => {
  try {
    const me = await User.findOne({
      where: { id: req.user!.id },
    });
    await me!.removeFollowing(parseInt(req.params.id, 10));
    res.send(req.params.id);
  } catch (e) {
    console.error(e);
    next(e);
  }
});

// 게시글 가져오기
router.get('/:id/posts', async (req, res, next) => {
  try {
    const posts = await Post.findAll({
      where: {
        UserId: parseInt(req.params.id, 10) || (req.user && req.user.id) || 0,
        // 유저 아이디가 특정 사람의 아이디 || 내 아이디 || 내 아이디마저 없다면 0 넣는 꼼수
        RetweetId: null,
      },
      include: [
        {
          model: User,
          attributes: ['id', 'nickname'],
        },
        {
          model: Image,
        },
        {
          model: User,
          as: 'Likers',
          attributes: ['id'],
        },
      ],
    });
    res.json(posts);
  } catch (e) {
    console.error(e);
    next(e);
  }
});

// 닉네임 수정
router.patch('/nickname', isLoggedIn, async (req, res, next) => {
  try {
    await User.update(
      {
        nickname: req.body.nickname,
      },
      {
        where: { id: req.user!.id },
      }
    );
    res.send(req.body.nickname);
  } catch (e) {
    console.error(e);
    next(e);
  }
});

export default router;
```

<br/>

### **🍳 models/user.ts**

```ts
import {
  Model,
  DataTypes,
  BelongsToManyGetAssociationsMixin,
  HasManyGetAssociationsMixin,
  BelongsToManyRemoveAssociationMixin,
  BelongsToManyAddAssociationMixin,
} from 'sequelize';
import { dbType } from './index';
import Post from './post';
import { sequelize } from './sequelize';

class User extends Model {
  public readonly id!: number;
  // !를 붙이는 이유? 반드시 존재한다는 것을 시퀄라이즈에 확신시키는 것
  public nickname!: string;
  public userId!: string;
  public password!: string;
  public readonly createdAt!: Date;
  // 시퀄라이즈 내에서 자체적으로 수정하기 때문에 readonly로
  public readonly updatedAt!: Date;

  public readonly Posts?: Post[];
  public readonly Followers?: User[];
  public readonly Followings?: User[];

  public addFollowing!: BelongsToManyAddAssociationMixin<User, number>;
  public getFollowings!: BelongsToManyGetAssociationsMixin<User>;
  public removeFollowing!: BelongsToManyRemoveAssociationMixin<User, number>;
  // remove는 제네릭이 두 개 필요함 <모델, FK>
  public getFollowers!: BelongsToManyGetAssociationsMixin<User>;
  public removeFollower!: BelongsToManyRemoveAssociationMixin<User, number>;
  public getPosts!: HasManyGetAssociationsMixin<Post>;
}

User.init(
  {
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
    },
  },
  {
    // 시퀄라이즈로 모델과 연동
    sequelize,
    modelName: 'User',
    tableName: 'user',
    charset: 'utf8', // 한글 인식 가능하도록
    collate: 'utf8_general_ci',
  }
);

// 모델간 관계 형성
export const associate = (db: dbType) => {
  db.User.hasMany(db.Post, { as: 'Posts' });
  db.User.hasMany(db.Comment);
  db.User.belongsToMany(db.Post, { through: 'Like', as: 'Liked' });
  // as 기준으로 위에 작성한 메서드들 get post remove가 생김
  db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followers', foreignKey: 'followingId' });
  // as가 가리키는 것과 foreignKey가 가리키는 것은 서로 반대
  db.User.belongsToMany(db.User, { through: 'Follow', as: 'Followings', foreignKey: 'followerId' });
};

export default User;

```

<br/>

### **🍳 Sequelize를 통한 모델간 관계**

유저 모델 코드에서 보는 것과 같이 시퀄라이즈를 사용하려면 직접 코드를 짜서 관계를 형성해야 한다. 

User는 Post, Comment와 `1:N` 관계를 가지고 있고 이는 hasMany를 통해 구현했고 

팔로우, 좋아요 기능을 위해 User의 `N:M` 관계를 belongsToMany로 지정해줬다.

이때 `through` 속성은 `N:M` 관계에서 소스 및 대상을 결합하는데 사용되는 테이블의 이름이다.

이렇게 관계를 지정하면 `User.getFollowers()` 같은 관계에 상응하는 여러 메소드들이 생성된다. 

하지만 타입스크립트는 런타임에 제공되는 메소드들을 가지고 있지 않기 때문에 직접 타이핑을 해줘야 했고, 그래서 User 클래스에서 우리가 사용할 메소드들을 직접 입력했던 것이다.

```ts
class User extends Model {
  public readonly id!: number;
  // !를 붙이는 이유? 반드시 존재한다는 것을 시퀄라이즈에 확신시키는 것
  public nickname!: string;
  public userId!: string;
  public password!: string;
  public readonly createdAt!: Date;
  // 시퀄라이즈 내에서 자체적으로 수정하기 때문에 readonly로
  public readonly updatedAt!: Date;

  public readonly Posts?: Post[];
  public readonly Followers?: User[];
  public readonly Followings?: User[];

  public addFollowing!: BelongsToManyAddAssociationMixin<User, number>;
  public getFollowings!: BelongsToManyGetAssociationsMixin<User>;
  public removeFollowing!: BelongsToManyRemoveAssociationMixin<User, number>;
  // remove는 제네릭이 두 개 필요함 <모델, FK>
  public getFollowers!: BelongsToManyGetAssociationsMixin<User>;
  public removeFollower!: BelongsToManyRemoveAssociationMixin<User, number>;
  public getPosts!: HasManyGetAssociationsMixin<Post>;
}
```

> [각 메소드에 대한 설명](https://o10if.github.io/guido-backend/modules/_node_modules__types_sequelize_index_d_.sequelize.html){:target="_blank"}
{:.prompt-info}

라우트를 작성하면서 타입스크립트를 사용하는 경우가 모델 부분에서 관계를 형성하는 것 외에는 별로 없었다. 라우터 자체는 그냥 로직이다. 

관계를 선언하고 관계에 따른 메소드에 타이핑을 해야 한다.

<br/>

---

<br/>

## **💻 Post**

### **🍳 models/post.ts**

```ts
import {
  DataTypes,
  Model,
  BelongsToManyAddAssociationsMixin,
  HasManyAddAssociationsMixin,
  HasManyAddAssociationMixin,
  BelongsToManyAddAssociationMixin,
  BelongsToManyRemoveAssociationMixin,
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

  public addHashtags!: BelongsToManyAddAssociationsMixin<Hashtag, number>;
  public addImages!: HasManyAddAssociationsMixin<Image, number>;
  public addImage!: HasManyAddAssociationMixin<Image, number>;
  public addLiker!: BelongsToManyAddAssociationMixin<User, number>;
  public removeLiker!: BelongsToManyRemoveAssociationMixin<User, number>;
}

Post.init(
  {
    content: {
      type: DataTypes.TEXT,
      allowNull: false,
    },
  },
  {
    sequelize,
    modelName: 'Post',
    tableName: 'post',
    charset: 'utf8mb4',
    // 이모티콘 등의 문자들도 많이 사용하기 때문에
    collate: 'utf8mb4_general_ci',
    // 텍스트 정렬할 때 a 다음에 b 가 나타나야 한다는 생각으로 나온 정렬방식. 일반적으로 널리 사용
  }
);

export const associate = (db: dbType) => {
  db.Post.belongsTo(db.User); // 게시글을 작성한 사람
  db.Post.hasMany(db.Comment); // 게시글은 여러 개의 댓글 가지고 있음
  db.Post.hasMany(db.Image); // 게시글은 여러 개의 이미지를 가지고 있음
  db.Post.belongsTo(db.Post, { as: 'Retweet' }); // 하나의 게시글은 다른 게시글에 리트윗이 될 수 있음
  db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' }); // 해시태그와 다대다 관계
  db.Post.belongsToMany(db.User, { through: 'Like', as: 'Likers' }); // 게시글은 좋아요를 누른 사용자와 다대다 관계
  // 관계에 대한 코드 작성하였다면, 이에 맞는 라우트 작성하기 (routes/post.ts)
};

export default Post;
```

<br/>

### **🍳 routes/post.ts**

post 라우터를 만들기 위해서 설치해야 할 모듈들이다.

```sh
npm i multer @types/multer multer-s3 @types/multer-s3 aws-sdk
```

> [multer](https://github.com/expressjs/multer/blob/master/doc/README-ko.md){:target="_blank"} : 파일 업로드를 위해 사용되는 multipart/form-data를 다루기 위한 node.js의 미들웨어
{:.prompt-info}

> [multer-s3](https://www.npmjs.com/package/multer-s3){:target="_blank"} : 이미지 업로드 시 로컬 서버가 아닌 아마존의 S3에 업로드
{:.prompt-info} 

> [aws-sdk](https://www.npmjs.com/package/aws-sdk){:target="_blank"} : S3에 업로드하기 위해 aws 설정 
{:.prompt-info}

우선 AWS 설정 코드를 작성한다.

```ts
// AWS 설정
AWS.config.update({
    region: 'ap-northeast-2',
    accessKeyId: process.env.S3_ACCESS_KEY_ID,
    secretAccessKey: process.env.S3_SECRET_ACCESS_KEY,
});
```

multer를 이용하여 S3 버킷을 생성하고 지정된 버킷에 객체를 업로드하도록 코드를 작성한다.

```ts
const upload = multer({
    storage: multerS3({
        s3: new AWS.S3(),
        bucket: 'ts-nodebird',
        key(req, file, cb) {
            cb(null, `orginal/${+new Date()}${path.basename(file.originalname)}`);
        },
    }),
    limits: { fileSize: 20 * 1024 * 1024 },
});
```

- `storage`: 파일이 저장될 위치로, S3에 저장될 수 있도록 설정

- `bucket`: 파일을 저장하는데 사용되는 버킷

- `key`: 파일의 이름

- `limits`: 업로드된 데이터의 한도를 설정

다음으로 게시글과 관련된 라우터를 구현한다.

강의 코드대로 작성한 `s3: new AWS.S3()` 부분에서 에러가 발생한다. 

아마 S3 패키지가 업데이트 된거 같다.

AWS 설정부분은 제거하고 S3부분을 아래와 같이 수정했다.

```ts
const upload = multer({
  storage: multerS3({
    s3: new S3Client({
      credentials: {
        accessKeyId: process.env.S3_ACCESS_KEY as string,
        secretAccessKey: process.env.S3_SECRET_KEY as string,
      },
      region: 'ap-northeast-2',
    }),
    bucket: 'react-nodebird',
    key(req, file, cb) {
      cb(null, `original/${+new Date()}${path.basename(file.originalname)}`);
    },
  }),
  limits: { fileSize: 20 * 1024 * 1024 },
});
```

다음으로 게시글과 관련된 라우터를 구현한다.

```ts
router.post('/', isLoggedIn, upload.none(), async (req, res, next) => {
  try {
    const hashtags: string[] = req.body.content.match(/#[^\s]+/g);
    const newPost = await Post.create({
      content: req.body.content,
      UserId: req.user!.id,
    });
    if (hashtags) {
      const promises = hashtags.map((tag) =>
        Hashtag.findOrCreate({
          where: { name: tag.slice(1).toLowerCase() },
        })
      );
      const result = await Promise.all(promises);
      await newPost.addHashtags(result.map((r) => r[0]));
    }
    if (req.body.image) {
      if (Array.isArray(req.body.image)) {
        // 이미지가 여러 장일 때
        const promises: Promise<Image>[] = req.body.image.map((image: string) =>
          Image.create({ src: image })
        );
        const images = await Promise.all(promises);
        await newPost.addImages(images);
      } else {
        const image = await Image.create({ src: req.body.image });
        await newPost.addImage(image); // 시퀄라이즈에서는 단수, 복수도 신경써야 함
      }
    }
    const fullPost = await Post.findOne({
      // 게시글 가져오기
      where: { id: newPost.id },
      include: [
        {
          model: User,
          attributes: ['id', 'nickname'],
        },
        {
          model: Image,
        },
        {
          model: User,
          as: 'Likers',
          attributes: ['id'],
        },
      ],
    });
    return res.json(fullPost);
  } catch (err) {
    console.error(err);
    return next(err);
  }
});
```


<br/>

### **🍳index.ts 파일에 user, post 라우터 미들웨어를 추가**

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

import { sequelize } from './models';
import userRouter from './routes/user';
import postRouter from './routes/post';

dotenv.config();
const app = express();
// 환경변수 설정
const prod: boolean = process.env.NODE_ENV === 'production'; // 배포용

app.set('port', prod ? process.env.PORT : 3065);
// 배포용이면 포트 자유자재로 바꿀 수 있도록, 개발용이면 3065로 고정

// 시퀄라이즈
sequelize
  .sync({ force: false })
  // true면 서버 재시작할 때마다 db 초기화됨(배포 때 대참사).
  // 나중에 개발할 때 테이블 컬럼 등 수정요소 있으면 true
  .then(() => {
    console.log('데이터베이스 연결 성공!');
  })
  .catch((err: Error) => {
    console.error(err);
  });

// 미들웨어 장착
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
    // 타입스크립트에서는 dotenv 인식 못 함. ! 를 통해 에러 없앰
    cookie: {
      httpOnly: true,
      secure: false, // https -> true
      domain: prod ? '.nodebird.com' : undefined,
      // domain: prod && '.nodebird.com'  // domain은 string | undefined 타입 형식으로 에러남(js에서는 문제 없음)
    },
    name: 'rnbck',
  })
);
app.use(passport.initialize());
app.use(passport.session());
app.use('/user', userRouter);
app.use('/post', postRouter);

app.get('/', (req, res, next) => {
  res.send('react nodebird 백엔드 정상 동작!');
});

app.listen(app.get('port'), () => {
  console.log(`server is running on ${app.get('port')}`);
});

```

🔻 pakage.son

```js
...
  "scripts": {
    "dev": "nodemon",
    "start": "tsc && node index"
  },
...
```

🔻 nodemon.json

```js
{
  "exec": "ts-node index.ts",
  "ext": "js json ts"
}

```

<br/>

### **🍳 실행해보기**

```sh
npm run dev
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/740b16d4-08ba-4f2a-ae0e-33abf715ff2f)

<br/>

### **🍳 routes/post.ts image를 업로드 하는 라우터**

```ts
router.post('/images', upload.array('image'), (req, res) => {
  console.log(req.files);
  if (Array.isArray(req.files)) {
    req.files.map(() => {});
  }
  res.json((req.files as Express.MulterS3.File[]).map((v) => v.location));
  /**
   * location이라는 메소드는 MulterS3 네임스페이스에만 존재하기 때문에 Multer를 MulterS3로 강제 형변환
   */
});
```
<br/>

### **🍳 해당 아이디의 게시글을 찾는 라우터**

```ts
//해당 아이디의 게시글을 찾는 라우터
router.get('/:id', async (req, res, next) => {
  try {
    const post = await Post.findOne({
      where: { id: req.params.id },
      include: [
        {
          model: User,
          attributes: ['id', 'nickname'],
        },
        {
          model: Image,
        },
        {
          model: User,
          as: 'Likers',
          attributes: ['id'],
        },
      ],
    });
    return res.json(post);
  } catch (e) {
    console.error(e);
    return next(e);
  }
});
```
<br/>

### **🍳 게시글 삭제 라우터**

```ts
//게시글 삭제 라우터
router.delete('/:id', isLoggedIn, async (req, res, next) => {
  try {
    // 작업 대상이 있는지 없는지 먼저 검사
    const post = await Post.findOne({ where: { id: req.params.id } });
    if (!post) {
      return res.status(404).send('포스트가 존재하지 않습니다.');
    }
    await Post.destroy({ where: { id: req.params.id } });
    return res.send(req.params.id);
  } catch (e) {
    console.error(e);
    return next(e);
  }
});
```

<br/>

### **🍳 댓글과 관련된 라우터들**

```ts
//댓글과 관련된 라우터들
router.get('/:id/comments', async (req, res, next) => {
  try {
    const post = await Post.findOne({ where: { id: req.params.id } });
    if (!post) {
      return res.status(404).send('포스트가 존재하지 않습니다.');
    }
    const comments = await Comment.findAll({
      where: {
        PostId: req.params.id,
      },
      order: [['createdAt', 'ASC']],
      include: [
        {
          model: User,
          attributes: ['id', 'nickname'],
        },
      ],
    });
    return res.json(comments);
  } catch (err) {
    console.error(err);
    return next(err);
  }
});

router.post('/:id/comment', isLoggedIn, async (req, res, next) => {
  try {
    const post = await Post.findOne({ where: { id: req.params.id } });
    if (!post) {
      return res.status(404).send('포스트가 존재하지 않습니다.');
    }
    const newComment = await Comment.create({
      PostId: post.id,
      UserId: req.user!.id,
      content: req.body.content,
    });
    // await post.addComment(newComment.id);
    const comment = await Comment.findOne({
      where: {
        id: newComment.id,
      },
      include: [
        {
          model: User,
          attributes: ['id', 'nickname'],
        },
      ],
    });
    return res.json(comment);
  } catch (err) {
    console.error(err);
    return next(err);
  }
});
```

<br/>

### **🍳 좋아요 기능 관련 라우터**

```ts
// 좋아요 기능 관련 라우터들
router.post('/:id/like', isLoggedIn, async (req, res, next) => {
  try {
    const post = await Post.findOne({ where: { id: req.params.id } });
    if (!post) {
      return res.status(404).send('포스트가 존재하지 않습니다.');
    }
    await post.addLiker(req.user!.id);
    return res.json({ userId: req.user!.id });
  } catch (err) {
    console.error(err);
    next(err);
  }
});

router.delete('/:id/like', isLoggedIn, async (req, res, next) => {
  try {
    const post = await Post.findOne({ where: { id: req.params.id } });
    if (!post) {
      return res.status(404).send('포스트가 존재하지 않습니다.');
    }
    await post.removeLiker(req.user!.id);
    return res.json({ userId: req.user!.id });
  } catch (err) {
    console.error(err);
    next(err);
  }
});
```

<br/>

### **🍳 리트윗 라우터**

```ts
// 리트윗 라우터
router.post('/:id/retweet', isLoggedIn, async (req, res, next) => {
  try {
    const post = await Post.findOne({
      where: { id: req.params.id },
      include: [
        {
          model: Post,
          as: 'Retweet',
        },
      ],
    });
    if (!post) {
      return res.status(404).send('포스트가 존재하지 않습니다.');
    }
    if (req.user!.id === post.UserId || (post.Retweet && post.Retweet.UserId === req.user!.id)) {
      return res.status(403).send('자신의 글은 리트윗할 수 없습니다.');
    }
    const retweetTargetId = post.RetweetId || post.id;
    const exPost = await Post.findOne({
      where: {
        UserId: req.user!.id,
        RetweetId: retweetTargetId,
      },
    });
    if (exPost) {
      return res.status(403).send('이미 리트윗했습니다.');
    }
    const retweet = await Post.create({
      UserId: req.user!.id,
      RetweetId: retweetTargetId,
      content: 'retweet',
    });
    const retweetWithPrevPost = await Post.findOne({
      where: { id: retweet.id },
      include: [
        {
          model: User,
          attributes: ['id', 'nickname'],
        },
        {
          model: Post,
          as: 'Retweet',
          include: [
            {
              model: User,
              attributes: ['id', 'nickname'],
            },
            {
              model: Image,
            },
          ],
        },
      ],
    });
    return res.json(retweetWithPrevPost);
  } catch (err) {
    console.error(err);
    next(err);
  }
});
```

<br/>

## **💻 나머지 Routes 작성하기**

<br/>

### **🍳 routes/hashtag.ts**

```ts
import express from 'express';
import { Request } from 'express';
import Sequelize from 'sequelize';

import Hashtag from '../models/hashtag';
import Image from '../models/image';
import Post from '../models/post';
import User from '../models/user';

const router = express.Router();

router.get<any, any, any, { lastId: string; limit: string }>(
  '/:tag',
  async (req: Request<any, any, any, { lastId: string; limit: string }>, res, next) => {
    try {
      let where = {};
      if (parseInt(req.query.lastId, 10)) {
        where = {
          id: {
            [Sequelize.Op.lt]: parseInt(req.query.lastId, 10),
          },
        };
      }
      const posts = await Post.findAll({
        where,
        include: [
          {
            model: Hashtag,
            where: { name: decodeURIComponent(req.params.tag) },
          },
          {
            model: User,
            attributes: ['id', 'nickname'],
          },
          {
            model: Image,
          },
          {
            model: User,
            as: 'Likers',
            attributes: ['id'],
          },
          {
            model: Post,
            as: 'Retweet',
            include: [
              {
                model: User,
                attributes: ['id', 'nickname'],
              },
              {
                model: Image,
              },
            ],
          },
        ],
        order: [['createdAt', 'DESC']],
        limit: parseInt(req.query.limit, 10),
      });
      res.json(posts);
    } catch (err) {
      console.error(err);
      return next(err);
    }
  }
);

export default router;
```

여기서 보면 시퀄라이즈 모듈에 `Op`라는 것을 사용했다. 

시퀄라이즈는 자바스크립트 Symbol 연산자를 사용하여 복잡한 비교 연산을 지원한다. 

만약 `[Op.lt]: 10` 이면 `10` 미만의 자료들을 찾는다.

시퀄라이즈 버전 5까지는 Op가 시퀄라이즈의 메소드였지만, 버전 6부터는 하나의 변수로 따로 선언이 되어 있기 때문에 `Sequelize.Op` 이라고 하면 타입스크립트가 읽지 못한다.

> [Model Querying - Basics](https://sequelize.org/docs/v6/core-concepts/model-querying-basics/){:target="_blank"}
{:.prompt-info}

<br/>

### **🍳 routes/posts.ts**

강의에서 단수, 복수를 구분하여 코드를 작성하신다고 한다. 

restAPI 디자인 가이드로는 단수를 사용하라고 하지만, 대부분 사람들은 restAPI를 정확히 지키지 못한다고 한다. 

실제 현업에서도 이를 다 지키면서 하는 회사를 본 적이 없다고 한다.

그래서 완전히 규칙을 따르지 못할 바에는 내가 편한대로 쓰자! 라는 생각으로 단수와 복수를 나눈다고 하셨고, 데이터를 하나만 가져올 때와 여러 개를 가져올 때를 분리한다고 한다. 

> [REST API 제대로 알고 사용하기
등록일 2016.07.25 조회수 713526](https://meetup.nhncloud.com/posts/92){:target="_blank"}
{:.prompt-info}


```ts
import express from 'express';
import { Request } from 'express';
import Sequelize from 'sequelize';

import Image from '../models/image';
import Post from '../models/post';
import User from '../models/user';

const router = express.Router();

router.get<any, any, any, { lastId: string; limit: string }>(
  '/',
  async (req: Request<any, any, any, { lastId: string; limit: string }>, res, next) => {
    try {
      let where = {};
      if (parseInt(req.query.lastId, 10)) {
        where = {
          id: {
            [Sequelize.Op.lt]: parseInt(req.query.lastId, 10), // less than
          },
        };
      }
      const posts = await Post.findAll({
        where,
        include: [
          {
            model: User,
            attributes: ['id', 'nickname'],
          },
          {
            model: Image,
          },
          {
            model: User,
            as: 'Likers',
            attributes: ['id'],
          },
          {
            model: Post,
            as: 'Retweet',
            include: [
              {
                model: User,
                attributes: ['id', 'nickname'],
              },
              {
                model: Image,
              },
            ],
          },
        ],
        order: [['createdAt', 'DESC']], // DESC는 내림차순, ASC는 오름차순
        limit: parseInt(req.query.limit, 10),
      });
      return res.json(posts);
    } catch (err) {
      console.error(err);
      return next(err);
    }
  }
);

export default router;

```

### **🍳 index.ts 라우터 추가**

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

import { sequelize } from './models';
import userRouter from './routes/user';
import postRouter from './routes/post';
import postsRouter from './routes/posts';
import hashtagRouter from './routes/hashtag';

dotenv.config();
const app = express();
// 환경변수 설정
const prod: boolean = process.env.NODE_ENV === 'production'; // 배포용

app.set('port', prod ? process.env.PORT : 3065);
// 배포용이면 포트 자유자재로 바꿀 수 있도록, 개발용이면 3065로 고정

// 시퀄라이즈
sequelize
  .sync({ force: false })
  // true면 서버 재시작할 때마다 db 초기화됨(배포 때 대참사).
  // 나중에 개발할 때 테이블 컬럼 등 수정요소 있으면 true
  .then(() => {
    console.log('데이터베이스 연결 성공!');
  })
  .catch((err: Error) => {
    console.error(err);
  });

// 미들웨어 장착
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
    // 타입스크립트에서는 dotenv 인식 못 함. ! 를 통해 에러 없앰
    cookie: {
      httpOnly: true,
      secure: false, // https -> true
      domain: prod ? '.nodebird.com' : undefined,
      // domain: prod && '.nodebird.com'  // domain은 string | undefined 타입 형식으로 에러남(js에서는 문제 없음)
    },
    name: 'rnbck',
  })
);
app.use(passport.initialize());
app.use(passport.session());
app.use('/post', postRouter);
app.use('/posts', postsRouter);
app.use('/hashtag', hashtagRouter);
app.use('/user', userRouter);

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

## **📚 레퍼런스**

> [Node.js에 TypeScript 적용하기(feat. NodeBird)](https://www.inflearn.com/course/nodejs-typescript-%EC%A0%81%EC%9A%A9/dashboard){:target="_blank"}
{:.prompt-info}
