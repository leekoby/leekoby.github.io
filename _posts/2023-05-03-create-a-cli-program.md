---
layout: post
title: 14. CLI 프로그램 만들기
author: admin
date: 2023-05-03 00:00:00 +900
lastmod: 2023-05-03 00:00:00 +900
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

# **🌈 CLI 프로그램 만들기**

## **💻 간단한 콘솔 명령어 만들기**

### **🍳 CLI**

CLI(Command Line Interface) 기반 노드 프로그램을 제작해보기

- 콘솔 창을 통해서 프로그램을 수행하는 환경

- 반대 개념으로는 GUI(그래픽 유저 인터페이스)가 있음

- 리눅스의 셸이나 브라우저 콘솔, 명령 프롬프트 등이 대표적인 CLI 방식 소프트웨어

- 개발자에게는 CLI 툴이 더 효율적일 때가 많음

### **🍳 콘솔 명령어**
노드 파일을 실행할 때 node [파일명] 명령어를 콘솔에 입력함


- node나 npm. nodemon처럼 콘솔에서 입력하여 어떠한 동작을 수행하는 명령어를 콘솔 명령어라고 부름.

- node와 npm 명령어는 노드를 설치해야만 사용할 수 있음

- nodemon, rimraf같은 명령어는 npm i –g 옵션으로 설치하면 명령어로 사용 가능

- 패키지 명과 콘솔 명령어를 다르게 만들 수도 있음(sequelize-cli는 sequelize 명령어 사용)

- 이러한 명령어를 만드는 게 이 장의 목표

<br/>

### **🍳 프로젝트 시작하기**

node-cli 폴더 안에 package.json과 index.js 생성

- index.js 첫 줄의 주석에 주목(윈도에서는 의미 없음)

- 리눅스나 맥 같은 유닉스 기반 운영체제에서는 /usr/bin/env에 등록된 node 명령어로 이 파일을 실행하라는 뜻

🔻 package.json

```js
{
  "name": "ch15",
  "version": "1.0.0",
  "description": "nodejs cli program",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "leekoby",
  "license": "ISC",
  "bin": {
    "cli": "./index.js"
  }
}
```

```sh
npm i -g
```

<br/>

### **🍳 실행 해보기**

🔻 index.js

```js
#!/usr/bin/env node
console.log('hello cli');
```

```sh
npx cli
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4e478a01-7d80-4513-8931-e28a6642af0b)


### **🍳 명령어에 옵션 붙이기**

process.argv로 명령어에 어떤 옵션이 주어졌는지 확인 가능(배열로 표시)


- 코드가 바뀔 때마다 전역 설치할 필요는 없음

- package.json 내용이 바뀌면 다시 전역 설치해야 함

- 배열의 첫 요소는 노드의 경로, 두 번째 요소는 cli 명령어의 경로, 나머지는 옵션

🔻 index.js

```js
#!/usr/bin/env node
console.log('hello cli', process.argv);
```

```sh
npx cli one two three four
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f2e78ec3-2d90-48c5-b93b-ccae91b58076)

### **🍳 사용자로부터 입력 받기**

노드 내장 모듈 readline 사용


- createInterface 메서드로 rl 객체를 만듦

- process.stdin, process.stdout은 각각 콘솔을 통해 입력받고 출력한다는 의미

- question 메서드로 질문을 표시하고 답변이 들어오면 콜백 함수가 실행됨

- 답변은 answer 매개변수에 담김

🔻 index.js

```js
#!/usr/bin/env node
const readline = require('readline');

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

console.clear();
const answerCallback = (answer) => {
  if (answer === 'y') {
    console.log('감사합니다!');
    rl.close();
  } else if (answer === 'n') {
    console.log('죄송합니다!');
    rl.close();
  } else {
    console.clear();
    console.log('y 또는 n만 입력하세요.');
    rl.question('예제가 재미있습니까? (y/n) ', answerCallback);
  }
};

rl.question('예제가 재미있습니까? (y/n) ', answerCallback);
```

```sh
npx cli
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/066f487f-a799-4313-bc53-c27b7083c523)

<br/>

### **🍳 템플릿을 만들어주는 명령어 만들기**

🔻 template.js

```js
#!/usr/bin/env node
const fs = require('fs');
const path = require('path');
const readline = require('readline');

let rl;
let type = process.argv[2];
let name = process.argv[3];
let directory = process.argv[4] || '.';

const htmlTemplate = `
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Template</title>
  </head>
  <body>
    <h1>Hello</h1>
    <p>CLI</p>
  </body>
</html>
`;

const routerTemplate = `
const express = require('express');
const router = express.Router();
 
router.get('/', (req, res, next) => {
   try {
     res.send('ok');
   } catch (error) {
     console.error(error);
     next(error);
   }
});
 
module.exports = router;
`;

const exist = (dir) => { // 폴더 존제 확인 함수
  try {
    fs.accessSync(dir, fs.constants.F_OK | fs.constants.R_OK | fs.constants.W_OK);
    return true;
  } catch (e) {
    return false;
  }
};

const mkdirp = (dir) => { // 경로 생성 함수
  const dirname = path
    .relative('.', path.normalize(dir))
    .split(path.sep)
    .filter(p => !!p);
  dirname.forEach((d, idx) => {
    const pathBuilder = dirname.slice(0, idx + 1).join(path.sep);
    if (!exist(pathBuilder)) {
      fs.mkdirSync(pathBuilder);
    }
  });
};

const makeTemplate = () => { // 템플릿 생성 함수
  mkdirp(directory);
  if (type === 'html') {
    const pathToFile = path.join(directory, `${name}.html`);
    if (exist(pathToFile)) {
      console.error('이미 해당 파일이 존재합니다');
    } else {
      fs.writeFileSync(pathToFile, htmlTemplate);
      console.log(pathToFile, '생성 완료');
    }
  } else if (type === 'express-router') {
    const pathToFile = path.join(directory, `${name}.js`);
    if (exist(pathToFile)) {
      console.error('이미 해당 파일이 존재합니다');
    } else {
      fs.writeFileSync(pathToFile, routerTemplate);
      console.log(pathToFile, '생성 완료');
    }
  } else {
    console.error('html 또는 express-router 둘 중 하나를 입력하세요.');
  }
};

const dirAnswer = (answer) => { // 경로 설정
  directory = answer?.trim() || '.';
  rl.close();
  makeTemplate();
};

const nameAnswer = (answer) => { // 파일명 설정
  if (!answer || !answer.trim()) {
    console.clear();
    console.log('name을 반드시 입력하셔야 합니다.');
    return rl.question('파일명을 설정하세요. ', nameAnswer);
  }
  name = answer;
  return rl.question('저장할 경로를 설정하세요.(설정하지 않으면 현재경로) ', dirAnswer);
};

const typeAnswer = (answer) => { // 템플릿 종류 설정
  if (answer !== 'html' && answer !== 'express-router') {
    console.clear();
    console.log('html 또는 express-router만 지원합니다.');
    return rl.question('어떤 템플릿이 필요하십니까? ', typeAnswer);
  }
  type = answer;
  return rl.question('파일명을 설정하세요. ', nameAnswer);
};

const program = () => {
  if (!type || !name) {
    rl = readline.createInterface({
      input: process.stdin,
      output: process.stdout,
    });
    console.clear();
    rl.question('어떤 템플릿이 필요하십니까? ', typeAnswer);
  } else {
    makeTemplate();
  }
};

program(); // 프로그램 실행부
```

<br/>

### **🍳 템플릿을 만들어주는 명령어 만들기**

- 디렉토리가 존재하는지 확인하는 exist 함수와 디렉토리를 생성하는 mkdirp 함수를 만듦

- program이라는 함수는 template.js의 실행부

- makeTemplate은 옵션을 읽어서 알맞은 템플릿을 작성해주는 함수

- 옵션에 따라 다른 동작을 하도록 분기 처리

- package.json의 명령어를 바꿔주고 전역 재설치

🔻 package.json

```js
{
  "name": "ch15",
  "version": "1.0.0",
  "description": "nodejs cli program",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "leekoby",
  "license": "ISC",
  "bin": {
    "cli": "./template.js"
  }
}
```

```sh
npm i -g
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b1a3a1da-9c5c-49b0-b91d-0b2d3d1dc884)

<br/>

---

<br/>

## **💻 Commender, Inquier 사용하기**

### **🍳 패키지로 쉽게 CLI 프로그램 만들기**

npm에는 CLI 프로그램을 위한 라이브러리가 많이 준비되어 있음

commander(CLI)와 inquirer(사용자와 상호작용)를 사용해서 예제를 만들어 봄

14.1의 프로그램을 commander와 inquirer로 재작성할 것

```sh
npm i commander@9 inquirer@8
```

<br/>

### **🍳 commander 사용하기**

command.js 파일 작성 후 pakage.json 수정하기


- `version`: 프로그램의 버전 설정(--version 또는 -v로 확인)

- `usage`: 프로그램 사용 방법 기입(--help로 또는 –h로 확인)

- `command`: 명령어 등록(template 과 * 등록함)

- `<>`는 필수 옵션을 의미

- `[]`는 선택 옵션을 의미

- `description`: 명령어에 대한 설명을 설정하는 메서드

- `alias`: 명령어에 대한 별칭

- `option`: 명령어에 대한 옵션을 등록

- `--옵션 [값]` 또는 `–옵션 <값>` 형식

- 두 번째 인자는 `설명`, 세 번째 인자는 `기본값`

- `Action`: 명령어가 실행될 때 수행할 동작 등록

- `parse`: process.argv를 파싱하여 옵션 등록

🔻 command.js

```js
#!/usr/bin/env node
const { program } = require('commander');

program
  .version('0.0.1', '-v, --version')
  .name('cli');

program
  .command('template <type>')
  .usage('<type> --filename [filename] --path [path]')
  .description('템플릿을 생성합니다.')
  .alias('tmpl')
  .option('-f, --filename [filename]', '파일명을 입력하세요.', 'index')
  .option('-d, --directory [path]', '생성 경로를 입력하세요', '.')
  .action((type, options, command) => {
    console.log(type, options.filename, options.directory);
  });

program
  .command('*', { noHelp: true })
  .action(() => {

    console.log('해당 명령어를 찾을 수 없습니다.');
    program.help();

  });

program.parse(process.argv);
```

<br/>

### **🍳 template.js를 커맨더로 전환하기**

command.js 수정

template.js를 붙여 넣은 후 첫 require 부분과 끝 program 부분만 수정하면 됨

프로그램 전역 재설치 후 실행해보기

- -v, -h로 버전, 설명 확인 가능하고 필수 옵션도 자동으로 체크해줌

🔻 command.js

![commandjs](https://github.com/leekoby/leekoby.github.io/assets/118284808/47f558a8-5a84-4510-8cbf-e896efd9b33c)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8aba90c2-b26f-4b6d-a0b1-bd12e1001435)

<br/>

### **🍳 commander 프로그램 실행하기**

명령어들을 커맨더 식으로 전환함

- 옵션들은 순서를 바꿔서 입력해도 됨

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ea160b0c-97d8-4d82-9992-0709c8a628a4)

### **🍳 inquirer 사용하기**

여전히 옵션들을 외워야 하는 불편함

- inquirer로 상호 작용 추가

🔻 command.js

![commandjs](https://github.com/leekoby/leekoby.github.io/assets/118284808/5d6f18f6-71e4-4deb-a493-d34459594ca7)


### **🍳 Inquirer API**

readline보다 간결해짐

- 커맨더의 액션이 실행되지 않은 경우 triggered가 false라서 inquirer가 실행됨

- prompt 메서드로 상호작용 창 띄울 수 있음

- type: 질문의 종류( input, checkbox, list, password, confirm 등)

- 예제에서는 input(평범한 답변), list(다중 택일), confirm(Yes or No) 사용

- name: 질문의 이름, 답변 객체 속성명으로 질문의 이름을, 속성 값으로 질문의 답을 가짐

- message: 사용자에게 표시되는 문자열(여기에 질문을 적음)

- choices: type이 checkbox, list 등인 경우 선택지를 넣는 곳(배열로)

- default: 답 적지 않았을 때 기본값

- 예제에서는 질문 네 개를 연달아 하고 있음

- 질문의 name이 type, name, directory라서 각각의 답변이 answers.type, answers.name, answers.director에 들어 있음.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5988d964-4ec7-4660-9dd5-3aef1c8d102c)

### **🍳 chalk**

콘솔에 색을 추가함

- console.log과 console.error에 chalk 적용

- 문자열을 chalk 객체의 메서드로 감싸면 됨

- chalk.green, chalk.yellow, chalk.red 등등

- chalk.rgb(12, 34, 56)(문자열) 또는 chalk.hex(‘#123456’)(텍스트)도 가능

- 배경색도 지정 가능해서 chalk.bgGreen, chalk.bgYellow, chalk.bgRgb, chalk.bgHex

- 동시에 지정하려면 chalk.red.bgBlue.bold

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/d6c71938-f6da-4e04-a0cf-80a8913af012)

<br/>

### **🍳 프로그램을 공유하고 싶다면?**

만든 CLI 프로그램을 공유하고 싶다면 5장의 과정대로 npm에 배포하면 됨

- 다른 사용자가 npm i –g <패키지명>을 한다면 다운로드 받아 사용할 수 있음

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

> [readline](https://nodejs.org/api/readline.html){:target="_blank"}
{:.prompt-info}

> [process](https://nodejs.org/api/process.html){:target="_blank"}
{:.prompt-info}

> [commander](https://www.npmjs.com/package/commander){:target="_blank"}
{:.prompt-info}

> [inquirer](https://www.npmjs.com/package/inquirer){:target="_blank"}
{:.prompt-info}

> [chalk](https://www.npmjs.com/package/chalk){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}