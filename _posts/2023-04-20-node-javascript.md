---
layout: post
title: 2. 알아두어야할 자바스크립트
author: admin
date: 2023-04-20 06:00:00 +900
lastmod: 2023-04-20 06:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NODE, NODEJS교과서]  # 대문자로 작성
tags: [node, nodejs, 노드교과서] # 소문자로 작성
---

> 기존 블로그에 작성했던 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `NODEJS`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---

<br/>

# **🌈 알아두어야할 자바스크립트**

## **💻 호출 스택**

```js
function first() {
  second();
  console.log('첫 번째');
}
function second() {
  third();
  console.log('두 번째');
}
function third() {
  console.log('세 번째');
}

first();

// 세 번째 -> 두 번째 -> 첫 번째
```

쉽게 파악이 안된다면 호출스택을 머리속으로 그려보자

![호출 스택](https://github.com/leekoby/leekoby.github.io/assets/118284808/ccff8a7b-7135-4df9-83aa-da0dbadcc1c9){: width="500" height="500" }

**호출 스택(함수의 호출, 자료구조의 스택)**

- Anonymous는 가상의 전역 컨텍스트(항상 있다고 생각하는게 좋음, 실행이 모두 끝나면 사라짐)

- 함수 호출 순서대로 쌓이고, 역순으로 실행됨

- 함수 실행이 완료되면 스택에서 빠짐

- LIFO 구조라서 스택이라고 불림

<br/>

---

<br/>

## **💻 이벤트 루프**

**이벤트 루프** 

- 이벤트 발생 시 호출할 콜백 함수들을 관리하고, 호출된 콜백 함수의 실행 순서를 결정하는 역할을 담당

- 노드가 종료될 때까지 이벤트 처리를 위한 작업을 반복하므로 루프라고 부른다.

**백그라운드**

- `setTimeout` 같은 타이머나 이벤트 리스너들이 대기하는 곳 

- 자바스크립트가 아닌 다른 언어로 작성된 프로그램이라고 봐도 무방

- 여러 작업이 동시에 실행될 수 있음 

**태스크 큐**

- 이벤트 발생 후, 백그라운드에서는 태스크 큐로 타이머나 이벤트 리스너의 콜백 함수를 보냄

- 정해진 순서대로 콜백들이 줄을 서 있으므로 콜백 큐 라고도 함 

- 콜백들은 보통 완료된 순서대로 줄을 서 있지만, 특정한 경우 순서가 바뀌기도 함

```js
function run() {
  console.log('3초 후 실행');
}
console.log('시작');
setTimeout(run, 3000);
console.log('끝');

// 시작 -> 끝 -> 3초 후 실행
```

비동기 함수 `setTimeout(run, 3000)`은 호출되면 백그라운드에 쌓인다.

호출스택에 다음 코드인 `console.log('끝')` 이 불려서 실행되는 와중에도 `백그라운드`에서 3초를 세며 자신의 동작인 `run을 대기`한다. ( 호출스택과 백그라운드가 동시에 작동된다.)

3초가 지나면 백그라운드에 남아있는 동작 run이 `태스크 큐`로 이동한다. 

호출 스택에 모든게 끝나서 텅텅 비어있다면(annonymous 포함) run을 호츨스택에 올려 `console.log('3초 후 실행')이 동작`한다.

>Promise의 then/catch가 setTimeout보다 더 먼저 작동함(새치기)

<br/>

---

<br/>

## **💻 화살표 함수**

```js
let relationship1 = {
  name: 'zero',
  friends: ['nero', 'hero', 'xero'],
  logFriends: function () {
    let that = this; // relationship1을 가리키는 this를 that에 저장
    this.friends.forEach(function (friend) {
      console.log(that.name, friend);
    });
  },
};
relationship1.logFriends();
// zero nero
// zero hero
// zero xero
```


**화살표 함수가 기존 function() {}을 대체하는건 아님(this가 달라짐)**

- logFriends 메소드의 this값에 주목

- forEach의 function의 this와 logFriends의 this는 다름

- that이라는 중간변수를 이용해서 logFriends의 this를 전달

```js
let relationship2 = {
  name: 'zero',
  friends: ['nero', 'hero', 'xero'],
  logFriends() {
    this.friends.forEach(friend => {
      console.log(this.name, friend);
    });
  },
};
relationship2.logFriends();
```

**forEach의 인자로 화살표 함수가 들어간 것에 주목**

- forEach의 화살표함수의 this와 logFriends의 this가 같아짐

- 화살표 함수는 자신을 포함하는 함수의 this를 물려받음

- 물려받고 싶지 않을 때 function() {}을 사용

this를 쓸거면 기존함수, this를 쓰지 않아도 되면 화살표함수 사용을 권장한다. (오해 방지)


<br/>

---

<br/>

## **💻 클래스**

**프로토타입 문법을 깔끔하게 작성할 수 있는 Class 문법 도입**

Constructor(생성자), Extends(상속) 등을 깔끔하게 처리할 수 있음

코드가 그룹화되어 가독성이 향상됨

<br/>

---

<br/>

## **💻 Promise**

**콜백 헬이라고 불리는 지저분한 자바스크립트 코드의 해결책**

- 프로미스? 내용이 실행은 되었지만 결과를 아직 반환하지 않은 객체

- `Then`을 붙이면 결과를 반환함

- 실행이 완료되지 않았으면 완료된 후에 Then 내부 함수가 실행됨

- `Resolve(성공리턴값)` -> `then`으로 연결

- `Reject(실패리턴값)` -> `catch`로 연결

`Finally` 부분은 무조건 실행됨

```js
const condition = true; // true면 resolve, false면 reject
const promise = new Promise((resolve, reject) => {
  if (condition) {
    resolve('성공');
  } else {
    reject('실패');
  }
});
// 다른 코드가 들어갈 수 있음
promise
  .then((message) => {
	console.log(message); // 성공한 경우 실행(resolve)
})
  .catch((error) => {
	console.error(error); // 실패한 경우 실행(reject)
})
```


**Promise.all(배열): 여러개의 프로미스를 동시에 실행**

- 하나라도 실패하면 catch로 감

- allSettled로 실패한 것만 추려낼 수 있음

<br/>

---

<br/>

## **💻 async/await**

변수 = await 프로미스; 인 경우 프로미스가 resolve된 값이 변수에 저장

변수 await 값인 경우 그 값이 변수에 저장

```js
async function findAndSaveUser(Users) {
  let user = await Users.findOne({});
  user.name = 'zero';
  user = awajit user.save();
  user = await Users.findOne({ gender: 'm' });
// 생략
}
```

<br/>

---

<br/>

## **💻 널 병합/옵셔널 체이닝**

### **🍳 `??` (널 병합(nullish coalescing))**

널 병합 연산자는 주로 `||`연산자 대용으로 사용된다.

FALSY 값 (0, '', false, NaN, null, undefined) 중 `null`과 `undefined`만 따로 구분한다.

```js
const a = 0;
const b = a || 3; // || 연산자는 falsy 값이면 뒤로 넘어감
console.log(b); // 3

const c = 0;
const d = c ?? 3; // ?? 연산자는 null과 undefined일 때만 뒤로 넘어감
console.log(d); // 0;

const e = null;
const f = e ?? 3;
console.log(f); // 3;

const g = undefined;
const h = g ?? 3;
console.log(h); // 3;
```

<br/>

### **🍳 `?.` (옵셔널 체이닝(optional chaining))**

옵셔널 체이닝 연산자는 null이나 undefined의 속성을 조회하는 경우 에러가 발생하는 것을 막는다.

```js
const a = {}
a.b; // a가 객체이므로 문제없음

const c = null;
try {
  c.d;
} catch (e) {
  console.error(e); // TypeError: Cannot read properties of null (reading 'd')
}
c?.d; // 문제없음

try {
  c.f();
} catch (e) {
  console.error(e); // TypeError: Cannot read properties of null (reading 'f')
}
c?.f(); // 문제없음

try {
  c[0];
} catch (e) {
  console.error(e); // TypeError: Cannot read properties of null (reading '0')
}
c?.[0]; // 문제없음
```

<br/>

---

<br/>

## **💻 AJAX**

**서버로 요청을 보내는 코드**

- 라이브러리 없이는 브라우저가 지원하는 `XMLHttpRequest` 객체 이용

- AJAX 요청 시 Axios 라이브러릴 사용하는게 편함

<br/>

---

<br/>

## **💻 FormData**

HTML form 태그에 담긴 데이터를 AJAX 요청으로 보내고 싶은 경우 

- FormData 객체 이용

FormData 메서드

- Append 로 데이터를 하나씩 추가

- has 로 데이터 존재 여부 확인

- Get 으로 데이터 조회

- GetAll 로 데이터 모두 조회

- delete 로 데이터 삭제

- set 으로 데이터 수정


```js
const formData = new FormData();
formData.append('name', 'zerocho');
formData.append('item', 'orange');
formData.append('item', 'melon');
formData.has('item'); // true
formData.has('money'); // false;
formData.get('item');// orange
formData.getAll('item'); // ['orange', 'melon'];
formData.append('test', ['hi', 'zero']);
formData.get('test'); // hi, zero
formData.delete('test');
formData.get('test'); // null
formData.set('item', 'apple');
formData.getAll('item'); // ['apple'];
```

```js
(async () => {
  try {
    const formData = new FormData();
    formData.append('name', 'zerocho');
    formData.append('birth', 1994);
    const result = await axios.post('https://www.zerocho.com/api/post/formdata', formData);
    console.log(result);
    console.log(result.data);
  } catch (error) {
    console.error(error);
  }
})();
```


<br/>

---

<br/>

## **💻 encodeURIComponent, decodeURIComponent**

한글 입력을 서버가 처리하지 못하는 경우 발생

- encodeURIComponent 로 감싸줘서 처리 

```js
(async () => {
  try {
    const result = await axios.get(`https://www.zerocho.com/api/search/${encodeURIComponent('노드')}`);
    console.log(result);
    console.log(result.data); // {}
  } catch (error) {
    console.error(error);
  }
})();
```

받는 쪽에서는 decodeURIComponent를 사용

역시 브라우저뿐만 아니라 노드에서도 사용할 수 있다.

```js
decodeURIComponent('%EB%85%B8%EB%93%9C'); // 노드
```

<br/>

---

<br/>

## **💻 데이터 속성과 dataset**

**HTML 태그에 데이터를 저장하는 방법**

- 서버의 데이터를 프론트엔드로 내려줄 때 사용

- 태그 속성으로 `data-속성명`

- 자바스크립트에서 `태그.dataset.속성명` 으로 접근 가능

  - data-user-job => dataset.userjob

  - data-id => dataset.id

- 반대로 자바스크립트 dataset에 값을 넣으면 data-속성이 생김 

  - dataset.monthSalary = 10000 => data-month-salary="10000"

```html
<ul>
  <li data-id="1" data-user-job="programmer">Zero</li>
  <li data-id="2" data-user-job="designer">Nero</li>
  <li data-id="3" data-user-job="programmer">Hero</li>
  <li data-id="4" data-user-job="ceo">Kero</li>
</ul>
<script>
  console.log(document.querySelector('li').dataset);
  // { id: '1', userJob: 'programmer' }
</script>
```

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

>[MDN 자바스크립트(저자의 추천)](https://developer.mozilla.org/ko/docs/Web/JavaScript){:target="_blank"}

>[ES 상세 후보군](https://github.com/tc39/proposals){:target="_blank"}

>[ES2015+ 브라우저/서버 호환 여부](http://kangax.github.io/compat-table/es6/){:target="_blank"}

>[브라우저별 기능 지원 여부 확인](https://caniuse.com/){:target="_blank"}

>[노드 버전별 ECMAScript 스펙](http://node.green){:target="_blank"}

>[AJAX 설명](https://developer.mozilla.org/ko/docs/Web/Guide/AJAX){:target="_blank"}

>[axios](https://github.com/axios/axios){:target="_blank"}

>[FormData 설명](https://developer.mozilla.org/ko/docs/Web/API/FormData){:target="_blank"}

>[ESLint 툴](https://eslint.org/){:target="_blank"}

>[에어비앤비 코딩 스타일](https://github.com/airbnb/javascript){:target="_blank"}

>[저자의 블로그 ES2015+](https://zerocho.com/category/EcmaScript){:target="_blank"}

>[모던 자바스크립트 튜토리얼](https://ko.javascript.info/){:target="_blank"}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}

