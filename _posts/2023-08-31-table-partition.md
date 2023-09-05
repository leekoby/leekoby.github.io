---
layout: post
title: TABLE PARTITION (테이블 파티션)
author: admin
date: 2023-08-31 00:00:00 +900
lastmod: 2023-08-31 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, table partition] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br />

# **🌈 TABLE PARTITION (테이블 파티션)**

## **💻 PARTITION의 기능**

**<span style="color:#ff6600">파티션은 대용량의 테이블을 여러 개의 데이터 파일에 분리해서 저장한다.</span>**

테이블의 데이터가 물리적으로 분리된 데이터 파일에 저장되면 입력, 수정, 삭제, 조회 성능이 향상된다.

파티션은 각각의 파티션 별로 독립적으로 관리될 수 있다. 

- 즉, 파티션 별로 백업하고 복구가 가능하면 파티션 전용 인덱스 생성도 가능하다.

파티션은 Oracle 데이터베이스의 논리적 관리 단위인 테이블 스페이스 간에 이동이 가능하다.

데이터를 조회할 때 데이터의 범위를 줄여서 성능을 향상시킨다.

<br/>

## **💻 RANGE PARTITION**

**<span style="color:#ff6600">Range Partition은 테이블의 칼럼 중에서 값의 범위를 기준으로 여러 개의 파티션으로 데이터를 나누어 저장하는 것이다.</span>**

![RANGE PARTITION](https://github.com/leekoby/leekoby.github.io/assets/118284808/4be2ab78-3d02-4cfc-ab1e-d6ee86bce487){: width="500" height="500" }

- 위의 예에서는 SAL 값 2000〜4000은 Dataülel.dbf 파일에 저장하고 SAL 값 5000〜7000의 값은 Datafile2.dbf에 저장한다.

> **<span style="color:#ff6600">테이블 스페이스</span>**
>
> 데이터베이스 오브젝트 내 실제 데이터를 저장하는 공간이다.
>
> 이것은 데이터베이스의 물리적인 부분가 논리적인 부분으로 나뉜다.
{: .prompt-info }


<br/>

## **💻 LIST PARTITION**

**<span style="color:#ff6600">List Partition은 특정 값을 기준으로 분할하는 방법이다.</span>**

![LIST PARTITION](https://github.com/leekoby/leekoby.github.io/assets/118284808/1e3a422d-712a-432e-98e4-b4b98b669358){: width="500" height="500" }

- 위의 예에서 DEPTNO가 10번인 것은 Datafile.dbf에 저장하고 20번인 것은 Datafile2.dbf에 저장한다.


<br/>

## **💻 HASH PARTITION**

**<span style="color:#ff6600">Hash Partition은 데이터베이스 관리 시스템이 내부적으로 해시 함수를 사용해서 데이터를 분할한다.</span>**

결과적으로 데이터베이스 관리 시스템이 알아서 분할하고 관리하는 것이다.

![HASH PARTITION](https://github.com/leekoby/leekoby.github.io/assets/118284808/5d0cc9c6-3165-4a02-a930-8c5afc800685){: width="500" height="500" }

- Hash Partition 이외에도 Composite Partition 있는데, Composite Partition은 여러 개의 파티션 기법을 조합해서 사용하는 것이다.

<br/>

## **💻 파티션 인덱스**

파티션 인덱스는 4가지 유형의 인덱스를 제공한다. 

- 즉, 파티션 키를 사용해서 인덱스를 만드는 Prefixed Index와 해당 파티션만 사용하는 Local Index 등으로 나누어진다.

Oracle 데이터베이스는 Global Non-Prefixed를 지원하지 않는다.

|구분	|주요 내용|
|Global Index|	여러 개의 파티션에서 하나의 인덱스를 사용한다.|
|Local Index|	해당 파티션 별로 각자의 인덱스를 사용한다.|
|Prefixed Index	|파티션 키와 인덱스 키가 동일하다.|
|Non Prefixed Index	|파티션 키와 인덱스 키가 다르다.|

**<span style="color:#ff6600"></span>**

>**<span style="color:#3366ff"></span> <span style="color:#ff6600"></span>**

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

