---
layout: post
title: 3.6 파일 시스템 접근하기
author: admin
date: 2023-04-22 04:00:00 +900
lastmod: 2023-04-22 04:00:00 +900
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

# **🌈 파일 시스템 접근하기**

## **💻 fs**

파일 시스템에 접근하는 모듈

- 파일/폴더 생성, 삭제, 읽기, 쓰기 가능

- 웹 브라우저에서는 제한적이었으나 노드는 권한을 가지고 있음

- 파일 읽기 예제(결과의 버퍼는 뒤에서 설명함)

🔻readme.txt

```
저를 읽어주세요.
```

🔻readFile.js
```js
const fs = require('fs');

fs.readFile('./readme.txt', (err, data) => {
  if (err) {
    throw err;
  }
  console.log(data);
  console.log(data.toString());
});
```

✅ 결과

```
$ node readFile
<Buffer ec a0 80 eb a5 bc 20 ec 9d bd ec 96 b4 ec a3 bc ec 84 b8 ec 9a 94 2e>
저를 읽어주세요.
```

<br/>

## **💻 fs 프로미스**

콜백 방식 대신 프로미스 방식으로 사용 가능

- require(‘fs’).promises

- 사용하기 훨씬 더 편해서 프로미스 방식을 추천함

🔻readfilepromise.js

```js
const fs = require('fs').promises;

fs.readFile('./readme.txt')
  .then((data) => {
    console.log(data);
    console.log(data.toString());
  })
  .catch((err) => {
    console.error(err);
  });
```
<br/>

## **💻 fs로 파일 만들기**

파일을 만드는 예제

🔻writeFile.js

```js
const fs = require('fs');

fs.writeFile('./writeme.txt', '글이 입력됩니다', (err) => {
  if (err) {
    throw err;
  }
  fs.readFile('./writeme.txt', (err, data) => {
    if (err) {
      throw err;
    }
    console.log(data.toString());
  });
});
```

✅ 결과

```shell
$ node writeFile
글이 입력됩니다.
```

<br/>

## **💻 동기 메서드와 비동기 메서드**

노드는 대부분의 내장 모듈 메서드를 비동기 방식으로 처리

- 비동기는 코드의 순서와 실행 순서가 일치하지 않는 것을 의미

- 일부는 동기 방식으로 사용 가능

- 우측 코드 콘솔 예측해보기

🔻readme2.txt

```
저를 여러 번 읽어보세요.
```


🔻async.js

```js
const fs = require('fs');

console.log('시작');
fs.readFile('./readme2.txt', (err, data) => {
  if (err) {
    throw err;
  }
  console.log('1번', data.toString());
});
fs.readFile('./readme2.txt', (err, data) => {
  if (err) {
    throw err;
  }
  console.log('2번', data.toString());
});
fs.readFile('./readme2.txt', (err, data) => {
  if (err) {
    throw err;
  }
  console.log('3번', data.toString());
});
console.log('끝');
```

✅ 결과

```shell
$ node async
시작
끝
2번 저를 여러 번 읽어보세요.
3번 저를 여러 번 읽어보세요.
1번 저를 여러 번 읽어보세요.
```

> **<span style="color:#3366ff">동기와 비동기, 블로킹과 논블로킹</span>**
>
> **<span style="color:#ff6600">동기와 비동기</span>**: 백그라운드 작업 완료 확인 여부
>
> **<span style="color:#ff6600">블로킹과 논블로킹</span>**: 함수가 바로 return되는지 여부
>
> 동기-블로킹 방식에서는 백그라운드 작업 완료 여부를 계속 확인하며, 호출한 함수가 바로 return되지 않고 백그라운드 작업이 끝나야 return
> 
> 비동기-논블로킹 방식에서는 호출한 함수가 바로 return되어 다음 작업으로 넘어가고, 백그라운드 작업 완료 여부는 신경 쓰지 않고 나중에 백그라운드가 알림을 줄 때 처리
>
> ![동기와 비동기, 블로킹과 논블로킹](https://github.com/leekoby/leekoby.github.io/assets/118284808/73d1eef7-28ff-49e9-ab5d-07070872a15b){: width="500" height="500" }
{:.prompt-info }

<br/>

## **💻 비동기 메서드로 순서 유지하기**

프로미스로 극복

> const fs = require('fs').promises;

🔻asyncorderpromise.js

```js
const fs = require('fs').promises;

console.log('시작');
fs.readFile('./readme2.txt')
  .then((data) => {
    console.log('1번', data.toString());
    return fs.readFile('./readme2.txt');
  })
  .then((data) => {
    console.log('2번', data.toString());
    return fs.readFile('./readme2.txt');
  })
  .then((data) => {
    console.log('3번', data.toString());
    console.log('끝');
  })
  .catch((err) => {
    console.error(err);
  });
```
<br/>

## **💻 버퍼와 스트림 이해하기**

`버퍼`: 일정한 크기로 모아두는 데이터

- 일정한 크기가 되면 한 번에 처리

- `버퍼링`: 버퍼에 데이터가 찰 때까지 모으는 작업

`스트림`: 데이터의 흐름

- 일정한 크기로 나눠서 여러 번에 걸쳐서 처리

- 버퍼(또는 청크)의 크기를 작게 만들어서 주기적으로 데이터를 전달

- `스트리밍`: 일정한 크기의 데이터를 지속적으로 전달하는 작업

![버퍼와 스트림](https://github.com/leekoby/leekoby.github.io/assets/118284808/a4fa10e8-abc3-4ca1-b725-bbfc46ff6967)

<br/>

## **💻 버퍼 사용하기**

노드에서는 Buffer 객체 사용

🔻buffer.js

```js
const buffer = Buffer.from('저를 버퍼로 바꿔보세요');
console.log('from():', buffer);
console.log('length:', buffer.length);
console.log('toString():', buffer.toString());

const array = [Buffer.from('띄엄 '), Buffer.from('띄엄 '), Buffer.from('띄어쓰기')];
const buffer2 = Buffer.concat(array);
console.log('concat():', buffer2.toString());

const buffer3 = Buffer.alloc(5);
console.log('alloc():', buffer3);
```

✅ 결과

```shell
$ node buffer
from(): <Buffer ec a0 80 eb a5 bc 20 eb b2 84 ed 8d bc eb a1 9c 20 eb b0 94 ea bf 94 eb b3 b4 ec 84 b8 ec 9a 94>
length: 32
toString(): 저를 버퍼로 바꿔보세요
concat(): 띄엄 띄엄 띄어쓰기
alloc(): <Buffer 00 00 00 00 00>
```

<br/>

## **💻 Buffer의 메서드**

노드에서는 Buffer 객체 사용

- `from(문자열)`: 문자열을 버퍼로 바꿀 수 있습니다. length 속성은 버퍼의 크기를 알려줍니다. 바이트 단위입니다.

- `toString(버퍼)`: 버퍼를 다시 문자열로 바꿀 수 있습니다. 이때 base64나 hex를 인자로 넣으면 해당 인코딩으로도 변환할 수 있습니다.

- `concat(배열)`: 배열 안에 든 버퍼들을 하나로 합칩니다.

- `alloc(바이트)`: 빈 버퍼를 생성합니다. 바이트를 인자로 지정해주면 해당 크기의 버퍼가 생성됩니다.

<br/>

## **💻 파일 읽는 스트림 사용하기**

`fs.createReadStream`

- createReadStream에 인자로 파일 경로와 옵션 객체 전달

- highWaterMark 옵션은 버퍼의 크기(`바이트 단위, 기본값 64KB`)

- `data(chunk 전달)`, `end(전달 완료)`, `error(에러 발생)` 이벤트 리스너와 같이 사용

🔻readmetext3.txt

```js
저는 조금씩 조금씩 나눠서 전달됩니다. 나눠진 조각을 chunk라고 부릅니다.
```

🔻createreadStream.js

```js
const fs = require('fs');

const readStream = fs.createReadStream('./readme3.txt', { highWaterMark: 16 });
const data = [];

readStream.on('data', (chunk) => {
  data.push(chunk);
  console.log('data :', chunk, chunk.length);
});

readStream.on('end', () => {
  console.log('end :', Buffer.concat(data).toString());
});

readStream.on('error', (err) => {
  console.log('error :', err);
});
```

✅ 결과 

```shell
$ node createReadStream
data : <Buffer ec a0 80 eb 8a 94 20 ec a1 b0 ea b8 88 ec 94 a9> 16
data : <Buffer 20 ec a1 b0 ea b8 88 ec 94 a9 20 eb 82 98 eb 88> 16
data : <Buffer a0 ec 84 9c 20 ec a0 84 eb 8b ac eb 90 a9 eb 8b> 16
data : <Buffer 88 eb 8b a4 2e 20 eb 82 98 eb 88 a0 ec a7 84 20> 16
data : <Buffer ec a1 b0 ea b0 81 ec 9d 84 20 63 68 75 6e 6b eb> 16
data : <Buffer 9d bc ea b3 a0 20 eb b6 80 eb a6 85 eb 8b 88 eb> 16
data : <Buffer 8b a4 2e> 3
end : 저는 조금씩 조금씩 나눠서 전달됩니다. 나눠진 조각을 chunk라고 부릅니다.
```

<br/>

## **💻 스트림 사이에 pipe 사용하기**

pipe로 여러 개의 스트림을 이을 수 있음

- 스트림으로 파일을 복사하는 예제

🔻readme4.txt

```
저를 writeme3.txt로 보내주세요.
```

🔻pipe.js

```js
const fs = require('fs');

const readStream = fs.createReadStream('readme4.txt');
const writeStream = fs.createWriteStream('writeme3.txt');
readStream.pipe(writeStream);
```

✅ 결과

```shell
$ node pipe
```

<br/>

## **💻 여러 개의 스트림 연결하기**

pipe로 여러 개의 스트림을 이을 수 있음

- 파일을 압축한 후 복사하는 예제

- 압축에는 zlib 내장 모듈 사용(createGzip으로 .gz 파일 생성)

🔻gzip.js

```js
const zlib = require('zlib');
const fs = require('fs');

const readStream = fs.createReadStream('./readme4.txt');
const zlibStream = zlib.createGzip();
const writeStream = fs.createWriteStream('./readme4.txt.gz');
readStream.pipe(zlibStream).pipe(writeStream);
```

✅ 결과

```shell
$ node gzip
```

<br/>

## **💻 큰 파일 만들기**

1GB 정도의 파일을 만들어 봄.

createWriteStream으로 만들어야 메모리 문제가 생기지 않음.

🔻createbigfile.js

```js
const fs = require('fs');
const file = fs.createWriteStream('./big.txt');

for (let i = 0; i <= 10000000; i++) {
  file.write('안녕하세요. 엄청나게 큰 파일을 만들어 볼 것입니다. 각오 단단히 하세요!\n');
}
file.end();
```

<br/>

## **💻 메모리 체크하기**

버퍼 방식과 스트림 방식 메모리 사용량을 비교해보기

🔻buffer-memory.js

```js
const fs = require('fs');

console.log('before: ', process.memoryUsage().rss);

const data1 = fs.readFileSync('./big.txt');
fs.writeFileSync('./big2.txt', data1);
console.log('buffer: ', process.memoryUsage().rss);
```

✅ 결과

```shell
$ node buffer-memory
before:  18137088
buffer: 1019133952
```

🔻stream-memory.js

```js
const fs = require('fs');

console.log('before: ', process.memoryUsage().rss);

const readStream = fs.createReadStream('./big.txt');
const writeStream = fs.createWriteStream('./big3.txt');
readStream.pipe(writeStream);
readStream.on('end', () => {
  console.log('stream: ', process.memoryUsage().rss);
});
```

✅ 결과

```sh
$ node stream-memory
before:  18087936
stream: 62472192
```

<br/>

## **💻 기타 fs 메서드**

파일 및 폴더 생성

🔻fscreate.js

```js
const fs = require('fs');

fs.access('./folder', fs.constants.F_OK | fs.constants.R_OK | fs.constants.W_OK, (err) => {
  if (err) {
    if (err.code === 'ENOENT') {
      console.log('폴더 없음');
      fs.mkdir('./folder', (err) => {
        if (err) {
          throw err;
        }
        console.log('폴더 만들기 성공');
        fs.open('./folder/file.js', 'w', (err, fd) => {
          if (err) {
            throw err;
          }
          console.log('빈 파일 만들기 성공', fd);
          fs.rename('./folder/file.js', './folder/newfile.js', (err) => {
            if (err) {
              throw err;
            }
            console.log('이름 바꾸기 성공');
          });
        });
      });
    } else {
      throw err;
    }
  } else {
    console.log('이미 폴더 있음');
  }
});
```

✅ 결과 

```sh
$ node fsCreate
폴더 없음
폴더 만들기 성공
빈 파일 만들기 성공 3
이름 바꾸기 성공
$ node fsCreate
이미 폴더 있음
```

<br/>

## **💻 access, mkdir, open, rename**

파일 및 폴더 생성, 삭제

모두 비동기 메서드이므로 한 메서드의 콜백에서 다른 메서드를 호출

- `fs.access(경로, 옵션, 콜백)`
  
  - 폴더나 파일에 접근할 수 있는지를 체크
  
  - 두 번째 인자로 상수들(constants를 통해 가져옴)을 넣었다. 
  
  - `F_OK`는 파일 존재 여부, `R_OK`는 읽기 권한 여부, `W_OK`는 쓰기 권한 여부를 체크
  
  - 파일/폴더나 권한이 없다면 에러가 발생하는데, 파일/폴더가 없을 때의 에러 코드는 ENOENT

- `fs.mkdir(경로, 콜백)`
  
  - 폴더를 만드는 메서드
  
  - 이미 폴더가 있다면 에러가 발생하므로 먼저 `access()` 메서드를 호출해서 확인하는 것이 중요

- `fs.open(경로, 옵션, 콜백)`
  
  - 파일의 아이디(fd 변수)를 가져오는 메서드
  
  - 파일이 없다면 파일을 생성한 뒤 그 아이디를 가져온다.
  
  - 가져온 아이디를 사용해 `fs.read()`나 `fs.write()`로 읽거나 쓸 수 있다. 
  
  - 두 번째 인자로 어떤 동작을 할 것인지 설정할 수 있다. 
  
  - 쓰려면 w, 읽으려면 r, 기존 파일에 추가하려면 a

  - 예제에서는 w로 설정했으므로 파일이 없을 때 새로 만들 수 있었다. 
  
  - r이었다면 에러가 발생하였을 것

- `fs.rename(기존 경로, 새 경로, 콜백)`

  - 파일의 이름을 바꾸는 메서드
  
  - 기존 파일 위치와 새로운 파일 위치
  
  - 반드시 같은 폴더를 지정할 필요는 없으므로 잘라내기 같은 기능을 할 수도 있다.

<br/>

## **💻 폴더 내용 확인 및 삭제**

- `fs.readdir(경로, 콜백)`
  - 폴더 안의 내용물을 확인
  
  - 배열 안에 내부 파일과 폴더명이 나온다.

- `fs.unlink(경로, 콜백)`
  
  - 파일을 지울 수 있다. 
  
  - 파일이 없다면 에러가 발생하므로 먼저 파일이 있는지를 꼭 확인해야 합니다.

- `fs.rmdir(경로, 콜백)`
  
  -  폴더를 지울 수 있다. 
  
  -  폴더 안에 파일이 있다면 에러가 발생하므로 먼저 내부 파일을 모두 지우고 호출

<br/>

## **💻 파일을 복사하는 방법**

🔻copyfile.js

```js
const fs = require('fs');

fs.copyFile('readme4.txt', 'writeme4.txt', (error) => {
  if (error) {
    return console.error(error);
  }
  console.log('복사 완료');
});
```

<br/>

## **💻 파일을 감시하는 방법(변경 사항 발생 시 이벤트 호출)**

🔻watch.js

```js
const fs = require('fs');

fs.watch('./target.txt', (eventType, filename) => {
  console.log(eventType, filename);
});
```

<br/>

## **💻 스레드풀 알아보기**

🔻fs, crypto, zlib 모듈의 메서드를 실행할 때는 백그라운드에서 동시에 실행됨

스레드풀이 동시에 처리해줌

```js
const crypto = require('crypto');

const pass = 'pass';
const salt = 'salt';
const start = Date.now();

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('1:', Date.now() - start);
});

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('2:', Date.now() - start);
});

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('3:', Date.now() - start);
});

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('4:', Date.now() - start);
});

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('5:', Date.now() - start);
});

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('6:', Date.now() - start);
});

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('7:', Date.now() - start);
});

crypto.pbkdf2(pass, salt, 1000000, 128, 'sha512', () => {
  console.log('8:', Date.now() - start);
});
```

✅ 결과

```sh
$ node threadpool
4: 1548
2: 1583
1: 1590
3: 1695
6: 3326
5: 3463
7: 3659
8: 3682
```

<br/>

## **💻 UV_THREAD_SIZE**

스레드풀을 직접 컨트롤할 수는 없지만 개수 조절은 가능

- 윈도우라면 터미널에 `SET UV_THREADPOOL_SIZE=개수`

- 맥, 리눅스라면 `UV_THREADPOOL_SIZE=개수`

- 이전 예제를 스레드풀 개수를 바꾼 뒤 재실행해보기

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/cab4d31a-91fa-470c-92ae-7ce6f56eb6ce){: width="500" height="500" }

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}