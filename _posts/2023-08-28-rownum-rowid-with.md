---
layout: post
title: ROWNUM, ROWID, WITH문
author: admin
date: 2023-08-28 10:00:00 +900
lastmod: 2023-08-28 10:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, rownum, rowid, with] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# **🌈 ROWNUM, ROWID, WITH문**

## **💻 ROWNUM**

**<span style="color:#ff6600">ROWNUM은 ORACLE 데이터베이스의 SELECT문 결과에 대해서 논리적인 일련번호를 부여</span>**한다

**<span style="color:#ff6600">ROWNUM은 조회되는 `행 수`를 `제한`할 때 많이 사용</span>**된다.

**<span style="color:#ff6600">ROWNUM은 화면에 데이터를 출력할 때 부여되는 논리적 순번</span>**이다.

- 만약 ROWNUM을 사용해서 페이지 단위 출력을 하기 위해서는 인라인 뷰(Inline view)를 사용해야 한다.

### **인라인 뷰(Inline view)**

- **<span style="color:#ff6600">인라인뷰는 SELECT문에서 FROM절에 사용되는 서브쿼리를 의미</span>**한다.
![인라인 뷰(Inline view)](https://github.com/leekoby/leekoby.github.io/assets/118284808/09e34072-62c8-4163-b8a2-9b4177b06c06){: width="500" height="500" }
- **<span style="color:#ff6600">ROWNUM의 값을 1, 2, 3, 4, 5, 6.... 같이 순차적으로 증가하는 ROWNUM 데이터를 얻고 싶을 때 인라인 뷰를 사용하는 것</span>**이다.
![인라인 뷰(Inline view)](https://github.com/leekoby/leekoby.github.io/assets/118284808/30dc6785-edeb-4f13-807c-7e4fdde290b5){: width="500" height="500" }
- 위의 예에서 “ROWNUM < 2"까지는 사용 가능하다. 
- 한 행을 가지고 올 수 있기 때문이다.
![인라인 뷰(Inline view)](https://github.com/leekoby/leekoby.github.io/assets/118284808/0c5c0e60-4675-43b2-9dfc-5fb9244b53d2){: width="500" height="500" }
- 위와 같이 5건의 행을 조회하기 위해서는 인라인 뷰를 사용하고 ROWNUM에 별칭을 사용해야 한다.

<br/>

### **SQL Server의 TOP 구문과 MySQL의 limit 구문**

Oracle은 ROWNUM을 사용하지만 SQL Server는 TOP문을 사용하고 MySQL은 LIMIT구를 사용한다. 

- 즉 10명만 인출(Fetch)하고자 할 때에는 다음과 같이 사용한다.

  - SQL SERVER
    - SELECT TOP(10) FROM EMP;

  - MySQL
    - SELECT * FROM EMP LIMIT 10; 

![ROWNUM을 활용하여 웹 게시판에서 사용되는 쿼리문의 형식](https://github.com/leekoby/leekoby.github.io/assets/118284808/165a52ad-1352-4507-98af-9846ffe9ca83)
- ROWNUM을 활용하여 웹 게시판에서 사용되는 쿼리문의 형식이다.

## **💻 ROWID**

**<span style="color:#ff6600">ROWID는 ORACLE 데이터베이스 내에서 데이터를 구분할 수 있는 유일한 값</span>**이다.

ROWID는 "SELECT ROWID. EMPNO FROM EMP”와 같은 SELECT문으로 확인할 수 있다.

ROWID를 통해서 데이터가 어떤 데이터 파일, 어느 블록에 저장되어 있는 지 알 수 있다.

ROWID는 **<span style="color:#ff6600">테이블에 데이터를 입력하면 자동으로 생성되는 값</span>**이다.

### **ROWID 구조**

|구조|길이|설명|
|-|-|-|
|오브젝트 번호|1~6|오브젝트별로 유일한 값을 가지고 있으며, 해당 오브젝트가 속해 있는 값이다.|
|상대 파일 번호|7~9|테이블스페이스에 속해 있는 데이터 파일에 대한 상대 파일번호이다.|
|블록 번호|10~15|데이터 파일 내부에서 어느 블록에 데이터가 있는지 알려준다.|
|데이터 번호|16~18|데이터 블록에 데이터가 저장되어 있는 순서를 의미한다.|

![ROWID](https://github.com/leekoby/leekoby.github.io/assets/118284808/1c98a1bf-3971-4442-afe7-a83df5cbb912)

## **💻 WITH문**

**<span style="color:#ff6600">WITH구문은 서브쿼리(Subquery)를 사용해서 임시 테이블이나 뷰처럼 사용할 수 있는 구문</span>**이다.

서브쿼리 블록에 별칭을 지정할 수 있다.

옵티마이저는 SQL을 인라인 뷰나 임시 테이블로 판단한다.

![WITH문](https://github.com/leekoby/leekoby.github.io/assets/118284808/9e24af30-b565-479a-9f5e-0fe7a86939cd)

>**<span style="color:#ff6600">UNION vs UNION ALL</span>**
>
> UNION
> - 2개의 테이블을 합해서 출력하며 중복은 제외하는 키워드 <br/>
>
> UNION ALL
> - 2개의 테이블을 합해서 출력하며 중복된 것을 포함한다.
{:.prompt-info}

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

