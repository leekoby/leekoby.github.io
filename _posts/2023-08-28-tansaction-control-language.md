---
layout: post
title: Transaction Control Language (TCL)
author: admin
date: 2023-08-28 21:00:00 +900
lastmod: 2023-08-28 21:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, SQLD]  # 대문자로 작성
tags: [sql, sqld, tansaction control language, tcl] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br/>

# **🌈 TCL(Transaction Control Language)**

**SQL 문장들의 종류**

| 종류 | 명령어 | 설명 |
|:-:|:-|:-|
| 데이터 조작어 <br/> DML <br/> (Data Manipulation Language) | SELECT |데이터베이스에 들어 있는 데이터를 조회하거나 <br/> 검색하기 위한 명령어를 말하는 것으로 RETRIEVE 라고도 한다. |
|  | INSERT <br/> UPDATE <br/> DELETE |데이터베이스의 테이블에 들어 있는 데이터에 변형을 하는 종류의 명령어들 <br/> 데이터를 테이블에 새로운 행을 집어넣거나 <br/> 원하지 않는 데이터를 수정/삭제하는 것들의 명령어들을 DML이라고 한다.|
|데이터 정의어 <br/> DDL <br/> (Data Definition Language)|CREATE <br/> ALTER <br/> DROP <br/> RENAME| 테이블과 같은 데이터 구조를 정의하는데 사용되는 명령어들 <br/> 구조를 생성하거나 변경하거나 삭제하거나 <br/> 이름을 바꾸는 데이터 구조와 관련된 명령어들을 DDL 이라고 부른다.|
|데이터 제어어 <br/> DCL <br/> (Data Control Language)| GRANT <br/> REVOKE| 데이터베이스에 접근하고 객체들을 사용하도록 권한을 주고 <br/> 회수하는 명령어를 DCL이라고 한다.|
|트랜잭션 제어어 <br/> TCL <br/> (Transaction Control Language) | COMMIT <br/> ROLLBACK | 논리적인 작업의 단위를 묶어서 DML에 의해 조작된 결과를<br/> 작업단위(트랜잭션) 별로 제어하는 명령어|

## **💻 COMMIT**

**<span style="color:#ff6600">COMMIT은 INSERT, UPDATE, DELETE문으로 변경한 데이터를 데이터베이스에 반영한다.</span>**

변경 이전 데이터는 잃어버린다. 

- 즉, A 값을 B로 변경하고 COMMIT을 하면 A 값은 잃어버리고 B 값을 반영한다.

다른 사용자는 변경된 값을 조회할 수 **<span style="color:#ff6600">없다.</span>**

**<span style="color:#ff6600">COMMIT이 완료되면 데이터베이스 변경으로 인한 LOCK이 해제(UNLOCK)된다.</span>**

COMMIT이 완료되면 다른 모든 데이터베이스 사용자는 변경된 데이터를 조작할 수 있다.

**<span style="color:#ff6600">COMMIT을 실행하면 하나의 트랜잭션 과정을 종료한다.</span>**

![COMMIT](https://github.com/leekoby/leekoby.github.io/assets/118284808/1a5e155f-65bb-41df-b589-2689b05040b9){: width="500" height="500" }

![COMMIT](https://github.com/leekoby/leekoby.github.io/assets/118284808/d13b0404-689b-4263-8d1b-817c41c88ea0){: width="500" height="500" }

- Oracle 데이터베이스는 암시적 트랜잭션 관리를 한다. 

- 즉, Oracle 데이터베이스로 트랜잭션을 시작하고 트랜잭션의 종료는 Oracle 데이터베이스 사용자가 COMMIT 혹은 ROLLBACKS 처리해야 한다.


**AUTO COMMIT**

- AUTO COMMIT 사용을 지양하자.

- SQL*PLUS 프로그램을 정상적으로 종료하는 경우 자동 COMMIT된다

- DDL 및 DCL을 사용하는 경우 자동 COMMIT된다

- "set autocommit on；"을 SQL+PLUS에서 실행하면 자동 COMMIT된다

## **💻 ROLLBACK**

**<span style="color:#ff6600">ROLLBACK을 실행하면 COMMIT 이전에 데이터에 대한 변경 사용을 모두 취소하고 트랜잭션을 종료한다.</span>**

**<span style="color:#ff6600">INSERT, UPDATE, DELETE문의 작업을 모두 취소한다. </span>**

- **<span style="color:#ff6600">같은 이름으로 저장한 SAVEPOINT가 있을 경우 처음의 SAVEPOINT로ROLLBACK된다.</span>**

- **<span style="color:#ff6600">단, 이전에 COMMIT한 곳까지만 복구한다.</span>**

ROLLBACK을 실행하면 LOCK이 해제되고 다른 사용자도 데이터베이스 행을 조작할 수 있다.

![ROLLBACK](https://github.com/leekoby/leekoby.github.io/assets/118284808/9e7f2c78-2e81-4325-8298-9e3d17d580e6){: width="500" height="500" }

## **💻 SAVEPOINT**

**<span style="color:#ff6600">SAVEPOINT는 트랜잭션을 작게 분할하여 관리하는 것으로 SAVEPOINT를 사용하면 지정된 위치 이후의 트랜잭션만 ROLLBACK 할 수 있다.</span>**

SAVEPOINT의 지정은 `SAVEPOINT〈SAVEPOINT명〉`을 실행한다,

**<span style="color:#ff6600">지정된 SAVEPOINT까지만 데이터 변경을 취소하고 싶은 경우는 “ROLLBACK TO 〈SAVEPOINT명〉”을 실행</span>**한다.

`ROLLBACK`을 실행하면 SAVEPOINT와 관계없이 데이터의 모든 변경사항을 저장하지 않는다.

![SAVEPOINT](https://github.com/leekoby/leekoby.github.io/assets/118284808/40a0dcf7-24af-4107-ae5e-36ae0094f301){: width="500" height="500" }

![SAVEPOINT](https://github.com/leekoby/leekoby.github.io/assets/118284808/d6915c96-4805-4502-ab64-af0db1c5af50){: width="500" height="500" }

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

