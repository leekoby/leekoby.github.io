---
layout: post
title: GROUP 연산
author: admin
date: 2023-08-27 18:00:00 +900
lastmod: 2023-08-27 18:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [DATABASE(DB), SQLD]  # 대문자로 작성
tags: [sql, sqld, group operation] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />


# **🌈 GROUP 연산**

## **💻 GROUP BY 문**

**<span style="color:#ff6600">GROUP BY는 테이블에서 소규모 행을 그룹화하여 `합계`, `평균`, `최댓값`, `최솟값` 등을 계산</span>**할
수 있다.

**<span style="color:#ff6600">HAVING구에 조건문을 사용</span>**한다.

**<span style="color:#ff6600">ORDER BY를 사용해서 정렬</span>**을 할 수 있다.

![GROUP BY 문](https://github.com/leekoby/leekoby.github.io/assets/118284808/5ee118bd-4196-4d09-90c4-9566a057e547){: width="500" height="500" }

- 위의 GROUP BY 예에서 ①번은 EMP 테이블을 그대로 조회한 것이다. 
  즉, `SELECT DEPTNO, SAL FROM EMP`를 실행한 것이다.

- ②번은 DEPTNO로 그룹을 만들고 

- ③번은 그룹별 합계를 계산하라는 뜻이다.

- ④번은 부서 별 합계를 계산하게 된다.

<br/>

## **💻 HAVING 문**

**<span style="color:#ff6600">GROUP BY에 조건절을 사용하려면 HAVING을 사용</span>**해야 한다. 

만약 WHERE절에 조건문을 사용하게 되면 조건을 충족하지 못하는 데이터들은 GROUP BY 대상에서 제외된다.

>**<span style="color:#ff6600">중요한 것은 WHERE절에는 집계(SUM, AVG, MAX, MIN 등)함수를 사용하면 안된다.</span>**<br/>
{:.prompt-warning}

![HAVING 문](https://github.com/leekoby/leekoby.github.io/assets/118284808/e0e81b6c-05cc-4e63-8e43-240a554946e2){: width="500" height="500" }

<br/>

## **💻 집계 함수 종류**

|집계함수|설명|
|:-:|:-|
|COUNT()|행 수를 조회한다.|
|SUM()|합계를 계산한다.|
|AVG()|평균을 계산한다.|
|MAX() 와 MIN()|최댓값과 최솟값을 계산한다.|
|STDDEV()|표준편차를 계산한다.|
|VARIAN()|분산을 계산한다.|

### **COUNT 함수**

**<span style="color:#ff6600">COUNT( ) 함수는 행 수를 계산하는 함수</span>**이다. 
  
**<span style="color:#ff6600">COUNT(*)는 NULL 값을 포함한 모든 행 수를 계산한다. 하지만 COUNT(칼럼명)는 NULL 값을 제외한 행 수를 계산</span>**한다,

![COUNT(*) 사용](https://github.com/leekoby/leekoby.github.io/assets/118284808/3344a4d6-f8cf-4884-9c9e-6b0266500585){: width="500" height="500" }


![COUNT(MGR) 사용](https://github.com/leekoby/leekoby.github.io/assets/118284808/492e5e23-1857-44b5-a87b-79fa6dc8ac45){: width="500" height="500" }

- MGR 칼럼을 한 개의 NULL을 가지고 있다. 그래서 COUNT(MGR)로 하면 NULL이 제외되고 행 수를 계산한다.

## **💻 GROUP BY 사용 예제**

### **부서별(DEPTNO), 관리자별(MGR) 급여평균 계산**

- 부서별, 관리자별 급여평균이므로 GROUP BY에 부서와 관리자를 추가한다.

- 평균을 계산하기 위해서 SELECT문에 AVG 함수를 사용해야 한다.

![부서별(DEPTNO), 관리자별(MGR) 급여평균 계산](https://github.com/leekoby/leekoby.github.io/assets/118284808/405756e5-7a07-4410-bc93-d8a141dcbd5e){: width="500" height="500" }

<br/>

### **직업별 급여합계 중에 급여합계가 1000 이상인 직업**

- 직업별 급여합계이므로 GROUP BY에 JOB을 포함시키고 급여합계가 1000 이상만 조회해야 하므로 HAVING구에 조건을 넣어야 한다.

![직업별 급여합계 중에 급여합계가 1000 이상인 직업](https://github.com/leekoby/leekoby.github.io/assets/118284808/596ba7c3-f6a9-44dd-90b9-67d6a16b20f7){: width="500" height="500" }

<br/>

### **사원번호 1000~1003번의 부서별 급여 합계**

- 사원번호 1000번에서 10003번까지 조회를 해야 하므로 WHERE문에 조건을 넣어야 한다.

- 그리고 부서별 합계이므로 GROUP BY에 DEPTNO를 사용하고 SELECT문에 SUM 함수
를 사용한다.

![사원번호 1000~1003번의 부서별 급여 합계](https://github.com/leekoby/leekoby.github.io/assets/118284808/f7764dc6-4e3e-4f7c-b759-88bbf2f54744){: width="500" height="500" }

<br/>

## **💻 SELECT문 실행 순서**

SQL의 실행 순서는 결과로 조회된 데이터를 이해하는 데 아주 중요한 요소이다.

**<span style="color:#ff6600">SELECT문의 실행 순서는 FROM, WHERE, GROUP BY, HAVING, SELECT, ORDER BY 순으로 실행</span>**된다.

![SELECT문 실행 순서](https://github.com/leekoby/leekoby.github.io/assets/118284808/e26fb4fa-430e-4138-b223-669d8201b73c){: width="500" height="500" }

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