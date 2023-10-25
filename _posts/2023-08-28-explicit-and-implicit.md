---
layout: post
title: 명시적(Explicit) 형변환과 암시적(Implicit) 형변환
author: admin
date: 2023-08-28 00:00:00 +900
lastmod: 2023-08-28 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, SQLD]  # 대문자로 작성
tags: [sql, sqld, explicit, implicit] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br/>

# **🌈 명시적(Explicit) 형변환과 암시적(Implicit) 형변환**

## **💻 형변환**

**<span style="color:#ff6600">형변환이라는 것은 두 개의 데이터의 데이터 타입(형)이 일치하도록 변환하는 것</span>**이다.

- 예를 들어 숫자와 문자열의 비교, 문자열과 날짜형의 비교와 같이 데이터 타입이 불일치할 때 발생한다.

**<span style="color:#ff6600">형변환은 명시적(Explicit) 형변환과 암시적(Implicit) 형변환</span>**이 있다.

> **<span style="color:#3366ff">명시적 형변환</span> : <span style="color:#ff6600">변환 함수를 사용하여 데이터 유형 반환을 명시적으로 나타냄</span>**
> 
> **<span style="color:#3366ff">암시적(암묵적) 형변환</span> : <span style="color:#ff6600">데이터베이스가 내부적으로 알아서 데이터 유형을 변환함</span>**
{:.prompt-info}

예를 들어 조건절에서 VARCHAR 유형의 BIRTHDAY 컬럼을 숫자와 비교할 경우

- 데이터베이스는 오류가 발생하지 않고

- 내부적으로 BIRTHDAY 컬럼을 NUMBER형으로 변환하게 되는데 이럴 때 쓰이는 것이 `암시적 형변환`이다.

![암시적 형변환](https://github.com/leekoby/leekoby.github.io/assets/118284808/2f498be2-d287-4437-a036-6d859cf14ee2)

> 암시적 형변환이 가능하다고 해서 컬럼의 데이터 유형을 고려하지 않고 SQL 작성을 하게 되면 성능 저하가 발생할 수 있고 
> 때에 따라서는 에러가 발생하기 때문에 되도록 명시적 형변환을 사용하는 것이 좋다.
{:.prompt-warning}

<br/>

## **💻 형변환 함수**

|형변환 함수|설명|
|:-|:-|
|TO_NUMBER(문자열)|문자열을 숫자로 변환한다.|
|TO_CHAR(숫자 | 날짜[, 포맷])|숫자 혹은 날짜를 지정된 포맷의 문자로 변환한다.|
|TO_DATE(문자열, 포맷)|문자열을 지정된 포맷의 날짜형으로 변환한다.|

**날짜 포맷**

|포맷 표현|의미|포맷 표현|의미|
|:-:|:-:|:-:|:-:|
|YYYY|년|HH|시(12)|
|MM|월|HH24|시(24)|
|DD|일|MI|분|
|||SS|초|

<br/>

## 🎯 **추가 내용**

### ❌

#### 👉

<br/>


## 📚 **레퍼런스**

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
{:.prompt-info }

> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
{:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.2 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-2/dashboard){:target="_blank"}
{:.prompt-info }