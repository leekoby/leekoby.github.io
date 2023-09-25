---
layout: post
title: 5. 패키지 매니저
author: admin
date: 2023-04-24 00:00:00 +900
lastmod: 2023-04-24 00:00:00 +900
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

# **🌈 패키지 매니저**

## **💻 npm 알아보기**

Node Package Manager

- 노드의 패키지 매니저

- 다른 사람들이 만든 소스 코드들을 모아둔 저장소

- 남의 코드를 사용하여 프로그래밍 가능

- 이미 있는 기능을 다시 구현할 필요가 없어 효율적

- 오픈 소스 생태계를 구성중

- `패키지`: npm에 업로드된 노드 모듈

- 모듈이 다른 모듈을 사용할 수 있듯 패키지도 다른 패키지를 사용할 수 있음

- `의존 관계`라고 부름

<br/>

---

<br/>

## **💻 pakage.json으로 패키지 관리하기**

### **🍳 pakage.json**

현재 프로젝트에 대한 정보와 사용 중인 패키지에 대한 정보를 담은 파일

- 같은 패키지라도 버전별로 기능이 다를 수 있으므로 버전을 기록해두어야 함

- 동일한 버전을 설치하지 않으면 문제가 생길 수 있음

- 노드 프로젝트 시작 전 package.json부터 만들고 시작함(npm init)


<br/>

### **🍳 pakage.json 속성들**

`package name`

- 패키지의 이름. package.json의 name 속성에 저장

`version`

- 패키지의 버전

- npm의 버전은 다소 엄격하게 관리

`entry point`
- 자바스크립트 실행 파일 진입점

- 보통 마지막으로 module.exports를 하는 파일을 지정

- package.json의 main 속성에 저장

`test command`

- 코드를 테스트할 때 입력할 명령어를 의미

- package.json scripts 속성 안의 test 속성에 저장

`git repository`
- 코드를 저장해둔 Git 저장소 주소를 의미

- 나중에 소스에 문제가 생겼을 때 사용자들이 이 저장소에 방문해 문제를 제기할 수도 있고, 코드 수정본을 올릴 수도 있다. 

- package.json의 repository 속성에 저장

`keywords`

- 키워드는 [npm 공식 홈페이지](https://npmjs.com){:target="_blank"}에서 패키지를 쉽게 찾을 수 있게 해준다. 

- package.json의 keywords 속성에 저장

`license`
- 해당 패키지의 라이선스

<br/>

### **🍳 npm 스크립트**

npm init이 완료되면 폴더에 package.json이 생성됨

🔻 package.json

```js
{
  "name": "npmtest",
  "version": "1.0.0",
  "description": "저를 읽어주세요.",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index"
    // start는 이미 많이 사용 중이라서 npm start라고 해도됨.
  },
  "author": "seokahi",
  "license": "MIT"
}
```

npm run [스크립트명]으로 스크립트 실행

>👉 "test": "echo \"Error: no test specified\" && exit 1" 실행

> **<span style="color:#3366ff">라이센스</span>** 
>
> 오픈 소스라고 해서 모든 패키지를 제약없이 사용할 수 있는 것은 아니다
>
> 라이센스 별로 제한 사항이 있으므로 설치 전에 반드시 라이선스를 확인해야 한다.
>
> ISC, MIT나 BSD 라이선스를 가진 패키지는 사용한 패키지와 라이선스만 밝히면 자유롭게 사용할 수 있다.
>
> 아파치(Apache) 라이선스 패키지는 사용은 자유롭지만 특허권에 대한 제한이 포함되어 있다.
>
> GPL 계열의 패키지를 사용한 패키지를 배포할 때는 자신의 패키지도 GPL로 배포하고 소스 코드도 공개해야 한다.
>
> 라이선스별로 특징이 다르므로 오픈 소스를 사용하기 전에는 반드시 라이선스를 확인하고 세부 내용을 읽어봐야한다.
> 
> 그렇지 않으면 이후 상용 프로그램을 개발했을 때 법적 문제가 생길 수 있습니다.
{:.prompt-tip}

<br/>

### **🍳 패키지 설치하기**

express 설치하기

package.json에 기록됨(dependencies에 express 이름과 버전 추가됨)

![dependencies에 express 이름과 버전 추가됨](https://github.com/leekoby/leekoby.github.io/assets/118284808/b7b0fe1c-35b9-4948-878f-f1e66419c125)


`dependencies`: 배포할 때까지 사용

`devDependencies`: 개발할 때만 사용

<br/>


### **🍳 node_modules**

npm install 시 node_modules 폴더 생성

- 내부에 설치한 패키지들이 들어 있음

- express 외에도 express와 의존 관계가 있는 패키지들이 모두 설치됨

- package-lock.json도 생성되어 패키지 간 의존 관계를 명확하게 표시함

![node_modules](https://github.com/leekoby/leekoby.github.io/assets/118284808/1fd2b540-f274-4c39-b8a9-9898fa363493)


<br/>

### **🍳 여러 패키지 동시 설치하기**

> npm install 패키지1 패키지2 패키지3 …


<br/>

### **🍳 개발용 패키지**

> npm install --save-dev 패키지명 또는 npm i -D 패키지명

`devDependencies`에 추가됨

<br/>

### **🍳 글로벌(전역) 패키지**

> npm install --global 패키지명 또는 npm i -g 패키지명

- 모든 프로젝트와 콘솔에서 패키지를 사용할 수 있음

- 예제는 rm –rf(리눅스의 삭제 명령)를 흉내내는 rimraf 패키지의 글로벌 설치

`rimraf`: 무언가를 지우는 모듈

> npm i rimraf -D 

- 개발용으로 설치, packet.json에 넣어줌

- npx로 글로벌 설치 없이 글로벌 명령어 사용 가능


<br/>

---

<br/>

## **💻 패키지 버전 이해하기**

### **🍳 SemVer 버저닝(유의적 버저닝)**

노드 패키지의 버전은 SemVer(유의적 버저닝) 방식을 따름

- Major(주 버전), Minor(부 버전), Patch(수 버전)

- 노드에서는 배포를 할 때 항상 버전을 올려야 함

- `Major`는 하위 버전과 호환되지 않은 수정 사항이 생겼을 때 올림

- `Minor`는 하위 버전과 호환되는 수정 사항이 생겼을 때 올림

- `Patch`는 기능에 버그를 해결했을 때 올림

![SemVer 버저닝](https://github.com/leekoby/leekoby.github.io/assets/118284808/01b3c002-cbfe-43e6-9886-fc7d1e0cef2a)

<br/>

### **🍳 버전 기호 사용하기**

버전 앞에 기호를 붙여 의미를 더함

`^1.1.1`: 패키지 업데이트 시 `minor 버전까지만` 업데이트 됨(2.0.0버전은 안 됨)

`~1.1.1`: 패키지 업데이트 시 `patch버전까지만` 업데이트 됨(1.2.0버전은 안 됨)

=, <=, >, <는 이상, 이하, 초과, 미만.

`@latest`는 `최신 버전을 설치`하라는 의미

실험적인 버전이 존재한다면 `@next`로 `실험적인 버전 설치` 가능(불안정함)

각 버전마다 부가적으로 알파/베타/RC 버전이 존재할 수도 있음

- (1.1.1-alpha.0, 2.0.0-beta.1, 2.0.0-rc.0)

<br/>

---

<br/>

## **💻 기타 npm 명령어**

`npm outdated`: 어떤 패키지에 기능 변화가 생겼는지 알 수 있음

![npm outdated](https://github.com/leekoby/leekoby.github.io/assets/118284808/431dca6d-3ca3-4878-bce4-56ad1916af44)

- `npm update`: package.json에 따라 패키지 업데이트

- `npm uninstall` 패키지명: 패키지 삭제(npm rm 패키지명으로도 가능)

- `npm search 검색어`: npm 패키지를 검색할 수 있음(npmjs.com에서도 가능)

- `npm info 패키지명`: 패키지의 세부 정보 파악 가능

- `npm login`: npm에 로그인을 하기 위한 명령어(npmjs.com에서 회원가입 필요)

- `npm whoami`: 현재 사용자가 누구인지 알려줌

- `npm logout`: 로그인한 계정을 로그아웃

- `npm version 버전`: package.json의 버전을 올림(Git에 커밋도 함)

- `npm deprecate [패키지명][버전] [메시지]`: 패키지를 설치할 때 경고 메시지를 띄우게 함(오류가 있는 패키지에 적용)

- `npm publish`: 자신이 만든 패키지를 배포

- `npm unpublish --force`: 자신이 만든 패키지를 배포 중단(배포 후 72시간 내에만 가능)

  - 다른 사람이 내 패키지를 사용하고 있는데 배포가 중단되면 문제가 생기기 때문

- 기타 명령어는 [https://docs.npmjs.com](https://docs.npmjs.com){:target="_blank"}의 CLI Commands에서 확인


<br/>

---

<br/>

## **💻 패키지 배포하기**

### **1️⃣ npm 회원가입**

[npmjs.com](https://npmjs.com){:target="_blank"}에 접속해서 회원가입

![npm 회원가입](https://github.com/leekoby/leekoby.github.io/assets/118284808/44f49871-b341-4d8b-bf91-94af18700c65)


<br/>

### **2️⃣ 배포할 패키지 작성**

package.json의 main 부분의 파일명과 일치해야 한다.

- 그래야 npm에서 이파일이 패키지의 진입점임을 알 수 있다.

“main”: “index.js”

🔻 index.js
```js
module.exports = () => {
  return 'hello package';
};
```
<br/>

### **3️⃣ 배포 시도하기**

npm publish 입력

- npmtest란 이름을 누가 사용중

- 이름이 겹치면 안 되므로 다른 것으로 바꿔서 배포

<br/>

### **4️⃣ 배포 시도하기**

이름을 변경한 후 `npm publish` 입력

<br/>

### **5️⃣ 배포 취소하기**

72시간 내에 `npm unpublish 패키지명 --force` 입력

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

>[npm 공식 웹 사이트](https://npmjs.com){:target="_blank"}
{:.prompt-info}

>[yarn 공식 웹 사이트](https://yarnpkg.com){:target="_blank"}
{:.prompt-info}

>[npm 명령어 설명서](https://docs.npmjs.com/cli){:target="_blank"}
{:.prompt-info}

>[패키지 간 비교 사이트](https://npmcompare.com){:target="_blank"}
{:.prompt-info}

>[패키지 다운로드 추이 확인](https://www.npmtrends.com){:target="_blank"}
{:.prompt-info}

>[패키지 이름에 네임스페이스 설정하기](https://docs.npmjs.com/misc/scope){:target="_blank"}
{:.prompt-info}

>[release-it](https://github.com/release-it/release-it){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}
