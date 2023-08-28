---
layout: post
title: Data Control Language (DCL)
author: admin
date: 2023-08-28 20:00:00 +900
lastmod: 2023-08-28 20:00:00 +900
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

# **🌈 Data Control Language (DCL)**

## **💻 GRANT**

**<span style="color:#ff6600">GRANT문은 데이터베이스 사용자에게 권한을 부여</span>**한다.

데이터베이스 사용을 위해서는 권한이 필요하며 연결, 입력, 수정, 삭제, 조회를 할 수 있다.

```sql
GRANT privileges ON object TO user;
```
- privileges는 권한을 의미하며 object는 테이블명이다.
- user는 Oracle 데이터베이스 사용자를 지정하면 된다


>**Privileges(권한)의 종류**

|권한|설명|
|-|-|
|SELECT|지정된 테이블에 대해서 SELECT 권한을 부여한다.|
|INSERT|지정된 테이블에 대해서 INSERT 권한을 부여한다.|
|UPDATE|지정된 테이블에 대해서 UPDATE 권한을 부여한다.|
|DELETE|지정된 테이블에 대해서 DELETE 권한을 부여한다.|
|REFERENCES|지정된 테이블을 참조하는 제약조건을 생성하는  권한을 부여한다.|
|ALTER|지정된 테이블에 대해서 수정할 수 있는 권한을 부여한다.|
|INDEX|지정된 테이블에 대해서 인덱스를 생성할 수 있는 권한을 부여한다.|
|ALL|테이블에 대한 모든 권한을 부여한다.|

![GRANT](https://github.com/leekoby/leekoby.github.io/assets/118284808/51821b87-5630-4108-851b-c931788f53d9)


> **WITH GRANT OPTION**

- WITH GRANT OPTION

  - 특정 사용자에게 권한을 부여할 수 있는 권한을 부여한다.

  - 권한을 A 사용자가 B에 부여하고 B가 다시 C를 부여한 후에 권한을 취소(Revoke)하면 모든 권한이 회수된다.

  - B와 C의 권한 회수

- WITH ADMIN OPTION

  - 테이블에 대한 모든 권한을 부여한다. 

  - 권한을 A 사용자가 B에 부여하고 B가 다시 C에게 부여한 후에 권한을 취소(Revoke)하면 B사용자 권한만 취소된다.

![GRANT](https://github.com/leekoby/leekoby.github.io/assets/118284808/4c2ae38e-4fe3-4410-aede-584307fa73a6)


## **REVOKE**

**<span style="color:#ff6600">REVOKE문은 데이터베이스 사용자에게 부여된 권한을 회수한다.</span>**

REVOKE문
```sql
REVOKE privileges ON object FROM user;
```
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

