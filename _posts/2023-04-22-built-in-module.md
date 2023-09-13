---
layout: post
title: 3.5 노드 내장 모듈 사용하기
author: admin
date: 2023-04-22 00:00:00 +900
lastmod: 2023-04-22 00:00:00 +900
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

# **🌈  노드 내장 모듈 사용하기**

## **💻 os**

운영체제의 정보를 담고 있음

모듈은 require로 가져옴(내장 모듈이라 경로 대신 이름만 적어줘도 됨)!

🔻os.js

```js
const os = require('os');

console.log('운영체제 정보---------------------------------');
console.log('os.arch():', os.arch());
console.log('os.platform():', os.platform());
console.log('os.type():', os.type());
console.log('os.uptime():', os.uptime());
console.log('os.hostname():', os.hostname());
console.log('os.release():', os.release());

console.log('경로------------------------------------------');
console.log('os.homedir():', os.homedir());
console.log('os.tmpdir():', os.tmpdir());

console.log('cpu 정보--------------------------------------');
console.log('os.cpus():', os.cpus());
console.log('os.cpus().length:', os.cpus().length);

console.log('메모리 정보-----------------------------------');
console.log('os.freemem():', os.freemem());
console.log('os.totalmem():', os.totalmem());
```

✅ 결과

```shell
$ node os
운영체제 정보---------------------------------
os.arch(): x64
os.platform(): win32
os.type(): Windows_NT
os.uptime(): 53354 
os.hostname(): DESKTOP-RRANDNC
os.release(): 10.0.18362
경로---------------------------------
os.homedir(): C:\Users\zerocho
os.tmpdir(): C:\Users\zerocho\AppData\Local\Temp
cpu 정보---------------------------------
os.cpus(): [ { model: 'Intel(R) Core(TM) i5-9400F CPU @ 2.90GHz',
    speed: 2904,
    times: { user: 970250, nice: 0, sys: 1471906, idle: 9117578, irq: 359109 } },
    // 다른 코어가 있다면 나머지 코어의 정보가 나옴
 ]
os.cpus().length: 6
메모리 정보---------------------------------
os.freemem(): 23378612224
os.totalmem(): 34281246720
```

<br/>

## **💻 os 모듈 메서드**

- `os.arch()`: process.arch와 동일

- `os.platform()`: process.platform과 동일

- `os.type()`: 운영체제의 종류

- `os.uptime()`: 운영체제 부팅 이후 흐른 시간(초)

- `os.hostname()`: 컴퓨터의 이름

- `os.release()`: 운영체제의 버전

- `os.homedir()`: 홈 디렉터리 경로

- `os.tmpdir()`: 임시 파일 저장 경로

- `os.cpus()`: 컴퓨터의 코어 정보

- `os.freemem()`: 사용 가능한 메모리(RAM)

- `os.totalmem()`: 전체 메모리 용량

<br/>

## **💻 path**

폴더와 파일의 경로를 쉽게 조작하도록 도와주는 모듈

- 운영체제별로 경로 구분자가 다름(Windows: ‘\’, POSIX: ‘/’)

🔻path.js

```js
const path = require('path');

const string = __filename;

console.log('path.sep:', path.sep);
console.log('path.delimiter:', path.delimiter);
console.log('------------------------------');
console.log('path.dirname():', path.dirname(string));
console.log('path.extname():', path.extname(string));
console.log('path.basename():', path.basename(string));
console.log('path.basename - extname:', path.basename(string, path.extname(string)));
console.log('------------------------------');
console.log('path.parse()', path.parse(string));
console.log('path.format():', path.format({
  dir: 'C:\Users\Seokahi\Desktop\노드',
  name: 'path',
  ext: '.js',
}));
console.log('path.normalize():', path.normalize('C:\Users\Seokahi\Desktop\노드\path.js'));
console.log('------------------------------');
console.log('path.isAbsolute(C:\\):', path.isAbsolute('C:\\'));
console.log('path.isAbsolute(./home):', path.isAbsolute('./home'));
console.log('------------------------------');
console.log('path.relative():', path.relative('C:\Users\Seokahi\Desktop\노드\path.js', 'C:\\'));
console.log('path.join():', path.join(__dirname, '..', '..', '/users', '.', '/노드'));
console.log('path.resolve():', path.resolve(__dirname, '..', 'users', '.', '/노드'));
```

✅ 결과

```shell
$ node path
path.sep: \
path.delimiter: ;
------------------------------
path.dirname(): C:\Users\zerocho 
path.extname(): .js
path.basename(): path.js
path.basename - extname: path
------------------------------
path.parse() {
  root:  'C:\',
  dir:  'C:\Users\zerocho',
  base:  'path.js',
  ext:  '.js',
  name:  'path'
}
path.format(): C:\users\zerocho\path.js
path.normalize(): C:\users\zerocho\path.js
------------------------------
path.isAbsolute(C:\): true
path.isAbsolute(./home): false
------------------------------
path.relative(): ..\..\..
path.join(): C:\Users\zerocho
path.resolve(): C:\zerocho
```

- `path.sep`: 경로의 구분자. 윈도는 \, POSIX는 /
  - `POSIX` : MAC + LINUX

- `path.delimiter`: 환경 변수의 구분자. 
  - `process.env.PATH`를 입력하면 여러 개의 경로가 이 구분자로 구분되어 있다.
  - 윈도는 세미콜론(;)이고, POSIX는 콜론(:)

- `path.dirname(경로)`: 파일이 위치한 폴더 경로

- `path.extname(경로)`: 파일의 확장자

- `path.basename(경로, 확장자)`: 파일의 이름(확장자 포함)을 표시
  - 파일의 이름만 표시하고 싶다면 basename의 두 번째 인수로 파일의 확장자를 넣으면 된다.

- `path.parse(경로)`: 파일 경로를 root, dir, base, ext, name으로 분리

- `path.format(객체)`: path.parse()한 객체를 파일 경로로 합친다.

- `path.normalize(경로)`: /나 \를 실수로 여러 번 사용했거나 혼용했을 때 정상적인 경로로 변환

- `path.isAbsolute(경로)`: 파일의 경로가 절대경로인지 상대경로인지를 true나 false로 알린다.

- `path.relative(기준경로, 비교경로)`: 경로를 두 개 넣으면 첫 번째 경로에서 두 번째 경로로 가는 방법을 알려줌

- `path.join(경로, …)`: 여러 인수를 넣으면 하나의 경로로 합칩니다. 상대경로인 ..(부모 디렉터리)과 .(현 위치)도 알아서 처리

- `path.resolve(경로, …)`: path.join()과 비슷하지만 차이가 있습니다. 차이점은 다음에 나오는 Note에서 설명

> **<span style="color:#3366ff">join과 resolve의 차이</span>**
> 
> **<span style="color:#ff6600">resolve는 `/`를 만나면 절대 경로로 처리, join은 상대경로로 처리</span>**
> 
> 상대 경로 : 현재 파일 기준. 같은 경로면 점 하나(.), 한 단계 상위 경로면 점 두개 (..)
> 
> 절대 경로 : 루트 폴더나 노드 프로세스가 실행되는 위치 기준 
>
> ```
> path.join('/a', '/b', 'c'); /* 결과: /a/b/c/ */
> path.resolve('/a', '/b', 'c'); /* 결과: /b/c */
> ```
{:.prompt-info }


**`\\`와 `\`차이** 

`\`는 윈도 경로 구분자, `\\`는 자바스크립트 문자열 안에서 사용(`\`가 특수문자라 `\\`로 이스케이프를 해준 것)

<br/>

**윈도에서 POSIX path를 쓰고 싶다면 : path.posix 객체 사용**

- POSIX에서 윈도우 path를 쓰고 싶다면 : path.win32 객체 사용

<br/>

## **💻 url**

인터넷 주소를 쉽게 조작하도록 도와주는 모듈

- url 처리에 크게 두 가지 방식이 있음(기존 노드 방식 vs WHATWG 방식)

- 아래 그림에서 가운데 주소를 기준으로 위쪽은 기존 노드 방식, 아래쪽은 WHATWG 방식

![WHATWG와 노드 주소 체계](https://github.com/leekoby/leekoby.github.io/assets/118284808/c33a823d-21a4-4cd6-ac07-b69286653227){: width="500" height="500" }

🔻url.js

```js
const url = require('url'); ---- ➊

const { URL } = url;
const myURL = new URL('http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor');
console.log('new URL():', myURL);
console.log('url.format():', url.format(myURL));
```
```shell
콘솔

$ node url
new URL(): URL {
  href: 'http://www.gilbut.co.kr/book/bookList.aspx?sercate 1 = 001001000 #anchor',
  origin: 'http://www.gilbut.co.kr' ,
  protocol: 'http:',
  username: '',
  password: '',
  host: 'www.gilbut.co.kr',
  hostname: 'www.gilbut.co.kr',
  port: '',
  pathname: '/book/bookList.aspx',
  search: '?sercate 1 = 001001000 ',
  searchParams: URLSearchParams { 'sercate 1 ' => '001001000 ' },
  hash: '#anchor'
}
url.format(): http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor
```

<br/>

## **💻 url 모듈 메서드**

기존 노드 방식 메서드

- `url.parse(주소)`: 주소를 분해 
  
  - WHATWG 방식과 비교하면 username과 password대신 auth 속성이 있고, searchParams 대신 query가 있다.

- `url.format(객체)`: WHATWG 방식의 url과 기존 노드의 url 모두 사용할 수 있다. 

  - 분해되었던 url 객체를 다시 원래 상태로 조립

<br/>

## **💻 searchParams**

WHATWG 방식에서 쿼리스트링(search) 부분 처리를 도와주는 객체

?page=3&limit=10&category=nodejs&category=javascript 부분

🔻searchParams.js

```js
const { URL } = require('url');

const myURL = new URL('http://www.gilbut.co.kr/?page=3&limit=10&category=nodejs&category=javascript');
console.log('searchParams:', myURL.searchParams);
console.log('searchParams.getAll():', myURL.searchParams.getAll('category'));
console.log('searchParams.get():', myURL.searchParams.get('limit'));
console.log('searchParams.has():', myURL.searchParams.has('page'));

console.log('searchParams.keys():', myURL.searchParams.keys());
console.log('searchParams.values():', myURL.searchParams.values());

myURL.searchParams.append('filter', 'es3');
myURL.searchParams.append('filter', 'es5');
console.log(myURL.searchParams.getAll('filter'));

myURL.searchParams.set('filter', 'es6');
console.log(myURL.searchParams.getAll('filter'));

myURL.searchParams.delete('filter');
console.log(myURL.searchParams.getAll('filter'));

console.log('searchParams.toString():', myURL.searchParams.toString());
myURL.search = myURL.searchParams.toString();
```

- `getAll(키)`: 키에 해당하는 모든 값들을 가져옵니다. category 키에는 두 가지 값, 즉 nodejs와 javascript의 값이 들어 있습니다.

- `get(키)`: 키에 해당하는 첫 번째 값만 가져옵니다.

- `has(키)`: 해당 키가 있는지 없는지를 검사합니다.

- `keys()`: searchParams의 모든 키를 반복기(iterator, ES2015 문법) 객체로 가져옵니다.

- `values()`: searchParams의 모든 값을 반복기 객체로 가져옵니다.

- `append(키, 값)`: 해당 키를 추가합니다. 같은 키의 값이 있다면 유지하고 하나 더 추가합니다.

- `set(키, 값)`: append와 비슷하지만 같은 키의 값들을 모두 지우고 새로 추가합니다.

- `delete(키)`: 해당 키를 제거합니다.

- `toString()`: 조작한 searchParams 객체를 다시 문자열로 만듭니다. 이 문자열을 search에 대입하면 주소 객체에 반영됩니다.

<br/>

## **💻 querystring**

기존 노드 방식에서는 url querystring을 querystring 모듈로 처리

- `querystring.parse(쿼리)`: url의 query 부분을 자바스크립트 객체로 분해해줍니다.

- `querystring.stringify(객체)`: 분해된 query 객체를 문자열로 다시 조립해줍니다.


🔻 querystring.js

```js
const url = require('url');
const querystring = require('querystring');

const parsedUrl = url.parse('http://www.gilbut.co.kr/?page=3&limit=10&category=nodejs&category=javascript');
const query = querystring.parse(parsedUrl.query);
console.log('querystring.parse():', query);
console.log('querystring.stringify():', querystring.stringify(query));
```

![querystring](https://github.com/leekoby/leekoby.github.io/assets/118284808/f0887432-4cba-4ba0-bf45-cc88394b2fea){: width="500" height="500" }


<br/>

## **💻 dns**

DNS를 다룰 때 사용하는 모듈

주로 도메인을 통해 IP나 기타 DNS 정보를 얻고자 할 때 사용

🔻dns.mjs

```js
import dns from 'dns/promises';

const ip = await dns.lookup('gilbut.co.kr');
console.log('IP', ip);

const a = await dns.resolve('gilbut.co.kr', 'A');
console.log('A', a);

const mx = await dns.resolve('gilbut.co.kr', 'MX');
console.log('MX', mx);

const cname = await dns.resolve('www.gilbut.co.kr', 'CNAME');
console.log('CNAME', cname);

const any = await dns.resolve('gilbut.co.kr', 'ANY');
console.log('ANY', any);
```

```shell
$ node dns.mjs
IP { address: ' 49 . 236 . 151 . 220 ', family: 4 }
A [ ' 49 . 236 . 151 . 220 ' ]
MX [
  { exchange: 'alt 2 .aspmx.l.google.com', priority: 5 },
  { exchange: 'aspmx 3 .googlemail.com', priority: 10 },
  { exchange: 'aspmx 2 .googlemail.com', priority: 10 },
  { exchange: 'aspmx.l.google.com', priority: 1 },
  { exchange: 'alt 1 .aspmx.l.google.com', priority: 5 }
]
CNAME [ 'slb- 1088813 .ncloudslb.com' ]
ANY [
  { address: ' 49 . 236 . 151 . 220 ', ttl: 14235, type: 'A' },
  { value: 'ns 1 - 2 .ns-ncloud.com', type: 'NS' },
  { value: 'ns 1 - 1 .ns-ncloud.com', type: 'NS' },
  {
    nsname: 'ns 1 - 1 .ns-ncloud.com',
    hostmaster: 'ns 1 - 2 .ns-ncloud.com',
     serial: 32,
    refresh: 21600,
    retry: 1800,
    expire: 1209600,
    minttl: 300,
    type: 'SOA'
  }
]
```

<br/>

## **💻 단방향 암호화(crypto)**

암호화는 가능하지만 복호화는 불가능

- `암호화`: 평문을 암호로 만듦

- `복호화`: 암호를 평문으로 해독

단방향 암호화의 대표 주자는 해시 기법

- 문자열을 고정된 길이의 다른 문자열로 바꾸는 방식

- abcdefgh 문자열 -> qvew

<br/>

## **💻 Hash 사용하기 (sha512)**

- `createHash(알고리즘)`: 사용할 해시 알고리즘을 넣는다. 
  
  - md5, sha1, sha256, sha512 등이 가능하지만, md5와 sha1은 이미 취약점이 발견되었다. 

  - 현재는 sha512 정도로 충분하지만, 나중에 sha512마저도 취약해지면 더 강화된 알고리즘으로 바꿔야 한다.

- update(문자열): 변환할 문자열을 넣는다.

- digest(인코딩): 인코딩할 알고리즘을 넣는다. 

  - base64, hex, latin1이 주로 사용되는데, 그중 base64가 결과 문자열이 가장 짧아서 애용된다.
  
  - 결과물로 변환된 문자열을 반환

![해시함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/22ef9777-f075-4b35-bd2e-c341c63fbee1){: width="500" height="500" }

🔻 hash.js

```js
const crypto = require('crypto');

console.log('base64:', crypto.createHash('sha512').update('비밀번호').digest('base64'));
console.log('hex:', crypto.createHash('sha512').update('비밀번호').digest('hex'));
console.log('base64:', crypto.createHash('sha512').update('다른 비밀번호').digest('base64'));
```

```shell
$ node hash
base64: dvfV6nyLRRt3NxKSlTHOkkEGgqW2HRtfu19Ou/psUXvwlebbXCboxIPmDYOFRIpqav2eUTBFuHaZri5x+usy1g==
hex: 76f7d5ea7c8b451b773712929531ce92410682a5b61d1b5fbb5f4ebbfa6c517bf095e6db5c26e8c483e60d8385448a6a6afd9e513045b87699ae2e71faeb32d6
base64: cx49cjC8ctKtMzwJGBY853itZeb6qxzXGvuUJkbWTGn5VXAFbAwXGEOxU2Qksoj+aM2GWPhc1O7mmkyohXMsQw==
```

<br/>

## **💻 pbkdf2**

컴퓨터의 발달로 기존 암호화 알고리즘이 위협받고 있음

- sha512가 취약해지면 sha3으로 넘어가야함

- 현재는 pbkdf2나, bcrypt, scrypt 알고리즘으로 비밀번호를 암호화

- Node는 pbkdf2와 scrypt 지원

![pbkdf2](https://github.com/leekoby/leekoby.github.io/assets/118284808/7b582690-d18d-4771-a29f-83c941daf082){: width="500" height="500" }

<br/>

## **💻 pbkdf2 예제**

- crypto.randomBytes로 64바이트 문자열 생성 -> `salt` 역할

- pbkdf2 인수로 순서대로 비밀번호, salt, 반복 횟수, 출력 바이트, 알고리즘

- 반복 횟수를 조정해 암호화하는 데 1초 정도 걸리게 맞추는 것이 권장됨

🔻pbkdf2.js

```js
const crypto = require('crypto');

crypto.randomBytes(64, (err, buf) => {
  const salt = buf.toString('base64');
  console.log('salt:', salt);
  crypto.pbkdf2('비밀번호', salt, 100000, 64, 'sha512', (err, key) => {
    console.log('password:', key.toString('base64'));
  });
});
```

```shell
$ node pbkdf2
salt: OnesIj8wznyKgHva1fmulYAgjf/OGLmJnwfy8pIABchHZF/Wn2AM2Cn/9170Y1AdehmJ0E5CzLZULps+daF6rA==
password: b4/FpSrZulVY28trzNXsl4vVfhOKBPxyVAvwnUCWvF1nnXS1zsU1Paq2p68VwUfhB0LDD44hJOf+tLe3HMLVmQ==
```

<br/>

## **💻 양방향 암호화(대칭형)**

대칭형 암호화(암호문 복호화 가능)

- Key가 사용됨 (Key 관리가 매우 중요)

- 암호화할 때와 복호화 할 때 같은 Key를 사용해야 함

🔻cipher.js

```js
const crypto = require('crypto');

const algorithm = 'aes-256-cbc';
const key = 'abcdefghijklmnopqrstuvwxyz123456';
const iv = '1234567890123456';

const cipher = crypto.createCipheriv(algorithm, key, iv);
let result = cipher.update('암호화할 문장', 'utf8', 'base64');
result += cipher.final('base64');
console.log('암호화:', result);

const decipher = crypto.createDecipheriv(algorithm, key, iv);
let result2 = decipher.update(result, 'base64', 'utf8');
result2 += decipher.final('utf8');
console.log('복호화:', result2);
```

## **💻 양방향 암호화 메서드**

- `crypto.createCipheriv(알고리즘, 키, iv)`: 암호화 알고리즘과 키, iv를 넣는다. 

  - 암호화 알고리즘은 aes-256-cbc를 사용했으며, 다른 알고리즘을 사용해도 된다. 
  
  - aes-256-cbc 알고리즘의 경우 키는 32바이트여야 하고, iv는 16바이트여야 한다. 
  
  - iv는 암호화할 때 사용하는 초기화 벡터를 의미하지만, 이 책에서 설명하기에는 내용이 많으므로 AES 암호화를 따로 공부하는 것이 좋다. 

  - 사용 가능한 알고리즘 목록은 crypto.getCiphers()를 호출하면 볼 수 있습니다.

- `cipher.update(문자열, 인코딩, 출력 인코딩)`: 암호화할 대상과 대상의 인코딩, 출력 결과물의 인코딩을 넣는다. 
  
  - 보통 문자열은 utf8 인코딩을, 암호는 base64를 많이 사용

- `cipher.final(출력 인코딩)`: 출력 결과물의 인코딩을 넣으면 암호화가 완료

- `crypto.createDecipheriv(알고리즘, 키, iv)`: 복호화할 때 사용
  
  -  암호화할 때 사용했던 알고리즘과 키, iv를 그대로 넣어야 한다.

- `decipher.update(문자열, 인코딩, 출력 인코딩)`: 암호화된 문장, 그 문장의 인코딩, 복호화할 인코딩을 넣는다. 
  - createCipheriv의 update()에서 utf8, base64 순으로 넣었다면 createDecipheriv의 update()에서는 base64, utf8 순으로 넣으면 된다.

- `decipher.final(출력 인코딩)`: 복호화 결과물의 인코딩을 넣는다.

<br/>

## **💻 util**

각종 편의 기능을 모아둔 모듈

- `deprecated`와 `promisify`가 자주 쓰임

🔻util.js

```js
const util = require('util');
const crypto = require('crypto');

const dontUseMe = util.deprecate((x, y) => {
  console.log(x + y);
}, 'dontUseMe 함수는 deprecated되었으니 더 이상 사용하지 마세요!');
dontUseMe(1, 2);

const randomBytesPromise = util.promisify(crypto.randomBytes);
randomBytesPromise(64)
  .then((buf) => {
    console.log(buf.toString('base64'));
  })
  .catch((error) => {
    console.error(error);
  });
```

✅ 결과

```shell
$ node util
3
(node:7264) DeprecationWarning: dontUseMe 함수는 deprecated되었으니 더 이상 사용하지 마세요!
(Use `node --trace-deprecation ...` to show where the warning was created)
60b4RQbrx1j130x4r95fpZac9lmcHyitqwAm8gKsHQKF8tcNhvcTfW031XaQqHlRKzaVkcENmIV25fDVs3SB7g==
```

<br/>

## **💻 util의 메서드**

- `util.deprecate`: 함수가 deprecated 처리되었음을 알려줌

  - 첫 번째 인자로 넣은 함수를 사용했을 때 경고 메시지가 출력

  - 두 번째 인자로 경고 메시지 내용을 넣으면 된다. 함수가 조만간 사라지거나 변경될 때 알려줄 수 있어 유용합니다.

- `util.promisify`: 콜백 패턴을 프로미스 패턴으로 바꿔줌

  - 바꿀 함수를 인자로 제공하면 된다. 이렇게 바꾸어두면 async/await 패턴까지 사용할 수 있다.

  - 단, 콜백이 `(error, data) =>{}` 형식이어야 한다.
  
- 프로미스를 콜백으로 바꾸는 `util.callbackify`도 있지만 자주 사용되지는 않는다.

<br/>

> **<span style="color:#3366ff"> deprecated</span>**
> 
> deprecated는 프로그래밍 용어로, ‘중요도가 떨어져 더 이상 사용되지 않고 앞으로는 사라지게 될’ 것이라는 뜻이다. 
> 
> 새로운 기능이 나와서 기존 기능보다 더 좋을 때, 기존 기능을 deprecated 처리하곤 한다. 
> 
> **<span style="color:#ff6600">‘이전 사용자를 위해 기능을 제거하지는 않지만 곧 없앨 예정이므로 더 이상 사용하지 말라’</span>**는 의미
{:.prompt-info }

<br/>

## **💻 worker_threads**

노드에서 멀티 스레드 방식으로 작업할 수 있음.

- `isMainThread`: 현재 코드가 메인 스레드에서 실행되는지, 워커 스레드에서 실행되는지 구분

- 메인 스레드에서는 new Worker를 통해 현재 파일(__filename)을 워커 스레드에서 실행시킴

- worker.postMessage로 부모에서 워커로 데이터를 보냄

- parentPort.on(‘message’)로 부모로부터 데이터를 받고, postMessage로 데이터를 보냄

- 워커에서 on 메서드를 사용할 때는 직접 워커를 종료해야 한다는 점에 주의

🔻worker_threads.js

```js
const {
  Worker, isMainThread, parentPort,
} = require('worker_threads');

if (isMainThread) { // 부모일 때
  const worker = new Worker(__filename);
  worker.on('message', message => console.log('from worker', message));
  worker.on('exit', () => console.log('worker exit'));
  worker.postMessage('ping');
} else { // 워커일 때
  parentPort.on('message', (value) => {
    console.log('from parent', value);
    parentPort.postMessage('pong');
    parentPort.close();
  });
}
```

✅ 결과

```shell
$ node worker_threads
from parent ping
from worker pong
worker exit
```

<br/>

## **💻 여러 워커스레드 사용하기**

new Worker 호출하는 수만큼 워커 스레드가 생성됨

🔻worker_data.js

```js
const {
  Worker, isMainThread, parentPort, workerData,
} = require('worker_threads');

if (isMainThread) { // 부모일 때
  const threads = new Set();
  threads.add(new Worker(__filename, {
    workerData: { start: 1 },
  }));
  threads.add(new Worker(__filename, {
    workerData: { start: 2 },
  }));
  for (let worker of threads) {
    worker.on('message', message => console.log('from worker', message));
    worker.on('exit', () => {
      threads.delete(worker);
      if (threads.size === 0) {
        console.log('job done');
      }
    });
  }
} else { // 워커일 때
  const data = workerData;
  parentPort.postMessage(data.start + 100);
}
```

✅ 결과

```shell
$ node worker_data
from worker 101
from worker 102
job done
```

![메인 스레드와 워커의 통신](https://github.com/leekoby/leekoby.github.io/assets/118284808/d6862082-bac7-4758-8903-603b490a4297)

<br/>

## **💻 child_process**

노드에서 다른 프로그램을 실행하고 싶거나 명령어를 수행하고 싶을 때 사용

- 현재 노드 프로세스 외에 새로운 프로세스를 띄워서 명령을 수행함.

- 명령 프롬프트의 명령어인 dir을 노드를 통해 실행(리눅스라면 ls를 대신 적을 것)

🔻 exec.js

```js
const exec = require('child_process').exec;

const process = exec('dir');

process.stdout.on('data', function(data) {
  console.log(data.toString());
}); // 실행 결과

process.stderr.on('data', function(data) {
  console.error(data.toString());
}); // 실행 에러
```

✅ 결과

```shell
$ node exec
(현재 폴더의 파일 목록 표시)
```

명령 프롬프트나 파워셸에서 한글이 제대로 표시되지 않는 경우에는 다음 명령어를 입력해 터미널을 utf8로 바꾼 뒤 다시 실행

<br/>

## **💻 파이썬 실행**

파이썬 3가 설치되어 있어야 한다.

🔻 test.py

```py
print('hello python')
```

🔻 spawn.js

```js
const spawn = require('child_process').spawn;

const process = spawn('python', ['test.py']);

process.stdout.on('data', function(data) {
  console.log(data.toString());
}); // 실행 결과

process.stderr.on('data', function(data) {
  console.error(data.toString());
}); // 실행 에러
```

✅ 결과

```shell
node spawn
hello python
```

<br/>

## **💻 기타 모듈들**

- `async_hooks`: 비동기 코드의 흐름을 추적할 수 있는 실험적인 모듈

- `dgram`: UDP와 관련된 작업을 할 때 사용

- `assert`: 값을 비교하여 프로그램이 제대로 동작하는지 테스트하는 데 사용

- `dns`: 도메인 이름에 대한 IP 주소를 얻어내는 데 사용

- `net`: HTTP보다 로우 레벨인 TCP나 IPC 통신을 할 때 사용

- `querystring`: `URLSearchParams`가 나오기 이전에 쿼리스트링을 다루기 위해 사용했던 모듈

  -  요즘은 `URLSearchParams`를 사용하는 것을 권장

- `string_decoder`: 버퍼 데이터를 문자열로 바꾸는 데 사용

- `tls`: TLS와 SSL에 관련된 작업을 할 때 사용

- `tty`: 터미널과 관련된 작업을 할 때 사용

- `v8`: V8 엔진에 직접 접근할 때 사용

- `vm`: 가상 머신에 직접 접근할 때 사용

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}