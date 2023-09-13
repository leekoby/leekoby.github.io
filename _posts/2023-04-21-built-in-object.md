---
layout: post
title: 3.4 노드 내장 객체 알아보기
author: admin
date: 2023-04-21 12:00:00 +900
lastmod: 2023-04-21 12:00:00 +900
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

# **🌈 노드 내장 객체 알아보기**

## **💻 global**

노드의 전역 객체

브라우저의 window같은 역할
모든 파일에서 접근 가능
window처럼 생략도 가능(console, require도 global의 속성)

✅ 결과

![global](https://github.com/leekoby/leekoby.github.io/assets/118284808/11993eef-8be9-48d0-9814-19d817cc02a9){: width="500" height="500" }


<br/>

## **💻 global 속성 공유**

global 속성에 값을 대입하면 다른 파일에서도 사용 가능

🔻globalA.js

```js
module.exports = () => global.message;
```

🔻globalB.js

```js
const A = require('./globalA');

global.message = '안녕하세요';
console.log(A());
```

```shell
$ node globalB
안녕하세요
```

<br/>

## **💻 console 객체**

>브라우저의 console 객체와 매우 유사
>
> - `console.time`, `console.timeEnd`: 시간 로깅
> - `console.error`: 에러 로깅
> - `console.log`: 평범한 로그
> - `console.dir`: 객체 로깅
> - `console.trace`: 호출스택 로깅

🔻console.js

```js
const string = 'abc';
const number = 1;
const boolean = true;
const obj = {
  outside: {
    inside: {
      key: 'value',
    },
  },
};
console.time('전체 시간');
console.log('평범한 로그입니다 쉼표로 구분해 여러 값을 찍을 수 있습니다');
console.log(string, number, boolean);
console.error('에러 메시지는 console.error에 담아주세요');

console.table([{ name: '제로', birth: 1994 }, { name: 'hero', birth: 1988}]);

console.dir(obj, { colors: false, depth: 2 });
console.dir(obj, { colors: true, depth: 1 });

console.time('시간 측정');
for (let i = 0; i < 100000; i++) {}
console.timeEnd('시간 측정');

function b() {
  console.trace('에러 위치 추적');
}
function a() {
  b();
}
a();

console.timeEnd('전체 시간');
```

✅ 결과

![console.js](https://github.com/leekoby/leekoby.github.io/assets/118284808/4bf993e8-b0f4-4757-80c9-838e4a4fb050){: width="500" height="500" }


<br/>

## **💻 console 예제 실행하기**

node console로 실행

![console 예제 실행](https://github.com/leekoby/leekoby.github.io/assets/118284808/9839147a-bfc4-4446-8a83-3300c77babaf){: width="500" height="500" }

<br/>

## **💻 타이머 메서드**

set 메서드에 clear 메서드가 대응됨

set 메서드의 리턴 값(아이디)을 clear 메서드에 넣어 취소

- `setTimeout(콜백 함수, 밀리초)`: 주어진 밀리초(1000분의 1초) 이후에 콜백 함수를 실행

- `setInterval(콜백 함수, 밀리초)`: 주어진 밀리초마다 콜백 함수를 반복 실행

- `setImmediate(콜백 함수)`: 콜백 함수를 즉시 실행

- `clearTimeout(아이디)`: setTimeout을 취소

- `clearInterval(아이디)`: setInterval을 취소

- `clearImmediate(아이디)`: setImmediate를 취소

<br/>

## **💻 타이머 예제**

setTimeout(콜백, 0)보다 `setImmediate` 권장

🔻timer.js

```js
const timeout = setTimeout(() => {
  console.log('1.5초 후 실행');
}, 1500);

const interval = setInterval(() => {
  console.log('1초마다 실행');
}, 1000);

const timeout2 = setTimeout(() => {
  console.log('실행되지 않습니다');
}, 3000);

setTimeout(() => {
  clearTimeout(timeout2);
  clearInterval(interval);
}, 2500);

const immediate = setImmediate(() => {
  console.log('즉시 실행');
});

const immediate2 = setImmediate(() => {
  console.log('실행되지 않습니다');
});

clearImmediate(immediate2);
```

✅ 결과

![타이머 예제 결과](https://github.com/leekoby/leekoby.github.io/assets/118284808/0dfa775e-c02b-4ae5-9b3f-ef513b49c0a7){: width="500" height="500" }

- setTimeout(콜백, 0)은 사용하지 않는 것을 권장

<br/>

## **💻 filename, dirname**

>filename: 현재 파일 경로
>dirname: 현재 폴더(디렉터리) 경로

🔻filename.js
```js
console.log(__filename);
console.log(__dirname);
```
```shell
$ node filename.js
C:\Users\zerochofilename.js
C:\Users\zerocho
```

<br/>

## **💻 module, exports**

module.exports 외에도 exports로 모듈을 만들 수 있음

- 모듈 예제의 var.js를 다음과 같이 바꾼 후 실행

- 동일하게 동작함

- 동일한 이유는 module.exports와 exports가 참조 관계이기 때문

- exports에 객체의 속성이 아닌 다른 값을 대입하면 참조 관계가 깨짐

🔻var.js

```js
exports.odd = '홀수입니다';
exports.even = '짝수입니다';
```

<br/>

## **💻 this**

노드에서 this를 사용할 때 주의점이 있음

- 최상위 스코프의 this는 module.exports를 가리킴

- 그 외에는 브라우저의 자바스크립트와 동일

- 함수 선언문 내부의 this는 global(전역) 객체를 가리킴

🔻this.js

```js
console.log(this);
console.log(this === module.exports);
console.log(this === exports);

function whatIsThis() {
  console.log('function', this === exports, this === global);
}
whatIsThis();
```

<br/>

## **💻 require의 특성**

몇 가지 알아둘 만한 속성이 있음

- require가 제일 위에 올 필요는 없음

- require.cache에 한 번 require한 모듈에 대한 캐싱 정보가 들어있음.

- require.main은 노드 실행 시 첫 모듈을 가리킴.

🔻require.js

```js
console.log('require가 가장 위에 오지 않아도 됩니다.');

module.exports = '저를 찾아보세요.';

require('./var');

console.log('require.cache입니다.');
console.log(require.cache);
console.log('require.main입니다.');
console.log(require.main === module);
console.log(require.main.filename);
```

<br/>

## **💻 순환참조**

두 개의 모듈이 서로를 require하는 상황을 조심해야 함

- Dep1이 dep2를 require하고, dep2가 dep1을 require 함.

- Dep1의 module.exports가 함수가 아니라 빈 객체가 됨(무한 반복을 막기 위해 의도됨)

- 순환참조하는 상황이 나오지 않도록 하는 게 좋음.

🔻dep1.js

```js
const dep2 = require('./dep2');
console.log('require dep2', dep2);
module.exports = () => {
  console.log('dep2', dep2);
};
```


🔻dep2.js

```js
const dep1 = require('./dep1');
console.log('require dep1', dep1);
module.exports = () => {
  console.log('dep1', dep1);
};
```

🔻dep-run.js

```js
const dep1 = require('./dep1');
const dep2 = require('./dep2');

dep1();
dep2();
```

<br/>

## **💻 process**

현재 실행중인 노드 프로세스에 대한 정보를 담고 있음

컴퓨터마다 출력값이 PPT와 다를 수 있음

![process](https://github.com/leekoby/leekoby.github.io/assets/118284808/cb3dcbcf-f9b7-45d8-a3e1-ab36dc18aa83){: width="500" height="500" }

<br/>

## **💻 process.env**

시스템 환경 변수들이 들어있는 객체

- 비밀키(데이터베이스 비밀번호, 서드파티 앱 키 등)를 보관하는 용도로도 쓰임
- 
- 환경 변수는 process.env로 접근 가능

```js
const secretId= process.env.SECRET_ID;
const secretCode= process.env.SECRET_CODE;
```

- 일부 환경 변수는 노드 실행 시 영향을 미침

- 예시) NODE_OPTIONS(노드 실행 옵션), UV_THREADPOOL_SIZE(스레드풀 개수)
max-old-space-size는 노드가 사용할 수 있는 메모리를 지정하는 옵션

```js
NODE_OPTIONS=--max-lod-space-size=8192
UV_THREADPOOL_SIZE=8
```


<br/>

## **💻 process.nextTick(콜백)**

**이벤트 루프가 다른 콜백 함수들보다 nextTick의 콜백 함수를 우선적으로 처리함**

- 너무 남용하면 다른 콜백 함수들 실행이 늦어짐

- 비슷한 경우로 promise가 있음(nextTick처럼 우선순위가 높음)

- 아래 예제에서 setImmediate, setTimeout보다 promise와 nextTick이 먼저 실행됨

🔻nextTick.js
```js
setImmediate(() => {
  console.log('immediate');
});
process.nextTick(() => {
  console.log('nextTick');
});
setTimeout(() => {
  console.log('timeout');
}, 0);
Promise.resolve().then(() => console.log('promise'));
```

```shell
$ node nextTick
nextTick
promise
timeout
immediate
```

![태스크와 마이크로태스크](https://github.com/leekoby/leekoby.github.io/assets/118284808/34b53ba4-a6d2-4c97-8ee5-35031de29ce2){: width="500" height="500" }


<br/>

## **💻 process.exit(코드)**

현재의 프로세스를 멈춤

- 코드가 없거나 0이면 정상 종료

- 이외의 코드는 비정상 종료를 의미함

🔻exit.js

```js
let i = 1;
setInterval(() => {
  if (i === 5) {
    console.log('종료!');
    process.exit();
  }
  console.log(i);
  i += 1;
}, 1000);
```

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}