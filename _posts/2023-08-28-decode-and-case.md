---
layout: post
title: DECODE와 CASE문
author: admin
date: 2023-08-28 06:00:00 +900
lastmod: 2023-08-28 06:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [DATABASE(DB), SQLD]  # 대문자로 작성
tags: [sql, sqld, data modeiling] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# **🌈 DECODE와 CASE문**

## **💻 DECODE**

**<span style="color:#ff6600">DECODE문으로 IF문을 구현할 수 있다. 즉, 특정 조건이 참이면 A, 거짓이면 B로 응답한다.</span>**

![DECODE](https://github.com/leekoby/leekoby.github.io/assets/118284808/bed84d13-f05f-4ae6-bd44-c9e098e509bc){: width="500" height="500" }

- 위의 예는 EMPNO와 1000과 비교해서 같으면 `TRUE`를 출력하고 다르면 `FALSE`를 출력한다.

![DECODE](https://github.com/leekoby/leekoby.github.io/assets/118284808/0132c6e0-90b3-4a9a-a15b-644bb252a33b){: width="500" height="500" }

- 위의 예는 EMP테이블에서 EMPNO와 1000과 비교해서 같으면 `TRUE`를 출력하고 다르면 `FALSE`를 출력한다.


<br/>

## **💻 CASE**

**<span style="color:#ff6600">CASE문은 IF〜THEN 〜ELSE-END의 프로그래밍 언어처럼 조건문을 사용할 수 있다</span>**

조건을 `WHEN`구에 사용하고 `THEN`은 해당 조건이 참이면 실행되고 거짓이면 `ELSE`구가 실행된다.

![CASE](https://github.com/leekoby/leekoby.github.io/assets/118284808/4fc3b102-3631-4928-8399-f5d3659e6e6c){: width="500" height="500" }

![CASE](https://github.com/leekoby/leekoby.github.io/assets/118284808/096724d6-c0f5-496b-b2df-b03275c5188b){: width="500" height="500" }

- 위의 예는 EMPNO가 1000이면 `A`를 출력하고 1001이면 `B`를 출력한다. 만약 그렇지 않으면 `C`를 출력한다.

<br/>

## 🎯 **기출 내용 추가**

### ❌

#### 👉

<br/>


# 📚 **레퍼런스**

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
{:.prompt-info }

> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
{:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.2 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-2/dashboard){:target="_blank"}
{:.prompt-info }

