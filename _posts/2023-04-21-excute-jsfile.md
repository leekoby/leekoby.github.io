---
layout: post
title: 3.2 JS 파일 실행하기
author: admin
date: 2023-04-21 04:00:00 +900
lastmod: 2023-04-21 04:00:00 +900
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

# **🌈 JS 파일 실행하기**

자바스크립트 파일을 만들어 통째로 코드를 실행하는 방법

- 아무 폴더(디렉터리)에서 helloWorld.js를 만들어보자

- node [자바스크립트 파일 경로]로 실행

- 실행 결과값이 출력됨

🔻helloworld.js

```js
function helloWorld() {
  console.log('Hello World');
  helloNode();
}

function helloNode() {
  console.log('Hello Node');
}

helloWorld();
```

✅ 결과

![JS 파일 실행하기](https://github.com/leekoby/leekoby.github.io/assets/118284808/055d3995-3ba3-403e-85a2-5f541ce54824){: width="500" height="500" }


<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}

