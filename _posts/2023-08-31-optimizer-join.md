---
layout: post
title: OPTIMIZER JOIN (옵티마이저 조인)
author: admin
date: 2023-08-31 17:00:00 +900
lastmod: 2023-08-31 17:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, SQLD]  # 대문자로 작성
tags: [sql, sqld, data modeiling] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br/>

# **🌈 OPTIMIZER JOIN (옵티마이저 조인)**

## **💻 Nested Loop 조인**

**<span style="color:#ff6600">Nested Loop 조인은 하나의 테이블에서 데이터를 먼저 찾고 그다음 테이블을 조인하는 방식으로 실행된다.</span>**

Nested Loop 조인에서 먼저 조회되는 테이블을 외부 테이블(Outer Table)이라고 하고 그다음 조회되는 테이블을 내부 테이블(Inner Table)이라고 한다.

Nested Loop 조인에서는 외부 테이블(선행 테이블)의 크기가 작은 것을 먼저 찾는 것이 중요하다.

-  그래야 데이터가 스캔되는 범위를 줄일 수 있기 때문이다.

**<span style="color:#ff6600">Nested Loop 조인은 RANDOM ACCESS가 발생하는데 RANDOM ACCESS가 많이 발생하면 성능 지연이 발생한다.</span>** 

- **<span style="color:#ff6600">그러므로 Nested Loop 조인은 RANDOM ACCESS의 양을 줄여야 성능이 향상된다.</span>**

![Nested Loop 조인](https://github.com/leekoby/leekoby.github.io/assets/118284808/065396f6-c836-4bc6-ba6d-d8be27f9a938){: width="500" height="500" }

- 위의 예는 Nested Loop 조인 방식을 도식화한 것이다. 

- 즉, T_CUST_SALE의 인덱스를 먼저 검색하고 T_CUST_SALE 인덱스에 있는 ROWID를 사용해서 T_CUST_SALE 테이블을 읽는다.

- 그다음은 T_CUST_SALE에서 T_CUST_MAST 테이블의 인덱스를 찾는다. 

- 이 부분을 RANDOM ACCESS라고 한다.

- T_CUST_MAST 인덱스를사용해서 다시 T_CUST_MAST 테이블에서 데이터를 찾는다. 

- 여기까지 실행되면 모든 데이터를 다 찾은 것이다. 

- 그다음은 인출(Fetch)을 실행해서 전송한다.

![Nested Loop 조인](https://github.com/leekoby/leekoby.github.io/assets/118284808/78e0067e-6acd-47b8-9eeb-11620de7c43a){: width="500" height="500" }

- 위의 예는 USE_NL 힌트를 사용해서 의도적으로 Nested Loop 조인을 실행했다.

- 실행 계획을 보면 EMP 테이블을 먼저 FULL SCAN하고 그다음 DEPT 테이블을 FULL SCAN 하여 Nested Loop 조인을 하는 것이다.

- **<span style="color:#ff6600">ORDERED 힌트는 FROM절에 나오는 테이블 순서대로 조인을 하게 하는 것이다. </span>**

- **<span style="color:#ff6600">ORDERED 힌트는 혼자 사용되지 않고 USE_NL, USE_MERGE, USE_HASH 힌트와 함께 사용된다.</span>**

<br/>

## **💻 Sort Merge 조인**

Sort Merge 조인은 두 개의 테이블을 SORT_AREA라는 메모리 공간에 모두 로딩 (Loading) 하고 SORT를 수행한다.

두 개의 테이블에 대해서 SORT가 완료되면 두 개의 테이블을 병합(Merge)한다.

Sort Merge 조인은 정렬(SORT)이 발생하기 때문에 데이터양이 많아지면 성능이 떨어지게 된다.

정렬 데이터양이 너무 많으면 정렬은 임시 영역에서 수행된다. 

임시 영역은 디스크에 있기 때문에 성능이 급격히 떨어진다.

![Sort Merge ](https://github.com/leekoby/leekoby.github.io/assets/118284808/caa807ff-60ae-453f-9bdc-09ca1d51e8ac){: width="500" height="500" }

![Sort Merge ](https://github.com/leekoby/leekoby.github.io/assets/118284808/91400bdd-797f-431f-abc5-eab702d8cd26){: width="500" height="500" }

- 위의 예는 Oracle 데이터베이스 힌트를 사용해서 의도적으로 SORT MERGE 조인을 한 것이다.

- use_merge 힌트를 사용해서 SORT MERGE 조인을 할 수 있다. 

- 단, use_merge 힌트는 ORDERED 힌트와 같이 사용해야 한다.

<br/>

## **💻 Hash 조인**

- Hash 조인은 두 개의 테이블 중에서 작은 테이블을 HASH 메모리에 로딩하고 두 개의 테이블의 조인 키를 사용해서 해시 테이블을 생성한다.

- Hash 조인은 해시 함수를 사용해서 주소를 계산하고 해당 주소를 사용해서 테이블을 조인하기 때문에 CPU 연산을 많이 한다.

- 특히 Hash 조인 시에는 선행 테이블이 충분히 메모리에 로딩되는 크기여야 한다.

![Hash 조인](https://github.com/leekoby/leekoby.github.io/assets/118284808/c8a069ae-197e-48b2-a045-51d5f9204a01){: width="500" height="500" }

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

> [SQL 개발자 (SQLD) 자격증 따기 Part.3 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-3){:target="_blank"}
{:.prompt-info }

