---
layout: post
title: Real MySQL 05. 트랜잭션과 잠금 - 3. InnoDB 스토리지 엔진 잠금
author: admin
date: 2023-09-26 00:00:00 +900
lastmod: 2023-09-26 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [DATABASE, REAL MYSQL]  # 대문자로 작성
tags: [database, mysql, real mysql] # 소문자로 작성
---

> 해당 포스트는 `MySQL`를 학습하며 필요한 내용을 정리한 포스트입니다.<br/>
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 5. 트랜잭션과 잠금**

## **💻 5.3 InnoDB 스토리지 엔진 잠금**

InnoDB 스토리지 엔진은 스토리지 엔진 내부에서 레코드 기반의 잠금 방식을 탑재하고 있다. 

InnoDB는 레코드 기반의 잠금 방식 때문에 MyISAM보다는 훨씬 뛰어난 동시성 처리를 제공할 수 있다. 

하지만 이원화된 잠금 처리 탓에 InnoDB 스토리지 엔진에서 사용되는 잠금에 대한 정보는 MySQL 명령을 이용해 접근하기가 상당히 까다롭다.

최근 버전에서는 InnoDB의 트랜잭션과 잠금, 그리고 잠금 대기 중인 트랜잭션의 목록을 조회할 수 있는 방법이 도입됐다. 

MySQL 서버의 `information_schema` 데이터베이스에 존재하는 `INNODB_TRX`, `INNODB_LOCKS`, `INNODB_LOCK_WAITS`라는 테이블을 조인해서 조회하면 현재 어떤 트랜잭션이 어떤 잠금을 대기하고 있고 해당 잠금을 어느 트랜잭션이 가지고 있는지 확인할 수 있으며, 또한 장시간 잠금을 가지고 있는 클라이언트를 찾아서 종료시킬 수도 있다.

<br/>

### **🍳 5.3.1 InnoDB 스토리지 엔진의 잠금**

InnoDB 스토리지 엔진은 레코드 기반의 잠금 기능을 제공하며, 잠금 정보가 상당히 작은 공간으로 관리되기 때문에 레코드 락이 페이지 락으로, 또는 테이블 락으로 레벨업 되는 경우(락 에스컬레이션)는 없다.

일반 사용 DBMS와는 조금 다르게 InnoDB 스토리지 엔진에서는 레코드 락뿐 아니라 레코드와 레코드 사이의 간격을 잠그는 갭(GAP) 락이라는 것이 존재한다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/801383d3-7623-4357-a682-6f3763ddb98b)

점선 레코드는 실제 존재하지 않는 레코드를 가정한 것임

<br/>

#### **5.3.1.1 레코드 락**

레코드 자체만을 잠그는 것을 레코드 락이라고 하며, 다른 상용 DBMS의 레코드 락과 동일한 역할을 한다. 

중요한 차이는 InnoDB 스토리지 엔진은 레코드 자체가 아니라 인덱스의 레코드를 잠근다는 점이다.

인덱스가 하나도 없는 테이블이더라도 내부적으로 자동 생성된 클러스터 인덱스를 이용해 잠금을 설정한다.

InnoDB에서는 대부분 보조 인덱스를 이용한 변경 작업은 넥스트 키 락(Next Key Lock) 또는 갭(Gap Lock)을 사용하지만, 프라이머리 키 또는 유니크 인덱스에 의한 변경 작업에서는 갭에 대해서는 잠그지 않고 레코드 자체에 대해서만 락을 건다.

<br/>

#### **5.3.1.2 갭 락**

갭 락은 레코드 자체가 아니라 레코드와 바로 인접한 레코드 사이의 간격만을 잠그는 것을 의미한다. 

갭 락의 역할은 레코드와 레코드 사이의 간격에 새로운 레코드가 생성(INSERT)되는 것을 제어하는 것이며, 넥스트 키 락의 일부로 자주 사용된다.

<br/>

#### **5.3.1.3 넥스트 키 락**

레코드 락과 갭 락을 합쳐 놓은 형태의 잠금을 넥스트 키 락이라고 한다. 

STATEMENT 포맷의 바이너리 로그를 사용하는 MySQL 서버에서는 REPEATABLE READ 격리 수준을 사용해야 한다. 

`innodb_locks_unsafe_for_binlog` 시스템 변수가 비활성화되면(`0`으로 설정되면) 변경을 위해 검색하는 레코드에는 넥스트 키 락 방식으로 잠금이 걸린다.

InnoDB의 갭 락이나 넥스트 키 락은 바이너리 로그에 기록되는 쿼리가 레플리카 서버에서 실행될 때 소스 서버에서 만들어 낸 결과와 동일한 결과를 만들어내도록 보장하는 것이 주목적이다. 

넥스트 키 락과 갭 락으로 인해 데드락이 발생하거나 다른 트랜잭션을 기다리게 만드는 일이 자주 발생한다.

바이너리 로그 포맷을 ROW 형태로 바꿔서 넥스트 키 락이나 갭 락을 줄이는 것이 좋다.

<br/>

#### **5.3.1.4 자동 증가 락**

MySQL에서는 자동 증가하는 숫자 값을 추출(채번)하기 위해 `AUTO_INCREMENT`라는 칼럼 속성을 제공한다.

`AUTO_INCREMENT` 칼럼이 사용된 테이블에 동시에 여러 레코드가 INSERT되는 경우, 저장되는 각 레코드는 중복되지 않고 저장된 순서대로 증가하는 일련번호 값을 가져야 하기 때문에 내부적으로 AUTO_INCREMENT 락이라고 하는 테이블 수준의 잠금을 사용한다.

AUTO_INCREMENT 락은 INSERT와 REPLACE 쿼리 문장과 같이 새로운 레코드를 저장하는 쿼리에서만 필요하며, UPDATE나 DELETE 등의 쿼리에서는 걸리지 않는다. 

InnoDB의 다른 잠금(레코드 락이나 넥스트 키 락)과는 달리 AUTO_INCREMENT 락은 트랜잭션과 관계없이 INSERT나 REPLACE 문장에서 AUTO_INCREMENT 값을 가져오는 순간만 락이 걸렸다가 즉시 해제된다. 

AUTO_INCREMENT 락은 테이블에 단 하나만 존재하기 때문에 두 개의 INSERT 쿼리가 동시에 실행되는 경우 하나의 쿼리가 AUTO_INCREMENT 락을 걸면 나머지 쿼리는 AUTO_INCREMENT 락을 기다려야 한다.

AUTO_INCREMENT 컬럼에 명시적으로 값을 설정하더라도 자동 증가 락을 걸게 된다.

<br/>

### **🍳 5.3.2 인덱스와 잠금**

InnoDB의 잠금은 레코드를 잠그는 것이 아니라 인덱스를 잠그는 방식으로 처리된다. 

즉, 변경해야 할 레코드를 찾기 위해 검색한 인덱스의 레코드를 모두 락을 걸어야 한다. 

정확한 이해를 위해 아래 UPDATE 문장을 한번 살펴보자.

```sql
-- // 예제 데이터베이스의 employees 테이블에는 아래와 같이 first_name 칼럼만
-- // 멤버로 담긴 ix_firstname이라는 인덱스가 준비돼 있다.
-- // KEY ix_firstname(first_name)
-- // employess 테이블에서 first_name='Georgi'인 사원은 전체 253명이 있으며,
-- // first_name='Georgi'이고 last_name='Klssen'인 사원은 딱 1명만 있는 것을 아래 쿼리로 확인할 수 있다.
mysql> SELECT COUNT(*) FROM employess WHERE first_name='Georgi';
+--------+
 |      253  |
+--------+

mysql> SELECT COUNT(*) FROM employess WHERE first_name='Georgi' AND last_name='Klssen';
+--------+
 |           1  |
+--------+

-- // employees 테이블에서 first_name='Georgi'이고, last_name="Klassen'인 사원의
-- // 입사 일자를 오늘로 변경하는 쿼리를 실행해보자.
mysql> UPDATE employess SET hire_date=NOW() WHERE first_name='Georgi' AND last_name='Klassen';
```

위 UPDATE 문장을 실행하면 인덱스를 이용할 수 있는 조건은 first_name='Georgi'이며, last_name 칼럼은 인덱스에 없기 때문에 first_name='Georgi'인 레코드 253건의 레코드가 모두 잠긴다. 

위 예제에서는 몇 건 안 되는 레코드만 잠그지만 UPDATE 문장을 위해 적절히 인덱스가 준비돼 있지 않다면 각 클라이언트 간의 동시성이 상당히 떨어져서 한 세션에서 UPDATE 작업을 하는 중에는 다른 클라이언트는 그 테이블을 업데이트하지 못하고 기다려야 하는 상황이 발생할 것이다.

아래 이미지는 UPDATE 문장이 어떻게 변경 대상 레코드를 검색하고, 실제 변경이 수행되는지를 보여준다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ccf80ec9-4edd-43cc-859f-4fe5f9a31aea)

만약 위 테이블에서 인덱스가 하나도 없다면 어떻게 될까? 

테이블을 풀 스캔하면서 UPDATE 작업을 하는데, 이 과정에서 테이블의 모든 레코드를 잠그게 된다. 

이것이 MySQL의 방식이며, MySQL의 InnoDB에서 인덱스 설계가 중요한 이유이다.

<br/>

### **🍳 5.3.3 레코드 수준의 잠금 확인 및 해제**

레코드 수준의 잠금은 테이블 수준 잠금보다 조금 더 복잡하고 문제의 원인을 발견하고 해결하기도 어렵다. 

MySQL 5.1부터는 레코드 잠금과 잠금 대기에 대한 조회가 가능해져 쿼리 하나로 잠금과 잠금 대기를 바로 확인할 수 있다.

```sql
// 명령이 실행된 상태의 프로세스 목록을 조회
SHOW PROCESSLIST;

// performance_schema의 data_locks 테이블과 data_lock_waits 테이블을 조인해 
// 잠금 대기 순서 조회

SELECT
    r.trx_id waiting_trx_id,
    r.trx_mysql_thread_id waiting_thread, 
    r.trx_query waiting_query,
    b.trx_id blocking_trx_id, 
    b.trx_mysql_thread_id blocking_thread, 
    b.trx_query blocking_query
FROM performance_schema.data_lock_waits w 
    INNER OOIN information_schema.innodb_trx b
        ON b.trx_id = w.blocking_engine_transaction_id 
    INNER JOIN information_schema.innodb_trx r
        ON r.trx_id = w.requesting_engine_transaction_id;
```

만약 특정 스레드가 어떤 잠금을 가지고 있는지 더 상세히 확인하고 싶다면 performance_schema의 data_locks 테이블이 가진 컬럼을 모두 살펴보면 된다.

```sql
SELECT * FROM performance_schema.data_locks\G
```

만약 특정 스레드가 잠금을 가진 상태에서 오랜 시간 멈춰있다면, 다음과 같이 특정 스레드를 강제 종료하여 잠금 경합을 끝낼 수 있습니다.

```sh
// KILL {특정 스레드번호}
KILL 17
```

<br/>

---

<br/>

## 📚 **레퍼런스**

> 백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드 1 / 백은빈, 이성욱 지음. (2021). Web.
{:.prompt-info}

>백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드. 2 / 백은빈, 이성욱 지음 (2021). Web.
{:.prompt-info}
