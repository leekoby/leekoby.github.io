---
layout: post
title: DML(Data Manipulation Language)
author: admin
date: 2023-08-27 14:00:00 +900
lastmod: 2023-08-27 14:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, data manipulation language, dml] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br />

# **⚡ DML(Data Manipulation Language)**

**SQL 문장들의 종류**

| 종류 | 명령어 | 설명 |
|:-:|:-|:-|
| 데이터 조작어 <br/> DML <br/> (Data Manipulation Language) | SELECT |데이터베이스에 들어 있는 데이터를 조회하거나 <br/> 검색하기 위한 명령어를 말하는 것으로 RETRIEVE 라고도 한다. |
|  | INSERT <br/> UPDATE <br/> DELETE |데이터베이스의 테이블에 들어 있는 데이터에 변형을 하는 종류의 명령어들 <br/> 데이터를 테이블에 새로운 행을 집어넣거나 <br/> 원하지 않는 데이터를 수정/삭제하는 것들의 명령어들을 DML이라고 한다.|
|데이터 정의어 <br/> DDL <br/> (Data Definition Language)|CREATE <br/> ALTER <br/> DROP <br/> RENAME| 테이블과 같은 데이터 구조를 정의하는데 사용되는 명령어들 <br/> 구조를 생성하거나 변경하거나 삭제하거나 <br/> 이름을 바꾸는 데이터 구조와 관련된 명령어들을 DDL 이라고 부른다.|
|데이터 제어어 <br/> DCL <br/> (Data Control Language)| GRANT <br/> REVOKE| 데이터베이스에 접근하고 객체들을 사용하도록 권한을 주고 <br/> 회수하는 명령어를 DCL이라고 한다.|
|트랜잭션 제어어 <br/> TCL <br/> (Transaction Control Language) | COMMIT <br/> ROLLBACK | 논리적인 작업의 단위를 묶어서 DML에 의해 조작된 결과를<br/> 작업단위(트랜잭션) 별로 제어하는 명령어|

## **💻 SELECT 문**

### **SELECT문**

테이블에 입력된 데이터를 조회하기 위해서 SELECT문을 사용한다.

SELECT문은 특정 컬럼이나 특정 행만을 조회할 수 있다.

```sql
SELECT 컬럼1, 컬럼2, ... FROM 테이블 WHERE 컬럼1 = '이꼬비';
```

```sql
SELECT * FROM 테이블;
```

컬럼을 따로 명시하지 않고 * (Asterisk)를 쓰면 전체 컬럼이 조회되며 조회되는 컬럼의 순서는 테이블의 컬럼 순서와 동일하다.

별도의 WHERE절이 없으면 테이블의 전체 ROW가 조회된다.

![SELECT문 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/965cc971-cb3b-42bd-a8f0-bddc3b23b30a)

- 위의 SELECT문에서 EMP 테이블의 모든 컬럼(`*`)을 출력한다.

- 단, `WHERE절에 있는 조건문에 있는 행만 조회`한다.

<br/>


> **<span style="color:#3366ff">Alias</span>**
> 
> **<span style="color:#ff6600">Alias(별칭)는 테이블명이나 컬럼명이 너무 길어서 간략하게 할 때 사용</span>**한다.
>
> 여러 개의 테이블을 JOIN하거나 서브쿼리가 있을 때 컬럼명 앞에 테이블명을 같이 명시해야하는 경우 테이블명은 비교적 길기 때문에 짧게 줄여쓰기 위해 사용
>
> ALIAS를 별도로 지정해주지 않으면 컬럼명이 `대문자`로 출력된다.
>
> ![Alias](https://github.com/leekoby/leekoby.github.io/assets/118284808/31f9fe1d-b5f6-4137-bc2e-9bfe891f8029)
{: .prompt-info }

<br/>

#### **SELECT 문법**

|SELECT문 문법|설명|
|-|-|
|SELECT \*| - 모든 컬럼을 출력한다.<br/> - `‘*’`는 모든 컬럼을 의미한다.|
|FROM EMP|- FROM절에는 테이블명을 쓴다.<br/> - 즉, EMP 테이블을 지정했다.|
|WHERE 사원번호 = 1000|- EMP 테이블에서 사원번호가 1000번인 행을 조회한다.<br/> - 즉, 조건문을 지정한다.|


#### **SELECT 컬럼 지정**

|사용 예제|설명|
|-|-|
|SELECT EMPNO, ENAME FROM EMP;|EMP 테이블의 모든 행에서 EMPNO와 ENAME컬럼만 출력한다. |
|SELECT * FROM EMP; |EMP 테이블의 모든 컬럼과 모든 행을 조회한다.|
|SELECT ENAME \|\| ‘님’ FROM EMP;|- EMP 테이블의 모든 행에서 ENAME 컬럼을 조회한다. <br/> - 단, ENAME 컬럼 뒤에 ‘님’이라는 문자를 결합한다.<br/> - 예를 들어 임베스트 님이라고 출력된다.|

<br/>

> **<span style="color:#ff6600">SELECT 쿼리문 순행순서</span>**
>
> 1. **FROM**: 해당 테이블에서 데이터를 가져온다.
> 
> 2. **WHERE**: 가져온 데이터 중에서 특정 조건을 만족하는 행을 필터링한다.
> 
> 3. **GROUP BY**: 필터링된 결과를 특정 컬럼의 값에 따라 그룹화한다.
> 
> 4. **HAVING**: 그룹화된 결과 중에서 특정 조건을 만족하는 그룹을 필터링한다.
> 
> 5. **SELECT**: 최종적으로 출력할 컬럼들을 선택하고, 집계 함수(Aggregate Function)들이 적용된다.
> 
> 6. **ORDER BY**: 선택된 결과를 특정 컬럼의 값에 따라 정렬한다.
> 
> ![SELECT 쿼리문 순행순서image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8847a7e0-2918-480a-8f74-f1213ecdfee2){: width="500" height="500" }<BR/>
{:.prompt-info}

<br/>

### **Distinct**

**<span style="color:#ff6600">Distinct문은 컬럼명 앞에 지정하여 중복된 데이터를 한 번만 조회</span>**하게 한다.

![Distinct 미사용](https://github.com/leekoby/leekoby.github.io/assets/118284808/921932ab-f0de-459e-88e4-a959754dcf57)

- 위의 예는 EMP 테이블의 DEPTNO 컬럼을 조회한 것이다. 

- 조회 내용을 보면 DEPTNO가 중복으로 저장되어 있는 것을 확인할 수 있다.

![Distinct 사용](https://github.com/leekoby/leekoby.github.io/assets/118284808/748b0a24-3425-400b-89d4-c53eea869871)

- Distinct를 사용하면 DEPTNO 값이 중복되지 않는다.

<br/>

### **ORDER BY 문**

SELECT문을 사용할 때 ORDER BY를 같이 사용할 수 있다.

- 단, **<span style="color:#ff6600">ORDER BY 구문은맨 마지막에 실행</span>**된다. 

**<span style="color:#ff6600">ORDER BY는 데이터를 오름차순(Ascending) 혹은 내림차순(Descending)으로 출력</span>**한다.

ORDER BY절을 따로 명시하지 않으면 데이터는 임의의 순서대로 출력된다.

ORDER BY 절에는 컬럼명을 명시해줄 수도 있고 SELECT 절에 기술된 컬럼 순서를 숫자로 명시해줄 수도 있다.

> **<span style="color:#3366ff">ASC (ASCENDING)</span> : <span style="color:#ff6600">오름차순</span>**
> 
> **<span style="color:#3366ff">DESC (DESCENDING)</span> : <span style="color:#ff6600">내림차순</span>**
> 
> **<span style="color:#3366ff">옵션 생략 시</span> <span style="color:#ff6600">ASC가 기본값이 된다.</span>**
{: .prompt-info }

ORDER BY가 정렬을 하는 시점은 모든 실행이 끝난 후에 데이터를 출력해 주기 바로 전이다.

ORDER BY 절은 논리적으로 SELECT 절 다음에 수행되기 때문에 SELECT 절에서 정의한 ALIAS를 사용할 수 있다.

ODER BY 절의 옵션은 각 컬럼 다음에 하나씩 붙여서 작성

ORDER BY는 정렬을 하기 때문에 데이터베이스 메모리를 많이 사용하게 된다. 

- 즉, 대량의 데이터를 정렬하게 되면 정렬로 인한 성능 저하가 발생된다.

Oracle 데이터베이스는 정렬을 위해서 메모리 내부에 할당된 SORT_AREA_SIZE를 사용한다. 

만약 SORT_AREA_SIZE가 너무 작으면 성능 저하가 발생한다.

정렬을 회피하기 위해서 인덱스를 생성할 때 사용자가 원하는 형태로 오름차순 혹은 내림차순으로 생성해야 한다.

**<span style="color:#ff6600">특별한 지정이 없으면 ORDER BY는 오름차순으로 정렬</span>**한다.

![ORDER BY image](https://github.com/leekoby/leekoby.github.io/assets/118284808/702dbf7f-fc64-4edb-8397-36ff49c1d8c4)

- ENAME 부분은 ENAME ASC와 같다. 기본적으로 오름차순과 내림차순을 지정하지 않으면 오름차순으로 정렬한다.

내림차순으로 정렬하고 싶을 때는 **`DESC`**를 사용한다.

ORDER BY에서 1, 2, 3, ... 숫자가 나오면 추출할 컬럼명을 대신하여 사용할 수 있다. 

오라클에서 정렬시 NULL값은 마지막에 정렬된다.

NULL값을 처음에 정렬되게 하려면 ORDER BY 컬럼 `NULLS FIRST`

`NULLS LAST`를 쓰면  NULL을 마지막에 정렬시킨다. 안쓴 경우와 동일한 결과가 나온다.

> **<span style="color:#3366ff">정렬의 기준이 되는 컬럼에</span> <span style="color:#ff6600">NULL 데이터가 포함되어 있을 경우 </span>**
>  
> 데이터베이스 종류에 따라 정렬의 위치가 달라지는데 Oracle의 경우에는 NULL을 최대값으로 취급하기 때문에 오름차순으로 했을 경우 맨 마지막에 위치하게 된다(SQL Server는 반대). 
> 
> 만약 순서를 변경하고 싶다면 ORDER BY 절에 `NULLS FIRST`, `NULLS LAST` 옵션을 써서 NULL의 정렬산 순서를 변경할 수 있다.
{: .prompt-tip }

### **Index를 사용한 정렬 회피**

정렬은 Oracle 데이터베이스에 부하를 주므로, **<span style="color:#ff6600">인덱스를 사용해서 `ORDER BY`를 회피할 수 있다.</span>**

#### **정렬 테스트 데이터 입력**

![정렬 테스트 데이터 입력](https://github.com/leekoby/leekoby.github.io/assets/118284808/a2c37d23-c010-4e42-a625-0806aaaac502)

- 위와 같이 데이터를 입력하고 SELECT문을 실행하면 EMPNO로 오름차순 정렬되어서 조회된다.

- 그 이유는 EMPNO가 기본키이기 때문에 자동으로 오름차순 인덱스가 생성된다

![Index를 사용한 정렬 회피](https://github.com/leekoby/leekoby.github.io/assets/118284808/54949636-7e28-4e7a-b0ba-6109e018524f)

- 위의 예를 보면 **`/*+ INDEX_DESC（A）*/`**를 사용했다. 

- 즉, 힌트를 사용한 것이다. 

- 그래서 EMP 테이블에 생성된 인덱스를 내림차순으로 읽게 지정한 것이다.

- 따라서 SELECT문에 **`ORDER BY EMPNO DESC`**를 사용하지 않았다.

![Index를 사용한 정렬 회피](https://github.com/leekoby/leekoby.github.io/assets/118284808/eda03671-21d6-48b3-a38f-3e2694d531ee)

- 위의 예처럼 SQL문을 사용하면 EMPNO 인덱스를 내림차순으로 읽는다.

- 인덱스를 스캔한 후에 해당 EMPNO의 값을 가지고 테이블의 데이터를 읽는다.

- 테이블에서 해당 행을 찾으면 인출하여 사용자 화면에 조회된다.

<br/>


## **💻 INSERT 문**

### **INSERT 문**

INSERT문은 테이블에 데이터를 입력하는 DML문이다.

![INSERT 문 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6a993272-6072-4103-bed8-6e7aff614e99)

EMP 테이블에 데이터를 삽입하려면 테이블명, 컬럼명, 데이터 순으로 입력하면 된다.

![INSERT 문 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5ecada15-3fb3-4454-91de-5e0af45bdf7f)

데이터를 입력할 때 문자열을 입력하는 경우에는 **`작은따옴표(’  ’)`**를 사용해야 한다.

만약 특정 테이블의 모든 컬럼에 대한 데이터를 삽입하는 경우에는 컬럼명을 생략할 수 있다.

모든 컬럼에 데이터를 입력한다.

![INSERT 문 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b09195c3-0659-4d76-ae70-6c5fecb2a4e3)

- 위의 예처럼 컬럼명을 생략할 수 있다. 

- 단, 위의 예제에서 EMP 테이블의 컬럼은 숫자형 데이터 타입 한 개의 컬럼과 문자형 데이터 타입 한 개의 컬럼만 있어야 한다.

- 주의 사항은 INSERT문을 실행했다고 데이터 파일에 저장되는 것은 아니다. 

- **<span style="color:#ff6600">최종적으로 데이터를 저장하려면 TCL문인 Commit을 실행</span>**해야 한다.

- 만약 Auto Commit으로 설정된 경우에는 Commit을 실행시키지 않아도 바로 저장된다.

>**<span style="color:#ff6600">AUTOCOMMIT을 주의해서 사용해야 하는 이유</span>**
> 1. **AUTOCOMMIT을 활성화 해놓은 후 DB 사용하면 실수를 하더라도 곧바로 즉시 반영이 된다.**
> 
> 2. **Commit이 된 트랜잭션은 Rollback이 불가능 하다.**<br/>
{:.prompt-danger}

<br/>

### **SELECT 문**

SELECT문을 사용하여 데이터를 조회해서 해당 테이블에 바로 삽입할 수 있다.

단, 입력되는 테이블은 사전에 생성되어 있어야 한다.

![SELECT문으로 데이터삽입 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/56e0ef83-8c4a-4610-9ff1-21563b6fad78)


<br/>

### **Nologging 문**

데이터베이스에 데이터를 입력하려면 로그파일에 그 정보를 기록한다.

Check point라는 이벤트가 발생하면 로그파일의 데이터를 데이터 파일에 저장한다.

**<span style="color:#ff6600">Nologging 옵션은 로그파일의 기록을 최소화시켜서 입력 시 성능을 향상시키는 방법</span>**이다.

Nologging 옵션은 Buffer Cache라는 메모리 영역을 생략하고 기록한다.

![Nologging 문 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/13742033-b6f3-4b01-92fa-da816617d51a)

<br/>

## **💻 UPDATE 문**

입력된 데이터의 값을 수정하려면, UPDATE문을 사용한다.

UPDATE문을 사용하여 원하는 조건으로 데이터를 검색해서 해당 데이터를 수정할 수 있다.

만약, **<span style="color:#ff6600">UPDATE문에 조건문을 입력하지 않으면 `모든` 데이터가 수정</span>**되므로 유의해야 한다.

![UPDATE 문 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/473945bb-2e51-45ad-978c-017516a97720)

UPDATE문에서 주의사항은 데이터를 수정할 때 조건절에서 검색되는 행 수만큼 수정된다는 것이다.

- 위의 예에서 EMPNO가 100번인 직원이 두 명이라면 두 명의 ENAME은 모두 ‘조조’로 수정된다.

<br/>

## **💻 DELETE 문**

DELETE문은 원하는 조건을 검색해서 해당되는 행을 삭제한다.

**<span style="color:#ff6600">DELETE문에 조건문을 입력하지 않으면 모든 데이터가 삭제</span>**된다. 

- 즉, 테이블에 있는 모든 데이터가 삭제되는 것이다.

DELETE문으로 데이터를 삭제한다고 해서 테이블의 용량이 초기화되지는 않는다.

![DELETE 문image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a49339d7-44b6-4970-9907-f64e37e1f646)

- 만약 위의 예에서 WHERE절(조건)을 입력하지 않으면 EMP 테이블의 모든 데이터가 삭제된다.

>**<span style="color:#ff6600">테이블 용량이 초기화 되지 않는다는 의미</span>**
>
>- Oracle 데이터베이스는 저장공간을 할당할 때 Extent 단위로 할당한다
>
>- 테이블에 데이터가 입력되면 Extentoil 저장하게 된다
>
>- 만약 Extent의 크기가 MAX_EXTENTS를 넘어서게 되면 용량 초과 오류가 발생하게 된다 즉 최대로 저장할 수 있는 공간의 의미를 가지고 있다
>
>- DELETE 문으로 데이터를 삭제하면 용량이 감소할 것으로 생각하는데 DELETE문은 삭제 여부만 표시하고 용량은 초기화되지 않는다.<br/>
{:.prompt-info}

**테이블의 모든 데이터 삭제 DELETE/TRUNCATE**

|||
|:-|:-|
|DELETE FROM TABLE|- 테이블의 모든 데이터를 삭제한다. <br/>- 데이터가 삭제되어도 테이블의 용량은 감소하지 않는다. |
|TRUNCATE TABLE TABLE|- 테이블의 모든 데이터를 삭제한다.<br/> - 데이터가 삭제되면 테이블의 용량은 초기화 된다.|



## **💻 WHERE 문**

INSERT를 제외한 DML문을 수행할 때 원하는 데이터만 골라 수행할 수 있도록 해주는 구문

WHERE 절은 FROM 절 다음에 위치하며, 조건식은 아래 내용으로 구성된다.

- 컬럼(COLUMN)명 (보통 조건식의 좌측에 위치)
- 비교 연산자
- 문자, 숫자, 표현식 (보통 조건식의 우측에 위치)
- 비교 칼럼명 (JOIN 사용 시)


### **WHERE문이 사용하는 연산**

WHERE문이 사용할 수 있는 연산자는 비교 연산자, 부정 비교 연산자, 논리 연산자, SQL 연산자, 부정 SQL 연산자가 있다.

#### **비교 연산자**

|비교 연산자 | 설명|
| :-: | :-: |
|= |같은 것을 조회 |
|<|작은 것을 조회 |
|<= | 작거나 같은 것을 조회|
|> | 큰 것을 조회|
|>= | 크거나 같은 것을 조회|

#### **부정 비교 연산자**

|부정 비교 연산자 | 설명|
| :-: | :-: |
|!=| 같지 않은 것을 조회한다. |
|^= | 같지 않은 것을 조회한다.|
| <>| 같지 않은 것을 조회한다.|
| NOT 컬럼명 = |같지 않은 것을 조회한다. |
| NOT 컬럼명 >|크지 않은 것을 조회한다. |

#### **논리 연산자**

> 논리 연산자는 SQL에 명시된 순서와는 관계없이 `()` => `NOT` => `비교연산자(>,>=,<,<=)와 SQL 비교연산자(BETWEEN, IN, LIKE, IS NULL)`=> `AND` => `OR` 순

|논리 연산자 | 설명|
| :-: | :-: |
| AND|조건을 모두 만족해야 참이 된다. |
|OR |조건 중 하나라도 만족해도 참이 된다. |
|NOT |참이면 거짓으로 바꾸고 거짓이면 참으로 바꾼다. |

![연산자 예](https://github.com/leekoby/leekoby.github.io/assets/118284808/031af944-0a46-4376-8591-60ecaff5e564)

- 위의 예는 EMP 테이블에서 EMPNO가 1001이고 SAL이 1000보다 크거나 같은 것을 조회한다.


#### **SQL 연산자**

|SQL 연산자 | 설명|
| :- | :-|
|LIKE ‘%비교 문자열%’ |- `비교 문자열을 조회`한다. ‘%’는 모든 값을 의미한다. |
| BETWEEN A AND B| - `A와 B 사이의 값`을 조회한다. (`A,B 포함`) <br/> - 연속적인 데이터 많이 사용|
|IN (LIST) | - OR를 의미하며 list 값 중에 `하나만 일치`해도 조회된다. <br/> - 이산적데이터에 사용|
|IS NULL |- `NULL 값`을 조회한다. <br/> - IS NULL(컬럼, X)의 형태로 결과 값이 NULL일 경우 X를 반환한다.|

#### **부정 SQL 연산자**

| 부정 SQL 연산자| 설명|
| :- | :- |
|NOT BETWEEN A AND B | - A와 B 사이의 해당되지 않는 값을 조회한다. (`A,B 미포함`)|
| NOT IN (LIST)|- LIST와 `불일치한 것을 조회`한다. |
|IS NOT NULL |- `NULL 값이 아닌 것을 조회`한다.|

<br/>

### **LIKE 문**

**<span style="color:#ff6600">LIKE문은 와일드 카드를 사용해서 데이터를 조회</span>** 할 수 있다.

#### **와일드 카드**

|와일드 카드	|설명|
|:-|:-|
|%| - 어떤 문자를 포함한 모든 것을 조회한다.<br/> - 예를 들어 ‘조%’는 ‘조’로 시작하는 모든 문자를 조회한다.|
|_(underscore / underbar)|- 한 개인 단일 문자를 의미한다.|

> ESCAPE
>
> LIKE 구문하고 같이 사용하는데 `%`나 `_`와 같이 사용된다.
>
> 예를 들면, `'%\_%' ESCAPE '\' `으로 설정을 하면 `\`뒤의 `_`을 포함하는 결과를 찾는다. 

![뒷 부분 와일드 카드 ](https://github.com/leekoby/leekoby.github.io/assets/118284808/24a39ef2-11ae-4bd3-a56e-7e50dabee86e)
- 뒷 부분 와일드 카드 

![앞 부분 와일드 카드](https://github.com/leekoby/leekoby.github.io/assets/118284808/608c240c-a396-4c5d-960f-d5200fe1b4df)
- 앞 부분 와일드 카드

![앞뒤 부분 와일드 카드](https://github.com/leekoby/leekoby.github.io/assets/118284808/6378a320-9e50-4b07-bea2-d4222604cd1b)
- 앞뒤 부분 와일드 카드

![와일드 카드 미사용](https://github.com/leekoby/leekoby.github.io/assets/118284808/0f93f8b6-637e-436c-9a10-b05a728f7803)
- 와일드 카드 미사용

![underscore](https://github.com/leekoby/leekoby.github.io/assets/118284808/4155ced9-5e12-41d3-bf8c-d8b688c8294a)
- _(underscore) 사용 한문자 조회

<br/>

### **BETWEEN 문**

**<span style="color:#ff6600">Between문은 지정된 범위에 있는 값을 조회</span>**한다.

**`Between 1000 and 2000`**은 1000과 2000을 포함하고 1000과 2000 사이의 값을 조회한다.

![BETWEEN 문](https://github.com/leekoby/leekoby.github.io/assets/118284808/579a2ce4-8a16-4ed7-bf90-574438c3a3eb)
- 위의 예는 급여(SAL)가 1000 이상 2000 이하인 직원을 조회한다.

![NOT BETWEEN 문](https://github.com/leekoby/leekoby.github.io/assets/118284808/39759cca-b852-4e71-9b84-d0a408695678)
- 위의 예는 급여(SAL)가 1000 미만 2000 초과인 값을 조회한다.

<br/>

### **IN 문**

**<span style="color:#ff6600">N문은 “OR”의 의미를 가지고 있어서 하나의 조건만 만족해도 조회</span>**I가 된다.

- 예를 들어 JOB이 `CLERK`이거나 `MANAGER`인 것을 조회할 때 **`JOB IN (’CLERK’,'MANAGER')`**를 사용한다.

![IN 문](https://github.com/leekoby/leekoby.github.io/assets/118284808/3ef963a3-d54b-49d5-834d-bcf8c7512578)

![IN 문](https://github.com/leekoby/leekoby.github.io/assets/118284808/b25ef03e-b686-45ee-ba6c-2f203203a964)

- 괄호를 사용하여 원하는 데이터를 컬럼명에 대응되도록 입력함으로써, IN문으로 여러 개의 컬럼에 대한 조건을 지정할 수 있다.

<br/>

### **NULL 값 조회**

#### **NULL의 특징**

- NULL은 모르는 값을 의미한다.

- NULL은 값의 부재를 의미한다.

- NULL과 숫자 혹은 날짜를 더하면 NULL이 된다.

- NULL과 어떤 값을 비교할 때, '알 수 없음이 반환된다.


#### **NULL 값 조회**

- **<span style="color:#ff6600">NULL을 조회할 경우는 IS NULL을 사용하고 NULL 값이 아닌 것을 조회할 경우는 IS NOT NULL을 사용</span>**한다.

![NULL 값 조회](https://github.com/leekoby/leekoby.github.io/assets/118284808/604f4499-5474-4207-90a6-c0593e397888)

![NOT NULL 값 조회](https://github.com/leekoby/leekoby.github.io/assets/118284808/7c42e200-2046-4e7f-8263-30506dbf07bf)

#### **NULL 관련 함수**

|||
|:-|:-|
|NVL(인수1,인수2)|- **인수1의 값이<span style="color:#ff6600">NULL이면 인수2를 반환</span>** <br/> - **<span style="color:#ff6600">NULL이 아닐 경우 인수1</span>**을 반환 <br/>- `NVL(MGR, 0)`은 MGR 컬럼이 NULL이면 0으로 바꾼다.|
|NVL2(컬럼,인수1,인수2)|- **<span style="color:#ff6600">함수와 DECODE 함수를 하나로 만든 것</span>** <br/>- `NVL2(MGR, 1, 0)`은 MGR컬럼이 NULL이 아니면 1을, <br/> NULL이면 0을 반환한다.|
|NULLIF(인수1,인수2)|- **<span style="color:#ff6600">인수1과 인수2의 값이 같으면 NULL <br/> - 같지 않으면 인수1을 반환</span>** <br/> - `NULLIF(exp1, exp2)`은 exp1과 exp2가 같으면 NULL을, <br/> 같지 않으면 exp1을 반환한다.|
|COALESCE(인수1,인수2,...)| - **<span style="color:#ff6600">NULL이 아닌 최초의 인수를 반환</span>**한다. <br/> - `COALESCE(exp1, exp2, exp3, …)`은 <br/> exp1이 NULL이 아니면 exp1의 값을,  <br/> 그렇지 않으면 그 뒤의 값의 NULL 여부를 판단하여 값을 반환|

<br/>

## 🎯 **추가 내용**

### **❌ 순수 관계 연산자**

SELECT,PROJECT, JOIN, DIVISION

#### **👉 SELECT**

- 릴레이션에 존재하는 튜플들 중에서 특정 조건을 만족하는 튜플들의 부분집합을 구하여 새로운 릴레이션을 만든다.

- 릴레이션의 행에 해당하는 튜플을 구하는 것으로, 수평 연산이라고도 한다.

- SELECT 연산의 기호는 그리스 문자 시그마(σ)이다.

- 표기 형식 : σ(조건)(R)

  - R은 릴레이션

  - 조건에서는 =, ≠, <, ≤, >, ≥ 등의 기호를 사용한 비교 연산이 허용, AND(∧), OR(∨), NOT(ㄱ) 등의 논리 연산자를 사용하여 여러 개의 조건들을 하나의 조건으로 결합시킬 수도 있다.

#### **👉 PROJECT**

- 주어진 릴레이션에서 속성 리스트에 제시된 속성 값만을 추출하여 새로운 릴레이션을 만든다. 단, 연산 결과에 중복이 발생하면 중복이 제거된다.

- 릴레이션에서 열에 해당하는 속성을 추출하는 것으로, 수직 연산이라고도 한다.

- PROJECT 연산의 기호는 그리스 문자 파이(π)이다.

- 표기 형식 : π(속성 리스트)(R)

  - R은 릴레이션

#### **👉JOIN**

- 공통 속성을 중심으로 2개의 릴레이션을 하나로 합쳐서 새로운 릴레이션을 만든다.

- JOIN 연산의 결과로 만들어진 릴레이션의 차수는 조인된 두 릴레이션의 차수를 합한 것과 같다.

- JOIN 연산의 결과는 CARTESIAN PRODUCT 연산을 수행한 다음 SELECT 연산을 수행한 것과 같다.

- JOIN 연산의 기호는 ▷◁ 이다.

- 표기 형식 : R▷◁(JOIN 조건)S

  - R, S는 릴레이션

  - =, ≠, <, ≤, >, ≥ 등의 비교 연산자를 비교 연산자를 θ로 일반화하여 θ로 표현될 수 있는 조인을 세타 조인(θ-join, Theta JOIN)이라고 한다.

  - JOIN 조건이 '='일 때 동일한 속성이 2번 나타나게 되는데, 이 중 중복된 속성을 제거하여 같은 속성은 1번만 나타나게 하는 연산을 자연 조인(NATURAL JOIN)이라고 한다.

  - NATURAL JOIN에서는 WHERE절에서 JOIN조건을 걸 수가 없다.

  - 자연 조인과 반대로 중복된 속성을 나타내는 연산은 동일 조인(Equi JOIN)이다.

#### **👉 DIVISION**

- 두 릴레이션 R(X)과 S(Y)에 대해 Y⊆X, X-Y=Z라고 하면, R(X)와 R(Z,Y)는 동일한 표현이다. 이때, 릴레이션 R(Z,Y)에 대한 S(Y)의 DIVISION 연산은 S(Y)의 모든 튜플에 연관되어 있는 R(Z)의 튜플을 선택하는 것이다.

- 표기 형식 : R[속성r ÷ 속성s]S

  - 속성 r은 릴레이션 R의 속성, 속성 s는 릴레이션 S의 속성이며, 속성 r과 s는 동일 소성 값을 가지는 속성이어야 한다.

<br/>


## 📚 **레퍼런스**

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
{:.prompt-info }

> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
{:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.2 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-2/dashboard){:target="_blank"}
{:.prompt-info }

> [[Oracle] SQL SELECT 쿼리 실행 순서 처리 과정 / [마이자몽] ](https://myjamong.tistory.com/172){:target="_blank"}
{:.prompt-info }

