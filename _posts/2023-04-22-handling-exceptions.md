---
layout: post
title: 3.8 예외 처리하기 + 3장 함께 보면 좋은 자료
author: admin
date: 2023-04-22 12:00:00 +900
lastmod: 2023-04-22 12:00:00 +900
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

# **🌈 예외 처리하기**

## **💻 예외 처리**

예외(Exception): 처리하지 못한 에러

- 노드 프로세스/스레드를 멈춤

- 노드는 기본적으로 싱글 스레드라 스레드가 멈춘다는 것은 프로세스가 멈추는 것

- 에러 처리는 필수

<br/>

---

<br/>

## **💻 try/catch문**

기본적으로 try catch문으로 예외를 처리

- 에러가 발생할 만한 곳을 try catch로 감쌈

🔻error1.js

```js
setInterval(() => {
  console.log('시작');
  try {
    throw new Error('서버를 고장내주마!');
  } catch (err) {
    console.error(err);
  }
}, 1000);
```

✅ 결과

```sh
$ node error1
시작
Error: 서버를 고장내주마!
...
시작
Error: 서버를 고장내주마!
...
// 계속 반복
```

>throw하는 경우에는 반드시 try/catch문으로 throw한 에러를 잡아야 한다.
{:.prompt-tip}

<br/>

---

<br/>

## **💻 노드 비동기 메서드의 에러**

노드 비동기 메서드의 에러는 따로 처리하지 않아도 됨

- 콜백 함수에서 에러 객체 제공

🔻error2.js

```js
const fs = require('fs');

setInterval(() => {
  fs.unlink('./abcdefg.js', (err) => {
    if (err) {
      console.error(err);
    }
  });
}, 1000);
```

✅ 결과

```sh
$ node error2
[Error: ENOENT: no such file or directory, unlink 'C:\Users\zerocho\abcdefg.js'] {
  errno: -4058,
  code:  'ENOENT',
  syscall:  'unlink',
  path:  'C:\Users\zerocho\abcdefg.js'
}
[Error: ENOENT: no such file or directory, unlink 'C:\Users\zerocho\abcdefg.js'] {
  errno: -4058,
  code:  'ENOENT',
  syscall:  'unlink',
  path:  'C:\Users\zerocho\abcdefg.js'
}
// 계속 반복
```


<br/>

---

<br/>

## **💻 프로미스의 에러**

노드 16 버전부터 프로미스의 에러는 반드시 catch해야 한다. 

catch하지 않으면 에러와 함께 노드 프로세스가 종료

🔻error3.js

```js
const fs = require('fs').promises;

setInterval(() => {
  fs.unlink('./abcdefg.js').catch(console.error);
}, 1000);
```

✅ 결과

```sh
$ node error3
[Error: ENOENT: no such file or directory, unlink 'C:\Users\zerochobcdefg.js'] {
  errno: -4058,
  code:  'ENOENT',
  syscall:  'unlink',
  path:  'C:\Users\speak\abcdefg.js'
}
// 계속 반복
```


<br/>

---

<br/>

## **💻 예측 불가능한 에러 처리하기**

최후의 수단으로 사용

- 콜백 함수의 동작이 보장되지 않음

- 따라서 복구 작업용으로 쓰는 것은 부적합

- 에러 내용 기록 용으로만 쓰는 게 좋음

🔻error4.js

```js
process.on('uncaughtException', (err) => {
  console.error('예기치 못한 에러', err);
});

setInterval(() => {
  throw new Error('서버를 고장내주마!');
}, 1000);

setTimeout(() => {
  console.log('실행됩니다');
}, 2000);
```

✅ 결과

```sh
$ node error4
예기치 못한 에러 Error: 서버를 고장내주마!
...
실행됩니다
예기치 못한 에러 Error: 서버를 고장내주마!
예기치 못한 에러 Error: 서버를 고장내주마!
// 계속 반복
```

<br/>

---

<br/>

## **💻 자주 발생하는 에러들**

- `node: command not found`
  
  - 노드를 설치했지만 이 에러가 발생하는 경우는 `환경 변수가 제대로 설정되어 있지 않은 것`
  
  - 환경 변수에는 노드가 설치된 경로가 포함되어야 한다. 
  
  - node 외의 다른 명령어도 마찬가지 
  
  - 그 명령어를 수행할 수 있는 파일이 환경 변수에 들어 있어야 명령어를 콘솔에서 사용할 수 있다.

- `ReferenceError: 모듈 is not defined`
  
  - 모듈을 require했는지 확인

- `Error: Cannot find module 모듈명`
  
  - 해당 모듈을 require했지만 설치하지 않았다. 
  
  - npm i 명령어로 설치

- `Error [ERR_MODULE_NOT_FOUND]`
  
  - 존재하지 않는 모듈을 불러오려 할 때 발생

- `Error: Can't set headers after they are sent`

  - 요청에 대한 응답을 보낼 때 응답을 두 번 이상 보냈다. 
  
  - 요청에 대한 응답은 한 번만 보내야 한다. 
  
  - 응답을 보내는 메서드를 두 번 이상 사용하지 않았는지 체크

- `FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed- JavaScript heap out of memory`
  
  - 코드를 실행할 때 `메모리가 부족`해서 스크립트가 정상적으로 작동하지 않는 경우 
  
  - 코드가 잘못 구현되었을 확률이 높으므로 코드를 점검
  
  - 만약 코드는 정상이지만 노드가 활용할 수 있는 메모리가 부족한 경우라면 노드의 메모리를 늘릴 수 있다.
  
  - 노드를 실행할 때 `node --max-old-space-size=4096` 파일명과 같은 명령어를 사용하면 된다. 
  
  - 4096은 4GB를 의미합니다. 여기에 원하는 용량을 적으면 된다.

- `UnhandledPromiseRejectionWarning: Unhandled promise rejection`
  
  - 프로미스 사용 시 catch 메서드를 붙이지 않으면 발생
  
  - 항상 catch를 붙여 에러가 발생하는 상황에 대비

- `EADDRINUSE 포트 번호`

  - 해당 포트 번호에 이미 다른 프로세스가 연결되어 있다. 
  
  - 그 프로세스는 노드 프로세스일 수도 있고 다른 프로그램일 수도 있다. 
  
  - 그 프로세스를 종료하거나 다른 포트 번호를 사용

- `EACCES 또는 EPERM`
  
  - 노드가 작업을 수행하는 데 권한이 충분하지 않다. 
  
  - 파일/폴더 수정, 삭제, 생성 권한을 확인해보는 것이 좋다. 
  
  - 맥이나 리눅스 운영체제라면 명령어 앞에 sudo를 붙이는 것도 방법이다.

- `EJSONPARSE`
  
  - package.json 등의 JSON 파일에 문법 오류가 있을 때 발생
  
  - 자바스크립트 객체와는 형식이 조금 다르니 쉼표 같은 게 빠지거나 추가되지는 않았는지 확인

- `ECONNREFUSED`
  
  - 요청을 보냈으나 연결이 성립하지 않을 때 발생
  
  - 요청을 받는 서버의 주소가 올바른지, 서버가 꺼져 있지는 않은지 등을 확인

- `ETARGET`
  
  - package.json에 기록한 패키지 버전이 존재하지 않을 때 발생
  
  - 해당 버전이 존재하는지 확인

- `ETIMEOUT`
  
  - 요청을 보냈으나 응답이 시간 내에 오지 않을 때 발생
  
  - 역시 요청을 받는 서버의 상태를 점검

- `ENOENT: no such file or directory`
  
  - 지정한 폴더나 파일이 존재하지 않는 경우
  
  - 맥이나 리눅스 운영체제에서는 대소문자도 구별하므로 확인

<br/>

---

<br/>

## **💻 프로세스 종료**

### **🍳 윈도우**

```sh
$ netstat -ano | findstr 포트
$ taskkill /pid 프로세스아이디 /f
```


### **🍳 맥/리눅스**

```sh
$ lsof -i tcp:포트
$ kill -9 프로세스아이디
```

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

>[노드 공식 문서](https://nodejs.org/dist/latest-v18.x/docs/api/){:target="_blank"}
{:.prompt-tip}

>[NODE_OPTIONS](https://nodejs.org/dist/latest-v18.x/docs/api/cli.html#cli_node_options_options){:target="_blank"}
{:.prompt-tip}

>[UV_THREADPOOL_SIZE](https://nodejs.org/dist/latest-v18.x/docs/api/cli.html#cli_uv_threadpool_size_size){:target="_blank"}
{:.prompt-tip}

>[에러 코드](https://nodejs.org/dist/latest-v18.x/docs/api/errors.html#errors_node_js_error_codes){:target="_blank"}
{:.prompt-tip}

>[uncaughtException](https://nodejs.org/dist/latest-v18.x/docs/api/process.html#process_event_uncaughtexception){:target="_blank"}
{:.prompt-tip}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}