---
layout: post
title: Real MySQL 04. 아키텍처 - 4. MySQL 로그 파일
author: admin
date: 2023-09-21 00:00:00 +900
lastmod: 2023-09-21 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, REAL MYSQL]  # 대문자로 작성
tags: [database, mysql, real mysql] # 소문자로 작성
---

> 해당 포스트는 `MySQL`를 학습하며 필요한 내용을 정리한 포스트입니다.<br/>
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 4. 아키텍처**

## **💻 4.4 MySQL 로그 파일**

MySQL 서버에 문제가 생겼을 때는 로그 파일들을 자세히 확인하는 습관이 필요하다.

### **🍳 4.4.1 에러 로그 파일**

MySQL이 실행되는 도중에 발생하는 에러나 경고 메세지가 출력되는 로그 파일이다.

에러 로그 파일 위치는 MySQL 설정파일(my.cnf)에서 `log_err`라는 이름의 파라미터로 정의된 경로에 생선된다. 

설정 파일에 별도로 정의되지 않은 경우에는 데이터 디렉터리에 `.err` 확장자가 붙은 파일로 생성된다. 

- MySQL이 시작하는 과정과 관련된 정보성 및 에러 메시지

- 비정상적으로 종료된 경우 나타나는 InnoDB의 트랜잭션 복구 메시지

- 쿼리 처리 도중에 발생하는 문제에 대한 에러 메시지

- 비정상적으로 종료된 커넥션 메시지(Aborted connection)

- InnoDB의 모니터링 또는 상태 조회 명령의 결과 메시지

- MySQL의 종료 메시지

<br/>

### **🍳 4.4.2 제너럴 쿼리 로그 파일(제네럴 로그파일, General log)**

MySQL 서버에서 실행되는 전체 쿼리를 조회하기 위해 사용한다.

<br/>

### **🍳 4.4.3 슬로우 쿼리 로그**

슬로우 쿼리 로그 파일엔 `long_query_time` 시스템 변수에 설정한 시간 이상이 소요된 쿼리가 기록된다. 

실제 소요된 시간을 기준으로 기록 여부를 판단하기 때문에 정상적으로 실행된 쿼리만 기록된다. 

`log_output` 옵션을 이용하여 로그를 파일로 기록할지 테이블로 기록할지 선택할 수 있다. 

하지만 테이블로 설정하더라도 slow_log 테이블과 general_log 테이블은 CSV 스토리지 엔진을 사용하기 때문에 파일 저장과 동일하게 동작한다. 

로그 분석을 좀 더 쉽게 하기 위해 Percona Toolkit의 pt-query-digest 스크립트를 사용할 수 있다.

#### **슬로우 쿼리 로그 구성**

`Time`: 쿼리가 종료된 시점

`User@Host`: 쿼리를 실행한 사용자 계정

`Query_time`: 쿼리가 실행되는 데 걸린 시간

`Lock_time`: InnoDB의 경우 MySQL 엔진 레벨의 잠금과 스토리지 엔진 자체의 잠금을 가지고 있는데, Lock_time은 MySQL 엔진 레벨에서의 테이블 잠금에 대한 대기 시간을 의미한다. 이 값은 쿼리 실행 전의 잠금 체크와 같은 코드 실행 시간까지 포함되기 때문에 매우 작은 값이라면 무시해도 무방하다.

`Rows_examined`: 쿼리가 처리되기 위해 접근한 레코드

`Rows_sent`: 실제 클라이언트로 보내진 레코드

<br/>

---

<br/>

## 📚 **레퍼런스**

> 백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드 1 / 백은빈, 이성욱 지음. (2021). Web.
{:.prompt-info}

>백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드. 2 / 백은빈, 이성욱 지음 (2021). Web.
{:.prompt-info}
