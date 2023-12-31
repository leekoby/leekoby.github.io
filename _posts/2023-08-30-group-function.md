---
layout: post
title: GROUP FUNCTION (그룹 함수)
author: admin
date: 2023-08-30 06:00:00 +900
lastmod: 2023-08-30 06:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, SQLD]  # 대문자로 작성
tags: [sql, sqld, group function] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br/>

# **🌈 GROUP FUNCTION (그룹 함수)**

## **💻 GROUPING 함수**

|||
|:-|:-|
|집계 함수| COUNT, SUM, AVG, MAX, MIN 등|
|소계(총계) 함수|ROLLUP, CUBE, GROUPING SETS 등|

**<span style="color:#ff6600">GROUPING 함수는 ROLLUP, CUBE, GROUPING SETS에서 생성되는 합계값을 구분하기 위해서 만들어진 함수이다.</span>**

> `ROLLUP` 함수는 인수의 순서에 따라 결과가 달라지며 `CUBE` 함수와 `GROUPING SETS` 함수는 인수의 순서가 바뀌어도 같은 결과를 출력한다.
{:.prompt-tip }


- 예를 들어 소계, 합계 등이 계산되면 GROUPING 함수는 1을 반환하고 그렇지 않으면 0을 반환해서 합계값을 식별할 수 있다.

![GROUPING 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/2539b6aa-a260-4668-be8a-076723675b8b){: width="500" height="500" }

- 위의 SQL문을 보면 소계와 합계가 계산된 데이터는 GROUPING 함수에서 1이 출력된 것을 알 수 있다.

- GROUPING의 반환값을 DECODE 혹은 CASE문으로 식별해서 SELECT문으로 ‘소계’,  ‘합계’를 구분하는 것이다.

![GROUPING 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/74a78699-79e0-4b97-b265-749d1b0d878f){: width="500" height="500" }

- 위의 예를 보면 DECODE 함수를 사용해서 GROUPING 함수 결과가 ‘1’이면 '전체합계' 혹은 부서합계를 출력하고 그렇지 않으면 NULL을 반환한다.

- GROUPING 함수의 기능을 사용하면 사용자가 필요로 하는 데이터를 SELECT문으로 작성하여 제공할 수 있다.

<br/>

## **💻 ROLLUP**

|||
|:-|:-|
| ROLLUP(A)| - A로 그룹핑 <br/> - 총합계|
| ROLLUP(A, B)| - A, B로 그룹핑 <br/> - A로 그루핑 <br/> - 총합계|
| ROLLUP(A, B, C)| - A, B, C로 그룹핑 <br/> - A, B로 그루핑 <br/> - A로 그루핑 <br/> - 총합계|

**<span style="color:#ff6600">ROLLUP은 GROUP BY의 컬럼에 대해서 Subtotal을 만들어 준다.</span>**

ROLLUP을 할 때 GROUP BY구에 컬럼이 두 개 이상 오면 순서에 따라서 결과가 달라진다.

![ROLLUP](https://github.com/leekoby/leekoby.github.io/assets/118284808/c0202582-d8c7-4c7b-8f24-35ed50f56016){: width="500" height="500" }

- 위의 예는 DEPTNO에 대해서 GROUP BY로 급여합계를 계산하고 부서별 전체합계를 추가해서 계산했다. 

- 즉, ROLLUP은 DEPTNO에 대해서 기존 GROUP BY와는 다르게 부서별 전체합계를 계산하게 된다.

- DECODE문은 전체합계를 조회할 때 '전체합계'라는 문자를 출력하기 위해서 사용된다. 

- DECODE문을 사용해서 DEPTNO가 NULL과 같으면 '전체합계'라는 문자를 출력하고 그렇지 않으면 부서번호(DEPTNO)를 출력한다.

![ROLLUP](https://github.com/leekoby/leekoby.github.io/assets/118284808/6b104401-d0ab-40b5-a417-3c8ae8d96dc8){: width="500" height="500" }

- 부서별, 직업별 ROLLUP을 실행하면 부서별 합계, 직업별 합계, 전체합계가 모두 조회된다.

- ROLLUP으로 실행되는 컬럼별로 Subtotal을 만들어 준다.

<br/>

## **💻 CUBE 함수**

|||
|:-|:-|
| CUBE(A)| - A로 그룹핑 <br/> - 총합계|
| CUBE(A, B)| - A, B로 그룹핑 <br/> - A로 그루핑 <br/> - B로 그루핑 <br/> - 총합계|
| CUBE(A, B, C)| - A, B, C로 그룹핑 <br/> - A, B로 그루핑 <br/> - A, C로 그루핑 <br/> - B, C로 그루핑 <br/> - A로 그루핑 <br/> - B로 그루핑 <br/> - C로 그루핑 <br/> - 총합계|

**<span style="color:#ff6600">CUBE는 CUBE 함수에 제시한 컬럼에 대해서 결합 가능한 모든 집계를 계산</span>**한다.

소그룹 간의 소계 및 총계를 다차원 집계를 제공하여 다양하게 데이터를 분석할 수 있게 한다.

- 예를 들어 부서와 직업을 CUBE로 사용하면 부서별 합계, 직업별 합계, 부서별 직업별 합계, 전체합계가 조회된다.

- 즉, 조합할 수 있는 경우의 수가 모두 조합되는 것이다.

![CUBE 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/ea1c6268-98fb-4c55-9ac7-f777b99c052e){: width="500" height="500" }

- 위의 예에서는 CUBE로 부서코드와 직업을 실행했다. 

- 그 결과 전체합계, 직업별 합계, 부서별 합계, 부서별 직업별 합계가 조회됐다.

<br/>

## **💻 GROUPING SETS 함수**

|||
|:-|:-|
|GROUPING SETS(A, B)| - A로 그룹핑 <br/> - B로 그룹핑|
|GROUPING SETS(A, B, ())| - A로 그룹핑 <br/> - B로 그룹핑 <br/> - 총합계|
|GROUPING SETS(A, ROLLUP(B))| - A로 그룹핑 <br/> - B로 그룹핑 <br/> - 총합계|
|GROUPING SETS(A, ROLLUP(B, C))| - A로 그룹핑 <br/> - B, C로 그룹핑 <br/> - B로 그룹핑 <br/> - 총합계|
|GROUPING SETS(A, B, ROLLUP(C))| - A로 그룹핑 <br/> - B로 그룹핑 <br/> - C로 그룹핑 <br/> - 총합계|

**<span style="color:#ff6600">특정 항목에 대한 소계를 계산</span>**

**<span style="color:#ff6600">인자값으로 ROLLUP이나 CUBE 사용가능</span>**

**<span style="color:#ff6600">GROUPING SETS 함수는 GROUP BY에 나오는 컬럼의 순서와 관계없이 다양한 소계를 만들 수 있다.</span>**

GROUPING SETS 함수는 GROUP BY에 나오는 컬럼의 순서와 관계없이 개별적으로 모두 처리한다.

![GROUPING SETS 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/6785da6b-dd30-4a4b-b01d-e1cd16dda8c5){: width="500" height="500" }

- 위의 예를 보면 GROUPING SETS 함수로 DEPTNO와 JOB을 실행했다.

- **<span style="color:#ff6600">그 결과 DEPTNO 합계과 JOB 합계가 개별적으로 조회되었다. </span>**

- **<span style="color:#ff6600">즉, 서로 관계가 없다는 것이다.</span>**

<br/>

## **💻 GROUPING 함수**

GROUPING 함수는 ROLLUP, CUBE, GROUPING SETS 등과 함께 쓰이며 소계를 나타내는 ROW를 구별할 수 있게 해준다.

GROUPING을 함께 사용하지 않았을 때 소계 영역에서 NULL 또는 공란으로 표현되었지만, GROUPING 함수를 이용하면 원하는 위치에 원하는 텍스트를 출력할 수 있다.

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

