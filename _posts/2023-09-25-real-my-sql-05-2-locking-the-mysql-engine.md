---
layout: post
title: Real MySQL 05. 트랜잭션과 잠금 - 2. MySQL 엔진의 잠금
author: admin
date: 2023-09-25 00:00:00 +900
lastmod: 2023-09-25 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, REAL MYSQL 1]  # 대문자로 작성
tags: [database, mysql] # 소문자로 작성
---

> 해당 포스트는 `MySQL`를 학습하며 필요한 내용을 정리한 포스트입니다.<br/>
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 5. 트랜잭션과 잠금**


## **💻 5.2 MySQL 엔진의 잠금**

MySQL 에서 사용 되는 잠금은 크게 `스토리지 엔진 레벨`과 `MySQL 엔진 레벨`로 나눌 수 있다. MySQL 엔진은 MySQL 서버에서 스토리지 엔진 영역을 제외한 나머지 부분으로 이해할 수 있다.

MySQL 엔진 레벨의 잠금은 모든 스토리지 엔진에 영향을 미치게 되지만, 스토리지 엔진 레벨의 잠금은 스토리지 엔진 간 상호 영향을 미치지는 않는다.

MySQL 엔진에서 테이블 데이터 동기화를 위한 테이블의 락 이에외에도 테이블 구조를 잠그는 `메타데이터 락(Metadata Lock)` 그리고 사용자의 필요에 따라 사용할 수 있는 `네임드 락(Named Lock)` 이라는 기능도 제공을 한다.

### **🍳 5.1.1 글로벌 락**

글로벌 락(Global Lock) 은 `FLUSH TABLES WITH READ LOCK` 명령으로 획득할 수 있으며 MySQL 제공하는 잠금 가운데 가장 범위가 크다.

한 세션에서 글로벌 락을 획득하면 다른 세션에서 SELECT 를 제외한 대부분의 DDL 과 DML 문장은 해당 글로벌 락에 의해서 대기한다.

글로벌 락이 영향을 미치는 범위는 MySQL 서버 전체 대상이며, 작업 대상 테이블이나 데이터베이스가 다르더라도 동일하게 영향을 미친다.

MyISAM 이나 MEMORY 테이블에 대해서 mysqldump 유틸리티를 통해서 일관된 백업을 받아야 할 때 글로벌 락을 사용해야 한다.   

글로벌 락을 실행하기 전에 테이블이나 레코드에 쓰기 잠금을 거는 SQL 이 실행 중이라면 해당 SQL 트랜잭션이 완료 될때 까지 기다려야  한다.

또한 테이블에 읽기 잠금을 거는 과정에서 먼저 테이블을 FLUSH 해야 하기 때문에 테이블에 실행중인 모든 종류의 쿼리가 완료 되어야 한다.

mysqldump 를 이용해 백업을 수행시 사용하는 옵션에 따라서 MySQL 서버에서 사용 되는 잠금이 다르게 되며 많이 사용 되는 옵션으로 `--single-transaction` 사용할 경우 InnoDB Storage 엔진의 테이블은 잠금을 걸지 않고 `Isolation Level - repeatable read` 를 사용한 MVCC 를 통해서 일관된 백업을 받게 된다

MySQL 8.0 부터는 InnoDB 스토리지 엔진이 기본으로 되었고, 조금 더 가벼운 글로벌 락의 필요성이 생기게 되었다. 그래서 Xtrabackup 또는 Enterprise Backup 과 같은 백업 툴에서 안정적인 실행을 위한 백업을 위한 Lock 이 추가되었다.

```sql
mysql> LOCK INSTANCE FOR BACKUP;
Query OK, 0 rows affected (0.00 sec)
-- //백업 실행
mysql> UNLOCK INSTANCE;
Query OK, 0 rows affected (0.00 sec)
```

이 새로운 유형의 백업 잠금은 온라인 백업 중에 DML은 허용하는 동시에 일관성 없는 스냅샷이 생성될 수있는 있는 작업을 방지한다.

새 백업 잠금은 `LOCK INSTANCE FOR BACKUP` 및 `UNLOCK INSTANCE` 구문을 통해서 사용할 수 있으며 이러한 명령어를 사용하기 위해서는 `BACKUP_ADMIN` 권한이 필요하다.

새로운 백업락을 사용하면 다음과 같이 테이블의 스키마나 사용자의 인증 관련 정보는 변경 할 수 없지만, 일반적인 테이블에 대한 변경은 허용한다.

- 데이터베이스 및 테이블 등의 모든 객체 생성 및 객체 변경, 삭제

- REPAIR TABLE , OPTIMIZER TABLE 명령

- 사용자 관리 및 비밀번호 변경

일반적으로 백업을 `Replica` 인스턴스에서 수행하더라도 `Xtrabackup` 이나 `Enterprise Backup` 툴이 실행되는 도중에 스키마 변경이 발생되면 백업은 실패할 수 있게 되는데 장시간 수행되었던 백업이었다면 다시 수행하기 위해 또 많은 시간이 소요되고 필요하게 된다. 

그래서 8.0 버전에서 이런 목적으로 백업락이 도입이 되었으며, 정상적으로 복제는 이루어지지만 백업의 실패를 막기 위해서 DDL 명령이 실행되면 복제를 일시 중지하는 역할을 한다.

<br/>

### **🍳 5.1.2 테이블 락**

테이블 락(Table Lock)은 개별 테이블 단위로 설정되는 잠금이며, 명시적 또는 묵시적으로 특정 테이블 락을 획득할 수 있다. 

명시적으로는 `LOCK TABLES 테이블명 [read | write ]` 명령으로 특정 테이블 락을 획득 할 수 있다.

테이블 락은 MyISAM 과 InnoDB 스토리지 엔진을 사용하는 테이블 모두 동일하게 설정할 수 있다.

명시적으로 획득한 잠금은 `UNLOCK TABLES` 명령으로 잠금을 해제를 할 수 있다.

특별한 경우가 아니라면 애플리케이션에서 사용할 필요는 없겠지만, 사용을 하게 된다면 글로벌 락과 동일하게 온라인 작업에서의 상당한 영향을 미칠수도 있다.

묵시적인 테이블 락은 MyISAM 이나 MEMORY 테이블에 데이터를 변경하는 쿼리를 실행하면 발생을 하게 된다.

사용자가 데이터를 변경하면 MySQL 서버는 테이블에 잠금을 설정하고 데이터를 변경한 한 후 잠금을 해제 하는 형태로 사용 된. 

즉, 묵시적인 테이블 락은 쿼리가 실행되는 동안 자동으로 획득되었다가 쿼리가 완료된 후 자동 해제된다.

InnoDB 스토리지 엔진 테이블의 경우 대부분의 데이터 변경(DML) 은 레코드 기반의 잠금을 사용하고 테이블 레벨의 잠금은 스키마 변경을 막기 위해 사용된다.

<br/>

### **🍳 5.1.3 네임드 락**

네임드 락(Named Lock) 은 `GET_LOCK()` 함수를 이용하여 임의의 문자열을 지정하여 잠금을 설정할 수 있다.

이 잠금의 특징은 대상 테이블이나 레코드 또는 AUTO_INCREMENT와 같은 데이터베이스 객체가 아닌, 단순히 사용자가 지정한 문자열(String)에 대해 잠금을 획득하고 반납(해제) 하는 잠금이다.

사실 네임드 락은 자주 사용되지는 않는다.

- 데이터베이스 서버 1대에 5대의 웹 서버가 접속해서 서비스 하는 상황에서 5대의 웹 서버가 어떤 정보를 동기화해야 하는 요건처럼 여러 클라이언트가 상호 동기화를 처리해야 할 때 네임드 락을 이용하면 쉽게 해결할 수 있다.

- 네임드 락의 경우 많은 레코드에 대해서 복잡한 요건으로 레코드를 변경하는 트랙잭션에 유용하게 사용할 수 있다. 

배치 프로그램처럼 많은 레코드를 변경하는 쿼리는 자주 데드락의 원인이 되곤 한다. 

- 각 프로그램의 실행 시간을 분산하거나 프로그램의 코드를 수정해서 데드락을 최소화 할 수는 있지만 완전한 해결책은 아니다.

- 동일 데이터를 변경하거나 참조하는 프로그램끼리 분류해서 네임드 락을 걸고 쿼리를 실행하면 해결할 수 있다.

<br/>

### **🍳 5.1.4 메타데이터 락**

메타데이터 락은 데이터베이스 객체(테이블이나 뷰 등)의 이름이나 구조를 변경하는 경우에 획득하는 잠금이다.

메타데이터 락은 명시적으로 획득하거나 해제할 수 있는 것이 아니고 "RENAME TABLE tab_a TO tab_b"와 같이 테이블의 이름을 변경하는 경우 자동으로 획득하는 잠금이다.

RENAME TABLE 명령의 경우 원본 이름과 변경될 이름 두 개 모두 한꺼번에 잠금을 설정한다. 이는 실시간으로 테이블을 바꿔야 하는 요건이 배치 프로그램에서 자주 발생하는데, 아래 예제를 잠깐 살펴보자

```sql
-- // 배치 프로그램에서 별도의 임시 테이블(rank_new)에 서비스용 랭킹 데이터를 생성

-- // 랭킹 배치가 완료되면 현재 서비스용 랭킹 테이블(rank)을 rank_backup으로 백업하고
-- // 새로 만들어진 랭킹 테이블(rank_new)을 서비스용으로 대체하고자 하는 경우
mysql> RENAME TABLE rank TO rank_backup, rank_new To rank;
```

위와 같이 하나의 RENAME TABLE 명령문에 두 개의 RENAME 작업을 한꺼번에 실행하면 실제 애플리케이션에서는 정상적으로 적용하는 것이 가능하다. 하지만 위 문장을 두 개로 나눠서 실행하면 아주 짧은 시간이지만 rank 테이블이 존재하지 않는 순간이 생기며, "Table not found 'rank'" 오류를 발생시킨다.

```sql
mysql> RENAME TABLE rank TO rank_backup
mysql> RANAME TABLE rank_new To rank;
```

때로는 메타데이터 잠금과 InnoDB의 트랜잭션을 동시에 사용해야 하는 경우도 있다. 

예를 들어, 아래와 같은 구조의 INSERT만 실행되는 로그 테이블을 가정해보자. 

이 테이블은 웹 서버의 액세스(접근) 로그를 저장만 하기 때문에 UPDATE와 DELETE가 없다.

```sql
mysql> CREATE TABLE access_log (
                     id BIGINT NOT NULL AUTO_INCREMENT,
                     client_ip INT UNSIGNED,
                     access_dttm TIMESTAMP,
                     ...
                     PRIMARY KEY (id)
              );
```

만약 어느 날 위 테이블의 구조를 변경해야 할 요건이 발생했다. 

MySQL 서버의 Online DDL을 이용하면 변경할 수도 있지만 시간이 너무 오래 걸리는 경우라면 언두 로그의 증가와 Online DDL이 실행되는 동안 누적된 Online DDL 버퍼의 크기 등 고민해야 할 문제가 많다. 

더 큰 문제는 MySQL 서버의 DDL은 단일 스레드로 작동하기 때문에 상당히 많은 시간이 소모될 것이라는 점이다. 

이때는 새로운 구조의 테이블을 생성하고 먼저 최근(1시간 직전 또는 하루 전)의 데이터까지는 프라이머리 키인 id 값을 범위별로 나눠서 여러 개의 스레드로 빠르게 복사한다.

```sql
-- // 테이블의 압축을 적용하기 위해 KEY_BLOCK_SIZE=4 옵션을 추가해 신규 테이블을 생성
mysql> CREATE TABLE access_log_new (
                     id BIGINT NOT NULL AUTO_INCREMENT,
                     client_ip INT UNSIGNED,
                     access_dttm TIMESTAMP,
                     ...
                     PRIMARY KEY (id)
              ) KEY_BLOCK_SIZE=4;

-- // 4개의 스레드를 이용해 id 범위별로 레코드를 신규 테이블로 복사
mysql_thread1> INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=0 AND id<10000;
mysql_thread2> INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=10000 AND id<20000;
mysql_thread3> INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=20000 AND id<30000;
mysql_thread4> INSERT INTO access_log_new SELECT * FROM access_log WHERE id>=30000 AND id<40000;
```

그리고 나머지 데이터는 아래와 같이 트랜잭션과 테이블 잠금, RENAME TABLE 명령으로 응용 프로그램의 중단 없이 실행할 수 있다. 

이때 "남은 데이터를 복사"하는 시간 동안은 테이블의 잠금으로 인해 INSERT를 할 수 없게 되기 때문에 가능하면 미리 최근 데이터까지 복사해 두어야 잠금 시간을 최소화할 수 있다.

```sql
-- // 트랜잭션을 autocommit으로 실행(BEGIN이나 START TRANSACTION으로 실행하면 안 됨)
mysql> SET autocommit=0;


-- // 작업 대상 테이블 2개에 대해 테이블 쓰기 락을 획득
mysql> LOCK TABLES access_log WRITE, access_log_new WRITE;

-- // 남은 데이터를 복사
mysql> SELECT MAX(id) as @MAX_ID FROM access_log_new;
mysql> INSERT INTO access_log_new SELECT * FROM access_log WHERE pk>@MAX_ID;
mysql> COMMIT;

-- // 새로운 테이블로 데이터 복사가 완료되면 RENAME 명령으로 새로운 테이블을 서비스로 투입
mysql> RENAME TABLE access_log TO access_log_old, access_log_new TO access_log;
mysql> UNLOCK TABLES;

-- // 불필요한 테이블 삭제
mysql> DROP TABLE access_log_old;
```

<br/>

---

<br/>

## 📚 **레퍼런스**

> 백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드 1 / 백은빈, 이성욱 지음. (2021). Web.
{:.prompt-info}

>백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드. 2 / 백은빈, 이성욱 지음 (2021). Web.
{:.prompt-info}
