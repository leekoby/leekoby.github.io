---
layout: post
title: DDL(Data Definition Language)
author: admin
date: 2023-08-27 06:00:00 +900
lastmod: 2023-08-27 06:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, data definition language, ddl] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br />

# **⚡ DDL(Data Definition Language)**

## **💾 테이블(Table) 생성**


### 테이블 생성

데이터베이스를 사용하기 위해서는 테이블을 먼저 생성해야 한다.

- **<span style="color:#ff6600">테이블 생성은 Create Table문</span>을 사용**

- **<span style="color:#ff6600">테이블 변경은 Alter Table문</span>을 사용**

- **<span style="color:#ff6600">테이블 삭제은 Drop Table문</span>을 사용**

<br/>

### **테이블 관리 SQL문**

|SQL문|설명|
|:-:|:-|
|Create Table|- 새로운 테이블을 생성한다. <br>- 테이블을 생성할 때 기본키, 외래키, 제약사항 등을 설정할 수 있다.|
|Alter Table|- 생성된 테이블을 변경한다. <br>- 컬럼을 추가하거나 변경,삭제할 수 있다.<br>- 기본키를 설정하거나, 외래키를 설정할 수 있다.|
|Drop Table|- 해당 테이블을 삭제한다.<br>- 테이블의 데이터 구조뿐만 아니라 저장된 데이터도 모두 삭제한다.|

<br/>

### **기본적인 테이블 생성** 

테이블을 생성하는 방법은 여러 가지가 있다. 먼저 아주 간단한 테이블 생성 방법을 확인해 보자.

![기본적인 테이블 생성 방법 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ac508307-889f-4acd-a213-8e136712dd4c){: width="500" height="500" }

<br/>

>**<span style="color:#3366ff">VARVHAR vs CHAR</span>** 
>
>**<span style="color:#3366ff">VARVHAR(n)</span>은 <span style="color:#ff6600">가변형으로 최대 n의 길이의 문자열을 저장할 수 있지만, 실제 사용하는 만큼만 공간을 차지, 비교 시 서로 길이가 다른 경우, 다른 내용으로 판단.</span>**
>
>**<span style="color:#3366ff">CHAR(n)</span>은 <span style="color:#ff6600">항상 n의 길이의 문자열을 저장. 만약 입력 문자열의 길이가 n보다 작으면, 나머지 공간은 공백으로 채워짐, 길이가 서로 다른 경우, 길이가 짧은 쪽에 공백을 추가하여 같은 내용인지 판단.</span>**
>
>**CHAR 필드의 처리 속도가 빠르고, VARCHAR 필드는 메모리를 보다 효율적으로 사용한다고 볼 수 있다.**<br/>
{:.prompt-info}

<br/>

### **Create Table문 구조**

|||
|:-:|:-|
|Create Table | - 새로운 테이블을 생성한다.<br/> - 테이블을 생성할 때 기본키, 외래키, 제약사항 등을 설정할 수 있다.|
|Alter Table | - 생성된 테이블을 변경한다. <br/> - 컬럼을 추가하거나 변경,삭제할 수 있다. <br/> - 기본키를 설정하거나, 외래키를 설정할 수 있다.|
|Drop Table | - 해당 테이블을 삭제한다. <br/> - 테이블의 데이터 구조뿐만 아니라 저장된 데이터도 모두 삭제한다.|

<br/>

테이블의 구조를 확인하고 싶다면 SQL 중에 DESC문이 있다. 

**<span style="color:#ff6600">`DESC(Describe)`문은 테이블의 구조를 확인할 때 사용</span>**된다.

즉 , Create Table로 생성된 테이블의 구조를 보고 싶을 때 사용한다.

> ORDER BY 의 desc와는 다른 것이므로 주의<br/>
{:.prompt-danger}

<br/>

### **🧷 제약조건 사용**

> 테이블을 생성할 때부터 제약조건을 설정하는 것이 좋다. <br/>
{:.prompt-tip}

기본키, 외래키, 기본값, not null 등은 테이블을 생성할 때 지정할 수 있다.

![제약조건 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/bf1901be-8878-442a-a007-09e19e67e49d)

- 위의 예를 보면, **`constraint`**를 사용하여 기본키와 기본키의 이름을 지정할 수 있다.

- 만약 위의 예에서 두 개의 기본키를 지정하고자 한다면 **`constraint emppk primary key(empno, ename)`**으로 지정하면 된다.

- sal 컬럼은 **`number(10,2)로 지정`**했다. 이것은 **`소수점 둘째 자리까지 저장`**하게 된다.

- Oracle 데이터베이스에서 **`sysdate`**는 **`오늘의 날짜를 조회`**한다. 이를 `default` 옵션을 사용해서 오늘 날짜를 기본값으로 지정할 수 있다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/62d99151-344d-422a-903d-77c71cb2b1e8)

<br/>

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/83f666fa-1728-485b-a047-35bd6f88c5af)

- 외래키를 지정하려면, 먼저 마스터(부모) 테이블이 생성되어야 한다. 

- 즉, 사원과 부서 테이블에서는 부서가 마스터 테이블이 된다.

- 사원 테이블이 부서 테이블의 `deptno`를 참조해야 하는 것이다.

- 그리고 EMP 테이블을 생성할 때 `constraint`를 사용하여 외래키 이름인 `“deptkf”`를 입력 후 외래키를 생성한다.

<br/>

### **테이블 생성 시 CASCADE 사용**

- 테이블을 생성할 때 CASCADE 옵션을 사용할 수 있다. 

- **<span style="color:#ff6600">CASCADE 옵션은 참조 관계(기본 키와 외래키 관계)가 있을 경우 참조되는 데이터를 자동으로 반영할 수 있는 것</span>**이다.

- 먼저, 마스터 테이블을 생성한다. 즉, DEPT 테이블을 생성하고 데이터를 입력한다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/98ed490c-a824-46c1-8420-2f2b3a982849)

- 그 다음 EMP 테이블을 생성하고 데이터를 입력한다. 

- 단, EMP 테이블을 생성할 때, **`On Delete Cascade`** 옵션을 사용한다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/3fcf086a-67e5-4d9f-b4ff-03fd0cfbc217)

- EMP 테이블을 생성하고 데이터 두개를 입력한다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f33478e5-31d7-46f8-9336-d51c3cbccb14)

- 위의 예제를 보면 DEPT 테이블에서 DEPTNO가 ‘1000’번인 인사팀을 삭제했다.

- 그리고 EMP 테이블의 데이터를 조회한 결과, DEPTNO ‘1000’번이였던 ‘임베스트’ 데이터도 자동으로 삭제된 것을 알 수 있다.

- 즉, **<span style="color:#ff6600">ON DELETE CASCADE 옵션은 자신이 참조하고 있는 테이블(DEPT)의 데이터가 삭제되면 자동으로 자신(EMP)도 삭제되는 옵션</span>**이다.

- **<span style="color:#ff6600">ON DELETE CASCADE 옵션을 사용하면 참조 무결성을 준수</span>**할 수 있다. 

>**<span style="color:#3366ff">참조 무결성이란, </span>** 
>
>**<span style="color:#ff6600">마스터 테이블(DEPT)에는 해당 부서번호(DEPTNO)가 없는데, 슬레이브 테이블(EMP)에는 해당 부서번호가 있는 경우를 참조 무결성 위배로 볼 수 있다.</span>**<br/>
{:.prompt-info}


>**<span style="color:#3366ff">마스터 테이블(DEPT)</span> VS <span style="color:#ff6600">슬레이브 테이블(EMP)</span>**
>
>종종 관계형 데이터베이스에서의 **`부모-자식 관계`** 또는 **`참조 관계`**를 설명하는 데 사용되는 용어
>
>**<span style="color:#3366ff">마스터 테이블</span>은 주로  <span style="color:#ff6600">참조의 기준점으로 사용되는 테이블</span>이다.**
> 종종 **`부모테이블`**로도 부른다.
> 
> - 예를 들어, 사원들의 `부서 정보`가 저장된 'DEPT' 테이블을 생각해보면, 각 부서에는 고유한 ID가 있고, 이 ID는 다른 테이블에서 부서를 참조할 때 사용된다.
>
>**<span style="color:#3366ff">슬레이브(또는 자식)테이블</span>은  <span style="color:#ff6600">마스터(또는 부모)테이블을 참조하는 행을 가지고 있는 테이블</span>이다.**
>
> -  예를 들어, `사원 정보`가 저장된 'EMP'테이블을 생각해보면 각 사원에 대한 정보와 함께 해당 사원의 부서 ID도 저장된다. 이 경우, 'EMP'테이블은 슬레아비(자식)테일반이며, 그들의 부서 ID 컬럼은 'DEPT'(마스터/부모)텍스트을 참조한다.
>
>**<span style="color:#3366ff">참조 무결성</span>은 <span style="color:#ff6600">이러한 관계를 유지하면서 데이터의 일관성을 보장하는 개념</span>**
>
> - 즉, EMP(슬레아비/자식)텍스트에 있는 모든 부서 ID(DEPTNO) 값들은 반드시 DEPT(마스터/부모)텍스트에 존재해야 한다. <br/>
{:.prompt-info}

<br/>

## **🔨 테이블 변경**

### **테이블명 변경**

**<span style="color:#ff6600">테이블명 변경은 ALTER TABLE ~ RENAME TO 문을 사용</span>하면 된다.**



![테이블명 변경 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c6cd10e6-8d20-482a-a888-142446f550d5)

<br/>

### **<span style="color:#ff6600">컬럼 추가</span>**

생성된 EMP 테이블에 ALTER TABLE ~ ADD문을 사용해서 컬럼을 추가한다.

![컬럼 추가 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6956fda9-77c1-48a8-8416-6e4880ec9f33)

<br/>

### **컬럼 변경**

**<span style="color:#ff6600">컬럼의 변경은 ALTER TABLE ~ MODIFY문을 사용</span>하면 된다. **

컬럼 변경을 통해 데이터 타입을 변경하거나 데이터의 길이를 변경할 수 있다.

컬럼을 변경할 때 제약조건을 설정할 수도 있다.

컬럼의 데이터 타입을 변경할 때 기존 데이터가 있는 경우 에러가 발생한다. 

- 예를 들어 숫자 타입이고 숫자 데이터가 저장되어 있는데 문자형 데이터 타입으로 변경하면 에러가 발생하는 것이다.

![컬럼 변경 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f9896d18-25ed-41b4-aa03-c09ccdb9cc63)

<br/>

### **컬럼 삭제**

**<span style="color:#ff6600">컬럼에 대한 삭제는 ALTER TABLE ~ DROP COLUMN문</span>으로 삭제한다.**

![컬럼 삭제 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a7e9a332-4e6d-478b-b6c3-53c404696f90)

<br/>

### **컬럼명 변경**

**<span style="color:#ff6600">컬럼명 변경은 ALTER TABLE ~ RENAME COLUMN ~ TO 문</span>으로 변경할 수 있다.**

![컬럼명 변경 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/414a2dcf-89b9-4323-98ee-78b0a2ab25f5)

<br/>

## **🪓 테이블 삭제**

**<span style="color:#ff6600">테이블 삭제는 DROP TABLE 문을 사용해서 삭제</span>할 수 있다.**

**<span style="color:#ff6600">DROP TABLE은 테이블의 구조와 데이터를 모두 삭제</span>한다.**

![테이블 삭제 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7d7bc80c-bd0f-4fda-8356-de5fdd15a517)

<br/>

**DROP TABLE에서 <span style="color:#ff6600">“CASCADE CONSTRAINT” 옵션을 사용할 수 있다. “CASCADE CONSTRAINT” 옵션은 해당 테이블의 데이터를 외래키로 참조한 슬레이브 테이블과 관련된 제약사항도 삭제</span>할 때 사용된다.**

![테이블 삭제 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/af96263d-9966-44a2-b2ca-24826313dfee)

<br/>

## 🎯 **추가 내용**

### **❌테이블의 제약 조건**

#### 👉 **설명**

- 외래키(Foreign Key)는 두 개의 테이블 간의 참조 무결성을 제약한다.

- 기본키(Primary Key) 제약사항은 테이블 당 하나만 제약할 수 있다.

- Check 제약조건(Constraint)은 특정 값만 입력되게 제안한다. 

- 고유키(Unique Key)제약조건으로 NULL은 허용하는데 단 1개만 허용한다. NOT NULL 일 경우 하나도 불가능


<br/>

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
