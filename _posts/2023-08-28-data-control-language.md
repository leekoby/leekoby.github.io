---
layout: post
title: Data Control Language (DCL)
author: admin
date: 2023-08-28 20:00:00 +900
lastmod: 2023-08-28 20:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, data control language, dcl] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br/>

# **🌈 Data Control Language (DCL)**

**SQL 문장들의 종류**

| 종류 | 명령어 | 설명 |
|:-:|:-|:-|
| 데이터 조작어 <br/> DML <br/> (Data Manipulation Language) | SELECT |데이터베이스에 들어 있는 데이터를 조회하거나 <br/> 검색하기 위한 명령어를 말하는 것으로 RETRIEVE 라고도 한다. |
|  | INSERT <br/> UPDATE <br/> DELETE |데이터베이스의 테이블에 들어 있는 데이터에 변형을 하는 종류의 명령어들 <br/> 데이터를 테이블에 새로운 행을 집어넣거나 <br/> 원하지 않는 데이터를 수정/삭제하는 것들의 명령어들을 DML이라고 한다.|
|데이터 정의어 <br/> DDL <br/> (Data Definition Language)|CREATE <br/> ALTER <br/> DROP <br/> RENAME| 테이블과 같은 데이터 구조를 정의하는데 사용되는 명령어들 <br/> 구조를 생성하거나 변경하거나 삭제하거나 <br/> 이름을 바꾸는 데이터 구조와 관련된 명령어들을 DDL 이라고 부른다.|
|데이터 제어어 <br/> DCL <br/> (Data Control Language)| GRANT <br/> REVOKE| 데이터베이스에 접근하고 객체들을 사용하도록 권한을 주고 <br/> 회수하는 명령어를 DCL이라고 한다.|
|트랜잭션 제어어 <br/> TCL <br/> (Transaction Control Language) | COMMIT <br/> ROLLBACK | 논리적인 작업의 단위를 묶어서 DML에 의해 조작된 결과를<br/> 작업단위(트랜잭션) 별로 제어하는 명령어|

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

<br/>

>**<span style="color:#3366ff">ROLE</span>** 
>
>**<span style="color:#ff6600">권한들을 묶어서 한꺼번에 부여한다.</span>**
>
>**<span style="color:#ff6600">DATABASE에서 OBJECT(테이블, 프로시져, 함수, 뷰, 트리거 등)의 권한을 묶어서 권한을 관리할 수가 있다.</span>**
{:.prompt-info }

## **REVOKE**

**<span style="color:#ff6600">REVOKE문은 데이터베이스 사용자에게 부여된 권한을 회수한다.</span>**

REVOKE문
```sql
REVOKE privileges ON object FROM user;
```
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

