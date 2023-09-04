---
layout: post
title: 명시적(Explicit) 형변환과 암시적(Implicit) 형변환
author: admin
date: 2023-08-28 00:00:00 +900
lastmod: 2023-08-28 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, explicit, implicit] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# **🌈 명시적(Explicit) 형변환과 암시적(Implicit) 형변환**

## **형변환**

**<span style="color:#ff6600">형변환이라는 것은 두 개의 데이터의 데이터 타입(형)이 일치하도록 변환하는 것</span>**이다.

- 예를 들어 숫자와 문자열의 비교, 문자열과 날짜형의 비교와 같이 데이터 타입이 불일치할 때 발생한다.

**<span style="color:#ff6600">형변환은 명시적(Explicit) 형변환과 암시적(Implicit) 형변환</span>**이 있다.

>**<span style="color:#3366ff">명시적 형변환은</span> <span style="color:#ff6600">형변환 함수를 사용해서 데이터 타입을 일치시키는 것으로 개발자가 SQL을 사용할 때 형변환 함수를 사용해야 한다.</span>**

**형변환 함수**

|형변환 함수|설명|
|:-|:-|
|TO_NUMBER(문자열)|문자열을 숫자로 변환한다.|
|TO_CHAR(숫자 혹은 날짜, [FORMAT])|숫자 혹은 날짜를 지정된 FORMAT의 문자로 변환한다.|
|TO_DATE(문자열, FORMAT)|문자열을 지정된 FORMAT의 날짜형으로 변환한다.|


>**<span style="color:#3366ff">암시적(암묵적) 형변환은</span> <span style="color:#ff6600">개발자가 형변환을 하지 않은 경우 데이터베이스 관리 시스템이 자동으로 형변환하는 것을 의미한다.</span>**

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