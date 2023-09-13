---
layout: post
title: 3.7 이벤트 이해하기
author: admin
date: 2023-04-22 08:00:00 +900
lastmod: 2023-04-22 08:00:00 +900
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

# **🌈 이벤트 이해하기**

## **💻 이벤트 만들고 호출하기**

🔻 event.js

```js
const EventEmitter = require('events');

const myEvent = new EventEmitter();
myEvent.addListener('event1', () => {
  console.log('이벤트 1');
});
myEvent.on('event2', () => {
  console.log('이벤트 2');
});
myEvent.on('event2', () => {
  console.log('이벤트 2 추가');
});
myEvent.once('event3', () => {
  console.log('이벤트 3');
}); // 한 번만 실행됨

myEvent.emit('event1'); // 이벤트 호출
myEvent.emit('event2'); // 이벤트 호출

myEvent.emit('event3');
myEvent.emit('event3'); // 실행 안 됨

myEvent.on('event4', () => {
  console.log('이벤트 4');
});
myEvent.removeAllListeners('event4');
myEvent.emit('event4'); // 실행 안 됨

const listener = () => {
  console.log('이벤트 5');
};
myEvent.on('event5', listener);
myEvent.removeListener('event5', listener);
myEvent.emit('event5'); // 실행 안 됨

console.log(myEvent.listenerCount('event2'));
```

<br/>

events 모듈로 커스텀 이벤트를 만들 수 있음

- 스트림에 쓰였던 on(‘data’), on(‘end’) 등과 비교

- `on(이벤트명, 콜백)`
  
  - 이벤트 이름과 이벤트 발생 시의 콜백을 연결
  
  -  이렇게 연결하는 동작을 `이벤트 리스닝` 
  
  -  event2처럼 이벤트 하나에 이벤트 여러 개를 달아줄 수도 있다.

- `addListener(이벤트명, 콜백)`
  
  - on과 기능이 같다.

- `emit(이벤트명)`
  
  - 이벤트를 호출하는 메서드
  
  - 이벤트 이름을 인자로 넣어주면 미리 등록해뒀던 이벤트 콜백이 실행

- `once(이벤트명, 콜백)`
  - 한 번만 실행되는 이벤트 
  
  - myEvent.emit('event3')을 두 번 연속 호출했지만 콜백이 한 번만 실행

- `removeAllListeners(이벤트명)`
  
  - 이벤트에 연결된 모든 이벤트 리스너를 제거
  
  - event4가 호출되기 전에 리스너를 제거했으므로 event4의 콜백은 호출되지 않는다.

- `removeListener(이벤트명, 리스너)`
  
  - 이벤트에 연결된 리스너를 하나씩 제거
  
  - 역시* event5의 콜백도 호출되지 않는다.

- `off(이벤트명, 콜백)`
  
  - 노드 10 버전에서 추가된 메서드로, removeListener와 기능이 같다.

- `listenerCount(이벤트명)`
  
  - 현재 리스너가 몇 개 연결되어 있는지 확인

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}