---
layout: post
title: OPTIMIZER (옵티마이저)
author: admin
date: 2023-08-31 10:00:00 +900
lastmod: 2023-08-31 10:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, optimizer] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br/>

# **🌈 OPTIMIZER (옵티마이저)**

## **💻 OPTIMIZER (옵티마이저)와 실행 계획**

### **OPTIMIZER (옵티마이저)**

SQL개발자가 SQL을 작성하여 실행할 때, 옵티마이저는 SQL을 어떻게 실행할 것인지를 계획하게 된다. 

- 즉 SQL 실행 계획(Execution Plan)을 수립하고 SQL을 실행한다.

**<span style="color:#ff6600">옵티마이저는 SQL의 실행 계획을 수립하고 SQL을 실행하는 데이터베이스 관리 시스템의 소프트웨어이다.</span>**
  
동일한 결과가 나오는 SQL도 어떻게 실행하느냐에 따라서 성능이 달라진다.

따라서 옵티마이저의 실행 계획은 SQL 성능에 아주 중요한 역할을 한다

<br/>

### **옵티마이저 특징**

옵티마이저는 데이터 딕셔너리(Data Dictionary)에 있는 오브젝트 통계, 시스템 통계 등의 정보를 사용해서 예상되는 비용을 산정한다.

**<span style="color:#ff6600">옵티마이저는 여러 개의 실행 계획 중에서 최저비용을 가지고 있는 계획을 선택해서 SQL을 실행한다.</span>**

<br/>

### **옵티마이저의 필요성**

SQL개발자가 작성한 SQL문을 어떻게 실행하느냐에 따라 성능이 달라진다

![SQL 실행 구조](https://github.com/leekoby/leekoby.github.io/assets/118284808/1eb08fe7-3560-4ed2-acec-ed52c03b630d){: width="500" height="500" }

- 위의 예를 보면 먼저 EMP 테이블을 실행하고 EMP 테이블에서 찾은 행과 동일한 것을 DEPT 테이블에서 찾는다. 그리고 최종 결과집합을 인출한다.

- 앞의 예를 보면 건수가 많은 EMP 테이블을 먼저 읽고 DEPT 테이블을 읽으면 불필요하게 비교 횟수가 증가하게 된다. 

- 만약, DEPT 테이블을 먼저 읽고 EMP 테이블을 읽게 되면 비교 횟수를 줄일 수 있다.

- 본 SQL문은 AND 조건이므로 작은 집합을 먼저 읽어도 큰 집합을 먼저 읽는 것과 동일한 결과가 나오게 된다.

- 옵티마이저는 이러한 실행 계획을 수립하는 것이며, 만약 옵티마이저가 비효율적으로 실행 계획을 수립하면, SQL개발자는 SQL을 개선해야 한다. 

- 이때 옵티마이저에게 실행 계획을 변경하도록 요청할 수가 있는데 이때 **<span style="color:#ff6600">힌트(HINT)를 사용</span>**한다.

<br/>

### **옵티마이저 실행 계획 확인**

**<span style="color:#ff6600">옵티마이저는 SQL 실행 계획을 PLAN_TABLE에 저장한다.</span>**

그래서 SQL개발자는 PLAN_TABLE을 조회해서 실행 계획을 확인할 수 있다.

![PLAN_TABLE](https://github.com/leekoby/leekoby.github.io/assets/118284808/6a6eebec-d322-489d-a471-5af2fe83ffda){: width="500" height="500" }

- 가장 편리하게 실행 계획을 확인하는 법은 TOAD에서 Execution Plan Current SQL 메뉴를 클릭하면 된다.

![TABLE ACCESS FULL](https://github.com/leekoby/leekoby.github.io/assets/118284808/bc27974d-110a-4516-a3ba-6bcaf34f69d3){: width="500" height="500" }

위의 예를 보면 `TABLE ACCESS FULL`이라고 되어 있다. 이것은 EMP 테이블의 전체를 모두 읽었다는 의미이다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e90a49b4-4ea3-4d2a-b8ef-e98751bf8e98){: width="500" height="500" }

- 가장 편리하게 실행 계획을 확인하는 방법은 SQL DEVELOPER에서 `F10`, 즉 `계획설명`을 클릭하면 된다. 

- 위의 예에서 SELECT * FROM EMP; 쿼리를 실행했을 때의 실행 계획이 된다. 

- 여기서는 TABLE ACCESS를 FULL로 되어 있기 때문에 EMP테이블의 데이터를 모두 읽었다는 의미이다.

<br/>


## **💻 옵티마이저의 종류**

### **옵티마이저의 실행 방법**

개발자가 SQL을 실행하면 파싱(Parsing)을 실행해서 SQL의 문법 검사 및 구문분석을 수행한다.

**<span style="color:#ff6600">구문분석이 완료되면 옵티마이저가 규칙 기반 혹은 비용 기반으로 실행 계획을 수립한다.</span>**

**<span style="color:#ff6600">옵티마이저는 기본적으로 비용 기반 옵티마이저를 사용해서 실행 계획을 수립한다. </span>**

**<span style="color:#ff6600">비용 기반 옵티마이저는 통계정보를 활용해서 최적의 실행 계획을 수립하는 것이다.</span>**

실행 계획 수립이 완료되면 최종적으로 SQL을 실행하고 실행이 완료되면 데이터를 인출(Fetch)한다.

![옵티마이저의 실행 방법](https://github.com/leekoby/leekoby.github.io/assets/118284808/29f7ad8d-c896-48da-9dcb-b34faacd4abf){: width="500" height="500" }


### **옵티마이저 엔진**

규칙 기반 옵티마이저(Rule base Optimizer)는 실행 계획을 수립할 때 15개의 우선순위를 기준으로 실행 계획을 수립한다.


|옵티마이저|역할|
|:-|:-|
|Query Transformer	|- SQL문을 효율적으로 실행하기 위해서 옵티마이저가 변환한다. <br/> - SQL이 변환되어도 그 결과는 동일하다.|
|Estimator|	- 통계정보를 사용해서 SQL 실행비용을 계산한다.<br/>- 총비용은 최적의 실행 계획을 수립하기 위해서이다.|
|Plan Generator	|SQL을 실행할 실행 계획을 수립한다.|

**<span style="color:#ff6600">최신 Oracle 버전은 규칙 기반 옵티마이저 보다 비용 기반 옵티마이저를 기본적으로 사용한다.</span>**

|우선순위	|설명|
|:-:|:-|
|1	|- ROWID를 사용한 단일 행인 경우|
|2	|- 클러스터 조인에 의한 단일 행인 경우|
|3	|- 유일하거나 기본키를 가진 해시 클러스터 키에 의한 단일 행인 경우|
|4	|- 유일하거나 기본키에 의한 단일 행인 경우|
|5	|- 클러스터 조인인 경우|
|6	|- 해시 클러스터 조인인 경우|
|7	|- 인덱스 클러스터 키인 경우|
|8	|- 복합 컬럼 인덱스인 경우|
|9	|- 단일 컬럼 인덱스인 경우|
|10	|- 인덱스가 구성된 컬럼에서 제한된 범위를 검색하는 경우|
|11	|- 인덱스가 구성된 컬럼에서 무제한 범위를 검색하는 경우|
|12	|- 정렬-병합(Sort Merge) 조인인 경우|
|13	|- 인덱스가 구성된 컬럼에서 MAX 혹은 MIN을 구하는 경우|
|14	|- 인덱스가 구성된 컬럼에서 ORDER BY를 실행하는 경우|
|15	|- 전체 테이블을 스캔(FULL TABLE SCAN) 하는 경|

- 규칙 기반 옵티마이저에서 우선순위 1위인 ROWID를 사용한 조회를 확인해 보면 다음과 같다.

![우선순위 1위인 ROWID를 사용한 조회](https://github.com/leekoby/leekoby.github.io/assets/118284808/5f766eb4-591f-44ab-89f9-a222c0cec3b2){: width="500" height="500" }

- 위의 SQL문은 `/*+ RULE */`를 사용해서 옵티마이저에서 규칙 기반 옵티마이저로 실행하도록 알려주었다. 이렇게 옵티마이저에서 실행방법을 알려주는 것이 힌트(HINT)이다.

- 실행 계획을 확인한 결과 EMP 테이블을 ROWID로 조회했다.

<br/>

### **비용 기반 옵티마이저**

비용 기반 옵티마이저(Cost base Optimizer)는 오브젝트 통계 및 시스템 통계를 사용해서 총비용을 계산한다.

**<span style="color:#ff6600">총비용이라는 것은 SQL문을 실행하기 위해서 예상되는 소요시간 혹은 자원의 사용량을 의미한다.</span>**

총비용이 적은 쪽으로 실행 계획을 수립한다. 

- 단, 비용 기반 옵티마이저에서 통계정보가 부적절한 경우 성능 저하가 발생할 수 있다.

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

