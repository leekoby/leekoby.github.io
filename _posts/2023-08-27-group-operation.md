---
layout: post
title: GROUP 연산
author: admin
date: 2023-08-27 18:00:00 +900
lastmod: 2023-08-27 18:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, group operation] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br />


# **🌈 GROUP 연산**

## **💻 GROUP BY 문**

```sql
SELECT [DISTINCT] 칼럼명 [ALIAS명]
FROM 테이블명
[WHERE 조건식]
[GROUP BY 컬럼이나 표현식]
[HAVING 그룹조건식]
```

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

> **<span style="color:#3366ff">GROUP BY 절과 HAVING 절의 특성</span>** 
>
> -**<span style="color:#ff6600"> GROUP BY 절을 통해 소그룹별 기준</span>**을 정한 후, **<span style="color:#ff6600">SELECT 절에 집계 함수를 사용</span>**한다.
> 
> - 집계 함수의 통계정보는 **<span style="color:#ff6600">NULL 값을 가진 행을 제외</span>**하고 수행한다.
> 
> - GROUP BY 절에서는 SELECT 절과는 달리 **<span style="color:#ff6600">ALIAS 명을 사용할 수 없다.</span>**
>
> - **<span style="color:#ff6600">집계 함수는 WHERE 절에는 올 수 없다. </span>**
>   - (집계 함수를 사용할 수 있는 GROUP BY 절보다 WHERE 절이 먼저 실행된다.) 
> 
> - WHERE 절은 전체 데이터를 GROUP으로 나누기 전에 **<span style="color:#ff6600">행들을 미리 제거</span>**시킨다.
> 
> - **<span style="color:#ff6600">HAVING 절은</span>** GROUP BY 절의 기준 항목이나 소그룹의 **<span style="color:#ff6600">집계 함수를 이용한 조건을 표시</span>**할 수 있다.
> 
> - GROUP BY 절에 의한 소그룹별로 만들어진 집계 데이터 중, HVAING 절에서 제한 조건을 두어 조건을 만족하는 내용만 출력한다.
> 
> - HAVING 절은 일반적으로 GROUP BY 절 뒤에 위치한다.
{:.prompt-info }


<br/>

## **💻 HAVING 문**

**<span style="color:#ff6600">GROUP BY에 조건절을 사용하려면 HAVING을 사용</span>**해야 한다. 

HAVING 절은 주로 GROUP BY 뒤에 오면서 집계 데이터에 대한 조건을 부여하지만 테이블 전체가 한 개의 그룹이 되는 경우 HAVING만 단독으로 사용할 수 있다.

만약 WHERE절에 조건문을 사용하게 되면 조건을 충족하지 못하는 데이터들은 GROUP BY 대상에서 제외된다.

HAVING절은 논리적으로 SELECT절 전에 수행되기 때문에 SELECT 절에 명시되지 않은 집계 함수로도 조건을 부여할 수 있다.

- SELECT 절에서 정의한 **<span style="color:#ff6600">ALIAS를 사용할 수 없다</span>**.
WHERE 절에서 사용해도 되는 조건까지 HAVING절로 써버리면 성능상 불리할 수도 있다(오류는 발생하지 않음).

- 왜냐하면 WHERE절에서 필터링이 선행되어야 GROUP BY할 데이터량이 줄어들기 때문

- GROUP BY는 비교적 많은 비용이 드는 작업이므로 수행 전에 데이터량을 최소로 줄여놓는 것이 좋다. 

>**<span style="color:#ff6600">중요한 것은 WHERE절에는 집계(SUM, AVG, MAX, MIN 등)함수를 사용하면 안된다.</span>**<br/>
{:.prompt-warning}

![HAVING 문](https://github.com/leekoby/leekoby.github.io/assets/118284808/e0e81b6c-05cc-4e63-8e43-240a554946e2){: width="500" height="500" }

<br/>

## **💻 집계 함수 종류**

|집계함수|설명|
|:-:|:-|
|COUNT(*)|- 전체 ROW를 COUNT하여 반환|
|COUNT(컬럼)| - 컬럼값이 NULL인 ROW를 제외하고 COUNT하여 반환|
|COUNT(DISTICT 컬럼)| - 컬럼값이 NULL이 아닌 ROW에서 중복을 제거한 COUNT를 반환|
|SUM(컬럼)| - NULL인 ROW를 제외하고 컬럼값들의 합계를 계산한다.|
|AVG(컬럼)| - NULL인 ROW를 제외하고 컬럼값들의 평균을 계산한다.|
|MIN(컬럼)| - 컬럼값들의 최솟값을 반환|
|MAX(컬럼)| - 컬럼값들의 최댓값을 반환|
|STDDEV(컬럼)| - 컬럼값들의 표준편차를 계산한다.|
|VARIAN(컬럼)| - 컬럼값들의 분산을 계산한다.|

### **COUNT 함수**

**<span style="color:#ff6600">COUNT( ) 함수는 행 수를 계산하는 함수</span>**이다. 
  
**<span style="color:#ff6600">COUNT(*)는 NULL 값을 포함한 모든 행 수를 계산한다. 하지만 COUNT(컬럼명)는 NULL 값을 제외한 행 수를 계산</span>**한다,

![COUNT(*) 사용](https://github.com/leekoby/leekoby.github.io/assets/118284808/3344a4d6-f8cf-4884-9c9e-6b0266500585){: width="500" height="500" }


![COUNT(MGR) 사용](https://github.com/leekoby/leekoby.github.io/assets/118284808/492e5e23-1857-44b5-a87b-79fa6dc8ac45){: width="500" height="500" }

- MGR 컬럼을 한 개의 NULL을 가지고 있다. 그래서 COUNT(MGR)로 하면 NULL이 제외되고 행 수를 계산한다.

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