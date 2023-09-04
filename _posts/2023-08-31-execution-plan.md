---
layout: post
title: EXECUTION PLAN (실행계획)
author: admin
date: 2023-08-31 16:00:00 +900
lastmod: 2023-08-31 16:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, data modeiling] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# **🌈 EXECUTION PLAN (실행계획)**

## **💻 실행 계획**

다음은 EMP 테이블과 DEPT 테이블을 조인하고 EMP 테이블의 DEPTNO 번호가 10번인 것을 조회하는 SQL이다.

![TOAD를 기준](https://github.com/leekoby/leekoby.github.io/assets/118284808/62410636-47a6-4e40-ac78-3e6fbba2a7b0){: width="500" height="500" }

![SQL DEVELOPER를 실행한 화면](https://github.com/leekoby/leekoby.github.io/assets/118284808/8a754d45-24b1-40cc-807c-5e0e48a6e547){: width="500" height="500" }

- 위의 예 왼쪽은 TOAD를 기준으로 실행한 것이고, 오른쪽은 SQL DEVELOPER를 실행한 화면이다. 

- 위의 SQL문의 실행 계획을 읽는 방법은 위에 나와 있는 번호 순서대로 읽으면 된다.

- 1번은 DEPT 테이블의 SYS_C007959 인덱스를 유일하게 조회(INDEX UNIQUE SCAN)하였다.

- 2번은 INDEX에서 DEPT 테이블 ROWID를 사용해서 조회하였다.


- 3번은 EMP 테이블을 전체 스캔(FULL SCAN) 하였다.

- 그리고 마지막 4번은 DEPT 테이블과 EMP 테이블을 Nested Loop 방식의 조인을 해서 최종 결과를 만들어 낸 것이다.

- Nested Loop 방식의 조인은 DEPT 테이블에서 먼저 데이터를 찾고 그다음 EMP 테이블을 찾는 것을 의미한다. 이러한 것을 Random Access라고 한다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f6783894-973d-4d96-821d-795253e69451){: width="500" height="500" }

- 위의 SQL 실행 흐름도를 보면 INDEX를 검색하고 ROWID를 사용해서 DEPT 테이블을 조회하는 것을 보여주고 있다.

- 이때 먼저 조회되는 테이블을 Outer Table이라고 하고 그다음에 조회되는 테이블을 Inner Table이라고 한다.

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

> [SQL 개발자 (SQLD) 자격증 따기 Part.3 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-3){:target="_blank"}
{:.prompt-info }

