---
layout: post
title: 내장형 함수(BUILT-IN FUNCTION)
author: admin
date: 2023-08-28 02:00:00 +900
lastmod: 2023-08-28 02:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [DATABASE(DB), SQLD]  # 대문자로 작성
tags: [sql, sqld, built in function] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# **🌈 내장형 함수(BUILT-IN FUNCTION)**

## **💻 내장형 함수**

모든 데이터베이스는 SQL에서 사용할 수 있는 내장형 함수를 가지고 있다

내장형 함수는 데이터베이스 관리 시스템 벤더별로 약간의 차이가 있지만, 거의 비슷한 방법으로 사용이 가능하다.

**<span style="color:#ff6600">내장형 함수로는 앞서 배운 형변환 함수, 문자열 및 숫자형 함수, 날짜형 함수</span>**가 있다.

## **💻 DUAL 테이블**

**<span style="color:#ff6600">DUAL 테이블은 Oracle 데이터베이스에 의해서 자동으로 생성되는 테이블</span>**이다.

Oracle 데이터베이스 사용자가 임시로 사용할 수 있는 테이블로 내장형 함수를 실행할 때도 사용할 수 있다

Oracle 데이터베이스의 모든 사용자가 사용할 수 있다.

![DUAL 테이블](https://github.com/leekoby/leekoby.github.io/assets/118284808/4ab9d301-5df6-4a73-8aea-54009d2abcdb){: width="500" height="500" }

## **💻 내장형 함수의 종류**

DUAL 테이블에 문자형 내장형 함수를 사용하면 다음과 같다.

ASCII 함수는 문자에 대한 ASCII 코드 값을 알려준다. ASCII 코드는 대문자 A를 기준으로 A(65), B(66), C(67)등의 값이다.

SUBSTR 함수는 지정된 위치의 문자열을 자르는 함수이고 LENGTH 함수, LEN 함수는 문
자열의 길이를 계산한다.

LTRIM 함수를 사용하면 문자열의 왼쪽 공백을 제거할 수 있다. 또한 함수를 중첩해서 사용해도 된다.( LENGTH(LTRIM(’ABC’)))

![내장형 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/e4ad29c1-ae20-4dc7-b524-69d1178b347d){: width="500" height="500" }

- Oracle 데이터베이스에서 제공하는 문자열 함수는 다음과 같다.

### **문자열 함수**

**문자열 함수	설명**

|||
|:-|:-|
|ASCII (문자)| -문자 혹은 숫자를 ASCII 코드값으로 변환한다.|
|CHAR（ASCII 코드값）|- ASCII 코드값을 문자로 변환한다.|
|SUBSTR（문자열m,n）|문자열에서 m번째 위치부터 n개를 자른다.|
|CONCAT（문자열1, 문자열2）| - 문자열1번과 문자열2번을 결합한다.<br/> - Oracle은 `ll`： MS-SQL은 `+`를 사용할수 있다.|
|LOWER（문자열）|- 영문자를 소문자로 변환한다.|
|UPPER（문자열）|- 영문자를 대문자로 변환한다.|
|LENGTH 혹은 LEN（문자열）|- 공백을 포함해서 문자열의 길이를 알려준다.|
|LTRIM（문자열 지정문자）|	- 왼쪽에서 지정된 문자를 삭제한다. <br/> - 지정된 문자를 생략하면 공백을 삭제한다.|
|RTRM（문자열 지정문자）|- 오른쪽에서 지정된 문자를 삭제한다.<br/> - 지정된 문자를 생략하면 공백을 삭제한다.|
|TRIM（문자열 지정된 문자）|	- 왼쪽 및 오른쪽에서 지정된 문자를 삭제한다. <br/> - 지정된 문자를 생략하면 공백을 삭제한다.|

<br/>

### **날짜형 함수**

날짜형 함수 중에서 오늘 날짜를 구하기 위해서는 **<span style="color:#ff6600">SYSDATE</span>**를 사용하면 된다.

만약, 해당 연도만 알고 싶다면, **<span style="color:#ff6600">EXTRACT</span>** 함수를 사용한다.

**<span style="color:#ff6600">TCLCHAR 함수는 형변환 함수 중에서 가장 많이 사용하는 것으로 숫자나 날짜를 원하는 포맷의 문자열로 변환</span>**한다.

![날짜형 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/b6cd363e-3d24-49d9-9ad0-1a9ea2a0383c){: width="500" height="500" }

- Oracle 데이터베이스 날짜형 함수는 다음과 같다.

|날짜형 함수|설명|
|:-|:-|
|SYSDATE	|오늘의 날짜를 날짜 타입으로 알려준다.|
|EXTRACT(’YEAR’ \| ‘MONTH’ \| ‘DAY’ from dual)|날짜에서 년, 월, 일을 조회한다.|

### **숫자형 함수**

절댓값을 계산하는 **`ABS`** 함수와 음수, 0, 양수를 구분하는 **`SIGN`**, 나머지를 계산하는 **`MOD`** 등의 함수를 사용하면 다음과 같다.

![수 계산 함수](https://github.com/leekoby/leekoby.github.io/assets/118284808/90afae20-d077-4102-8acc-3bd2aea199e7){: width="500" height="500" }

- Oracle 데이터베이스가 지원하는 숫자형 함수는 다음과 같다.

|숫자형 함수|설명|
|ABS（숫자）|절댓값을 돌려준다.|
|SIGN（숫자）|양수, 음수, 0을구별한다.|
|MOD（숫자1, 숫자2）|- 숫자1을 숫자2로 나누어 나머지를 계산한다.<br/> - %를 사용해도 된다.|
|CEIL/CEILING（숫자）|숫자보다 **`크거나 같은 최소의 정수`**를 돌려준다.|
|FLOOR（숫자）|숫자보다 **`작거나 같은 최대의 정수`**를 돌려준다.|
|ROUND（숫자, m）|- **`소수점 m 자리에서 반올림`**한다. <br/> - m의 기본값은 0이다.|
|TRUNC（숫자, m）|	- **`소수점 m 자리에서 절삭`**한다. <br/> - m의 기본값은 0이다.|

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