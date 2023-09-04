---
layout: post
title: WINDOW FUNCTION (윈도우 함수)
author: admin
date: 2023-08-30 22:00:00 +900
lastmod: 2023-08-30 22:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, window function] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# **🌈 WINDOW FUNCTION (윈도우 함수)**

## **💻 WINDOW FUNCTION (윈도우 함수)**

**<span style="color:#ff6600">윈도우 함수는 행과 행 간의 관계를 정의하기 위해서 제공되는 함수이다.</span>**

윈도우 함수를 사용해서 순위, 합계, 평균, 행 위치 등을 조작할 수 있다.

![윈도우 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/a2fafa99-09f8-4d25-b841-833c4431d38c){: width="500" height="500" }

### **윈도우 함수 구조**

|구조	| 설명|
| :- | :-|
|ARGUMENTS	|- 윈도우 함수에 따라서 0~N개의 인수를 설정한다.|
|PARTITION BY	| - **<span style="color:#ff6600">전체 집합을 기준에 의해 소그룹</span>**으로 나눈다.|
|ORDER BY	| - 어떤 항목에 대해서 정렬한다.|
|WINDOWING	| - 행 기준의 범위를 정한다. <br/>- **<span style="color:#ff6600">ROWS는 물리적 결과의 행 수</span>**이고 <br/> **<span style="color:#ff6600">RANGE는 논리적인 값에 의한 범위</span>**이다.|

<br/>

### **WINDOWING**

|구조|	설명|
|:-|:-|
|ROWS	| 부분집합인 윈도우 크기를 물리적 단위로 행의 집합을 지정한다.
|RANGE|	논리적인 주소에 의해 행 집합을 지정한다.|
|BETWEEN~AND|	윈도우의 시작과 끝의 위치를 지정한다.|
|UNBOUNDED PRECEDING	| 윈도우의 시작 위치가 첫 번째 행임을 의미한다.|
|UNBOUNDED FOLLOWING	| 윈도우의 마지막 위치가 마지막 행임을 의미한다.|
|CURRENT ROW	| 윈도우 시작 위치가 현재 행임을 의미한다.|

- WINDOWING은 아래와 같이 사용한다.

![WINDOWING](https://github.com/leekoby/leekoby.github.io/assets/118284808/3d3d2a60-e615-4802-a2b4-e1a58c072435){: width="500" height="500" }

- UNBOUNDED PRECEDING은 처음 행을 의미하며, UNBOUNDED FOLLOWING은 마지막 행을 의미한다. 

- 그러므로 TOTSAL에 처음부터 마지막까지의 합계 (SUM(SAL))를 계산한 것이다.

![WINDOWING](https://github.com/leekoby/leekoby.github.io/assets/118284808/043429ae-3882-4895-8882-910da5305e48){: width="500" height="500" }

- 위의 SQL문은 처음부터 CURRENT ROW까지의 합계를 계산한다. 

- 결과적으로 누적합계가 된다.

- 즉, 첫 번째 행의 SAL은 800이고 두 번째 행의 SAL은 950이다. 

- 그러므로 두 번째 행의 TOTSAL은 800+950=1750이 된다.

- 세 번째 행은 다시 1100+1750=2850이 된다.

- **<span style="color:#ff6600">CURRENT ROW는 데이터가 인출된 현재 행을 의미</span>**한다.

![WINDOWING](https://github.com/leekoby/leekoby.github.io/assets/118284808/b61e8f84-33c9-47f6-842f-0f983ba38182){: width="500" height="500" }

- 위의 SQL문은 현재 행(CURRENT ROW)부터 마지막 행(UNBOUNDED FOLLOWING)까지의 합계를 계산한다.

- 첫 번째 행의 SAL이 800이므로 800부터 끝까지의 합계를 TOTSAL에 계산한다. 

- 결과적으로 전체합계가된다.

- 그다음은 950부터 마지막까지이므로 800이 제외된 합계가 된다. 

- 따라서 28225가 된다.

<br/>

## **💻 순위 함수**

- 윈도우 함수는 특정 항목과 파티 션에 대해서 순위를 계산할 수 있는 함수를 제공한다.

- **<span style="color:#ff6600">순위 함수는 RANK, DENSE_RANK, ROW_NUMBER 함수가 있다.</span>**

<br/>

**순위 관련 윈도우 함수**

|순위 함수	| 설명|
|RANK|	- 특정 항목 및 파티션에 대해서 순위를 계산한다.<br/> - 동일한 순위는 동일한 값이 부여된다.|
|DENSE_RANK| -	동일한 순위를 하나의 건수로 계산한다.|
|ROW_NUMBER	| - 동일한 순위에 대해서 고유의 순위를 부여한다.|

![순위 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/ddff8cc3-c15e-4e6f-8fc1-a8f88f650e7b){: width="500" height="500" }

- **<span style="color:#ff6600">RANK 함수는 순위를 계산하며, 동일한 순위에는 같은 순위가 부여된다.</span>**

- RANK( ) OVER (ORDER BY SAL DESC)는 SAL로 등수를 계산하고 내림차순으로 조회하게 한다.

- RANK( ) OVER (PARTITION BY JOB ORDER BY SAL DESC)는 JOB으로 파티션을 만들고 JOB별 순위를 조회하게 한다.

![DENSE RANK](https://github.com/leekoby/leekoby.github.io/assets/118284808/d52ead45-f117-4eed-89e7-7aea03942378){: width="500" height="500" }

- **<span style="color:#ff6600">DENSE RANK는 동일한 순위를 하나의 건수로 인식해서 조회</span>**한다.

![ROW_NUMBER](https://github.com/leekoby/leekoby.github.io/assets/118284808/8b576878-9485-416d-b061-3a767dd45f65){: width="500" height="500" }

- **<span style="color:#ff6600">ROW_NUMBER 함수는 동일한 순위에 대해서 고유의 순위를 부여</span>**한다.

<br/>

## **💻 집계 함수**

윈도우 함수를 제공한다.

<br/>

**집계 관련 윈도우 함수**

|집계 함수	|  설명|
|:-|:-|
|SUM	| 파티션 별로 합계를 계산한다.|
|AVG	|파티션 별로 평군을 계산한다.|
|COUNT|	파티션 별로 행 수를 계산한다.|
|MAX와 MIN	|파티션 별로 최댓값과 최솟값을 계산한다.|

![집계 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/3577a989-e863-4218-af56-489dc22af17c){: width="500" height="500" }

- 집계 함수 SUM()와 OVER절에서 MGR파티션을 만들고 같은 관리자를 가지고 있는 합계를 계산하고 있다.

<br/>

## **💻 행 순서 관련 함수**

행 순서 관련 함수는 상위 행의 값을 하위에 출력하거나 하위 행의 값을 상위 행에 출력할 수 있다.

특정 위치의 행을 출력할 수 있다.

<br/>

**행 순서 관련 윈도우 함수**

|행 순서	| 설명|
|FIRST_VALUE	| - 파티션에서 **<span style="color:#ff6600">가장 처음에 나오는 값</span>**을 구한다.<br/> - **<span style="color:#ff6600">MIN 함수</span>**를 사용해서 같은 결과를 구할 수 있다.|
|LAST_VALUE	 | - 파티션에서 **<span style="color:#ff6600">가장 나중에 나오는 값</span>**을 구한다. <br/> - **<span style="color:#ff6600">MAX 함수</span>**를 사용해서 같은 결과를 구할 수 있다.|
|LAG	|**<span style="color:#ff6600">이전 행</span>**을 가지고 온다.|
|LEAD|	- 윈도우에서 **<span style="color:#ff6600">특정 위치의 행을 가지고 온다. </span>**<br/> - 기본값은 1이다.|

![FIRST_VALUE](https://github.com/leekoby/leekoby.github.io/assets/118284808/9e845207-dbe0-4019-94d5-cbd32d89bd52){: width="500" height="500" }

- **<span style="color:#ff6600">FIRST_VALUE 함수는 파티션에서 조회 된 행 중에서 첫 번째 행의 값을 가지고 온다.</span>**

- 위의 예에서 TEST7과 TEST14가 조회되었다. 

- 그중에서 TEST7이 첫 번째 행이므로 첫 번째 행의 TEST7을 가지고 온다.

- 단, SAL 내림차순으로 조회했기 때문에 의미상으로는 부서 내에 가장 급여가 많은 사원이
된다.

<br/>

![LAST_VALUE](https://github.com/leekoby/leekoby.github.io/assets/118284808/2c494459-0262-4260-9435-d1167493cace){: width="500" height="500" }

- **<span style="color:#ff6600">LAST_VALUE 함수는 파티션에서 마지막 행을 가지고 온다. </span>**

- 그래서 FIRST_VALUE와 다르게 TEST14가 출력된다.

- **<span style="color:#ff6600">“BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING”의 의미는 현재 행에서 마지막 행까지의 파티션을 의미한다.</span>**

<br/>

![LAG](https://github.com/leekoby/leekoby.github.io/assets/118284808/f0e2e190-6af3-408c-a04d-b63d4c100dbe)

- LAG 함수는 이전 값을 가지고 오는 것이다. 

- 예를 들어 PRE_SAL의 5000값은 SAL의 이전 데이터이다.

<br/>

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5d5eacda-80d9-42b3-baca-f80398e8b963)

- LEAD 함수는 지정된 행의 값을 가지고 오는 것이다. 

- 위의 예는 SAL에서 2번째 행의 값을 가지고 온다.

- LEAD의 기본값은 1며, 첫 번째 행의 값을 가지고 오는 것이다.

<br/>

## **💻 비율 관련 함수**

비율 관련 함수는 누적 백분율, 순서별 백분율, 파티션을 N분으로 분할한 결과 등을 조회할 수 있다.

|비율 함수|	설명|
|:- |:- |
|CUME_DIST|	- 파티션 전체 건수에서 현재 행보다 작거나 같은 건수에 대한 **<span style="color:#ff6600">누적 백분율을 조회</span>**한다.<br/> - 누적 분포상의 위치를 0~1사이의 값을 가진다. |
|PERCENT_RANK	|파티션에서 제일 먼저 나온 것을 0으로 제일 늦게 나온 것을 1로 하여 <br/> 값이 아닌 행의 순서별 백분율을 조회한다.|
|NTILE	|파티션별로 전체 건수를 ARGUMENT 값으로 N 등분한 결과를 조회한다.|
|RATIO_TO_REPORT	|파티션 내에 전체 SUM에 대한 행 별 칼럼값의 백분율을 소수점가지 조회한다.|

![PERCENT_RANK](https://github.com/leekoby/leekoby.github.io/assets/118284808/048731b8-ba03-4751-9698-7977a86442bc){: width="500" height="500" }

- PERCENT_RANK 함수는 파티션에서 등수의 퍼센트를 구하는 것이다.

<br/>

![NTILE](https://github.com/leekoby/leekoby.github.io/assets/118284808/b7cfb5aa-5659-4036-b0dd-d4e243a2794c){: width="500" height="500" }

- NTILE(4)는 4등분으로 분할하라는 의미로 위의 예에서는 급여가 높은 순으로 1〜4등분으로
분할한다.

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

