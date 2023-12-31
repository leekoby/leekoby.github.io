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

- 리눅스나 맥 같은 유닉스 기반 운영체제에서는 `/usr/bin/env`에 등록된 node 명령어로 이 파일을 실행하라는 뜻

🔻 package.json

![pakagejson](https://github.com/leekoby/leekoby.github.io/assets/118284808/7957f174-1930-4aab-a219-ad31e48c4266)

![install](https://github.com/leekoby/leekoby.github.io/assets/118284808/1c34b5d8-3362-45f5-8c13-72e7984d2f3c){: width="300" height="150" }


<br/>

### **🍳 실행 해보기**

🔻 index.js

![index](https://github.com/leekoby/leekoby.github.io/assets/118284808/b77849f7-e787-428a-b127-f03bf1377a8d)

![npx](https://github.com/leekoby/leekoby.github.io/assets/118284808/0ba2f1f5-0284-4755-8cdf-117924d53354){: width="300" height="150" }

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/4e478a01-7d80-4513-8931-e28a6642af0b)


### **🍳 명령어에 옵션 붙이기**

process.argv로 명령어에 어떤 옵션이 주어졌는지 확인 가능(배열로 표시)

- 코드가 바뀔 때마다 전역 설치할 필요는 없음

- package.json 내용이 바뀌면 다시 전역 설치해야 함

- 배열의 첫 요소는 노드의 경로, 두 번째 요소는 cli 명령어의 경로, 나머지는 옵션

🔻 index.js

![index](https://github.com/leekoby/leekoby.github.io/assets/118284808/606087f5-8b77-46af-86b5-8f320387528f)

![npx](https://github.com/leekoby/leekoby.github.io/assets/118284808/f40436a9-ff10-4c23-b1dc-e390a7380576){: width="300" height="150" }

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f2e78ec3-2d90-48c5-b93b-ccae91b58076)

### **🍳 사용자로부터 입력 받기**

노드 내장 모듈 readline 사용

- createInterface 메서드로 rl 객체를 만듦

- process.stdin, process.stdout은 각각 콘솔을 통해 입력받고 출력한다는 의미

- question 메서드로 질문을 표시하고 답변이 들어오면 콜백 함수가 실행됨

- 답변은 answer 매개변수에 담김

🔻 index.js

![index](https://github.com/leekoby/leekoby.github.io/assets/118284808/71fe0097-d335-428b-a58b-6047e95b7298)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/066f487f-a799-4313-bc53-c27b7083c523)

<br/>

### **🍳 템플릿을 만들어주는 명령어 만들기**

🔻 template.js

![template](https://github.com/leekoby/leekoby.github.io/assets/118284808/2994cfcc-5670-4117-ac57-b4d44c47d424)

<br/>

### **🍳 템플릿을 만들어주는 명령어 만들기**

- 디렉토리가 존재하는지 확인하는 exist 함수와 디렉토리를 생성하는 mkdirp 함수를 만듦

- program이라는 함수는 template.js의 실행부

- makeTemplate은 옵션을 읽어서 알맞은 템플릿을 작성해주는 함수

- 옵션에 따라 다른 동작을 하도록 분기 처리

- package.json의 명령어를 바꿔주고 전역 재설치

🔻 package.json

![pakagejson](https://github.com/leekoby/leekoby.github.io/assets/118284808/8acb140c-81f1-4320-a0de-0c46b06e0396)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b1a3a1da-9c5c-49b0-b91d-0b2d3d1dc884)

<br/>

---

<br/>

## **💻 Commender, Inquier 사용하기**

### **🍳 패키지로 쉽게 CLI 프로그램 만들기**

npm에는 CLI 프로그램을 위한 라이브러리가 많이 준비되어 있음

commander(CLI)와 inquirer(사용자와 상호작용)를 사용해서 예제를 만들어 봄

14.1의 프로그램을 commander와 inquirer로 재작성할 것

![install](https://github.com/leekoby/leekoby.github.io/assets/118284808/9797d625-2cbb-44e2-91cb-051819efd18c){: width="300" height="150" }

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

![commandjs](https://github.com/leekoby/leekoby.github.io/assets/118284808/3630ea63-4d33-4062-8f9c-fe38d8fab1fb)

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

- 다른 사용자가 `npm i –g <패키지명>`을 한다면 다운로드 받아 사용할 수 있음

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