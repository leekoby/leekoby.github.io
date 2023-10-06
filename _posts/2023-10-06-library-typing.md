---
layout: post
title: 라이브러리 타이핑하기
author: admin
date: 2023-10-06 00:00:00 +900
lastmod: 2023-10-06 00:00:00 +900
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

# **🌈 라이브러리 타이핑하기**

## **💻 타입 없는 라이브러리 타이핑하기**

본 강의에서는 프론트엔드는 다루지 않기 때문에 대체할만 것들을 직접 작성한다.

타입스크립트를 작성할 때 크게 두 가지 방법이 있다. 

1. 첫 번째는 리덕스처럼 처음부터 타입스크립트로 코드를 작성하는 방법
2. `JS`로 먼저 코드 작성 후 타입스크립트를 붙이는 방식이다.

두 번째 방식으로 타입스크립트를 작성할 것이고, `@types/passport-local` 라이브러리를 직접 코딩하면서 테스트해볼 것이다.

그래서 기존에 설치했던 이 모듈은 삭제. 

```sh
npm rm @types/passport-local
```

타이핑은 /back/types에서 할 것이다. 

tsconfig의 typeRoots를 "./types"로 설정했기 때문이다.

/back/types 디렉토리에서 `passport-local.d.ts` 파일을 생성한다.

back/passport/local.ts의 코드를 보면서, 내가 사용하는 코드 부분에만 타이핑을 작성하면 된다. 

```ts
import { Strategy as PassportStrategy } from 'passport';
import express = require('express');

declare module 'passport-local' {
  // delcare module은 실제 모듈의 이름과 같게 해야 에러나지 않음

  export interface IVerifyOptions {
    // export로 확장성 고려
    // 인터페이스 이름은 실제 모듈과 이름을 같게 만듦
    [key: string]: any;
  }

  export interface IStrategyOptions {
    usernameField: string;
    passwordField: string;
  }

  export interface Done {
    (error: Error | null, user?: any, options?: IVerifyOptions): void;
  }

  export interface VerifyFunction {
    (username: string, password: string, done: Done): void | Promise<any>;
  }
  // delcare module은 실제 모듈의 이름과 같게 해야 에러나지 않음
  export class Strategy extends PassportStrategy {
    constructor(options: IStrategyOptions, verify: VerifyFunction);
   }
}

```

다 작성을 했는데 `local.ts`의 `done(err)` 부분에서 에러가 난다.

unkown은 `'Error | Null'` 에 할당될 수 없다는 문구가 뜬다. 

그래서 catch 부분에서 err를 any로 타입을 정의해서 문제를 해결했지만, @types/passport-local을 참조해보니, done 부분의 매개변수들을 any로 설정한 것을 볼 수 있었다. 

그래서 나도 any로 수정했다.

```ts
 export interface Done {
    (err: any, user?: any, options?: IVerifyOptions): void;
  }
```

<br/>

---

<br/>

## **💻 다양한 케이스를 위한 오버로딩**

오버로딩? 같은 이름의 함수를 매개 변수에 따라 다르게 사용하는 것

local.ts 파일에 해당하는 타입 정의를 위해 직접 타이핑을 했었다. 그런데 만약 local 내의 코드를 수정하거나 확장해야 하는 경우라면 어떻게 해야 할까?

🔻 passport/local.ts

```ts
import passport from 'passport';
import bcrypt from 'bcrypt';
import { Strategy } from 'passport-local';
import User from '../models/user';

export default () => {
  passport.use(
    'local',
    new Strategy(
      {
        usernameField: 'userId',
        passwordField: 'password',
        session: false, // 세션에 로그인 정보 저장 안할 때 false
        passReqToCallback: true, // req 객체를 passport 인증 시 활용
      },
      async (userId, password, done) => {
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
      }
    )
  );
};
```

기존 코드에서 strategy 안에 session과 passReqToCallback 속성을 추가했다.

passReqToCallback 속성을 true로 설정하게 되면 req객체를 passport 인증시 활용해야 하기 때문에 콜백함수에 req 매개변수도 추가되어야 한다. 

여기서 문제가 발생한다.

🔻 passport.local.d.ts

```ts
  export interface IStrategyOptions {
    usernameField: string;
    passwordField: string;
  }
```

우리가 사용하는 인터페이스에서는 추가되는 옵션들이 없기 때문에 추가해보면 

```ts
  export interface IStrategyOptions {
    usernameField: string;
    passwordField: string;
    session?: boolean;
    passReqToCallback?: boolean;
  }
```

우리가 사용하는 인터페이스에서는 passReqToCallback 속성을 boolean로 하고 클래스 부분을 수정해봤다. 

```ts
  export class Strategy extends PassportStrategy {
    constructor(options: IStrategyOptions | IStrategyOptionsWithRequest, verify: VerifyFunction | VerifyFunctionWithRequest)
    // | 연산자 사용하면 잘못된 결과가 나오거나 에러가 발생할 수 있음
  }
```

위의 경우 `|`를 통해 유니언타입으로 설정했는데 경우의 수가 다양해져 우리가 원하는 타입이 아닌 다른 결과가 나오거나 에러가 발생할 수 있다. 

때문에 interface에서 boolean이 아닌 `true` 와 `false`로 분리된 인터페이스를 설정해주는 편이 좋다.

속성의 옵션을 그때그때 다르게 사용하게 되는 경우 그때마다 작업해야 하는 요소가 생기게 되는 것이고, 이는 매우 비효율적인 방법이다. 이렇게 모듈들은 가변적인 매개변수를 받을 수 있고, 이런 다양한 케이스에 맞는 타이핑을 한다.

```ts
import { Strategy as PassportStrategy } from 'passport';
import express = require('express');

declare module 'passport-local' {
  // delcare module은 실제 모듈의 이름과 같게 해야 에러나지 않음

  export interface IVerifyOptions {
    // export로 확장성 고려
    // 인터페이스 이름은 실제 모듈과 이름을 같게 만듦
    [key: string]: any;
  }

  export interface IStrategyOptions {
    usernameField: string;
    passwordField: string;
    session?: boolean;
    passReqToCallback?: false;
  }

  export interface Done {
    (err: any, user?: any, options?: IVerifyOptions): void;
  }

  interface IStrategyOptionsWithRequest {
    usernameField?: string;
    passwordField?: string;
    session?: boolean;
    passReqToCallback: true;
  }

  // req를 가장 앞 매개변수에 사용해야 하는 경우가 필요할 때, 오버로딩할 수 있는 인터페이스 생성
  interface VerifyFunctionWithRequest {
    (req: express.Request, username: string, password: string, done: Done): void | Promise<any>;
  }

  export interface VerifyFunction {
    (username: string, password: string, done: Done): void | Promise<any>;
  }
  // delcare module은 실제 모듈의 이름과 같게 해야 에러나지 않음
  export class Strategy extends PassportStrategy {
    // constructor(options: IStrategyOptions | IStrategyOptionsWithRequest, verify: VerifyFunction | VerifyFunctionWithRequest)
    // | 연산자 사용하면 잘못된 결과가 나오거나 에러가 발생할 수 있음
    constructor(options: IStrategyOptionsWithRequest, verify: VerifyFunctionWithRequest);
    constructor(options: IStrategyOptions, verify: VerifyFunction);
  }
}

```

<br/>

---

<br/>

## **📚 레퍼런스**

> [Node.js에 TypeScript 적용하기(feat. NodeBird)](https://www.inflearn.com/course/nodejs-typescript-%EC%A0%81%EC%9A%A9/dashboard){:target="_blank"}
{:.prompt-info}