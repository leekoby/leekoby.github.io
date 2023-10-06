---
layout: post
title: Real MySQL 05. 트랜잭션과 잠금 - 4. MySQL의 격리 수준
author: admin
date: 2023-09-27 00:00:00 +900
lastmod: 2023-09-27 00:00:00 +900
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

## **💻 5.4 MySQL의 격리 수준**

트랜잭션의 격리 수준(Isolation Level)이란

- 여러 트랜잭션이 동시에 처리될 때 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지를 결정하는 것이다.

- 격리 수준은 크게 `READ UNCOMMITED`, `READ COMMITED`, `REPEATABLE READ`, `SERIALIZABLE` 4가지로 나뉜다.

- `DIRTY READ`라고도 하는 `READ UNCOMMITED`는 일반적인 데이터베이스에서는 거의 사용하지 않고, `SERIALIZABLE` 또한 동시성이 중요한 데이터베이스에서는 거의 사용되지 않는다.

- 4개의 격리 수준에서 순서대로 뒤로 갈수록 각 트랜잭션 간의 데이터 격리(고립)정도가 높아지며, 동시 처리 성능도 떨어지게 된다. 

- 격리 수준이 높아질수록 MySQL 서버의 처리 성능이 많이 떨어질 것으로 생각되지만, 사실 `SERIALIZABLE` 격리 수준이 아니라면 크게 성능의 개선이나 저하는 발생하지 않는다.

||DIRTY READ|NON-REPEATED READ|PHANTOM READ|비고|
|:-|:-|:-|:-|:-|
|READ UNCOMMITTED|발생|발생|발생|거의 사용하지 않음|
|READ COMMTTIED|없음|발생|발생|오라클,PostgreSQL(기본)|
|REPEATABLE READ|없음|없음|발생 <br/>(innoDB는 없음)|innoDB(기본)|
|SERIALIZABLE|없음|없음|없음|거의 사용하지 않음|


`SQL-92` 또는 `SQL-99` 표준에 따르면 

- REPEATABLE READ 격리 수준에서는 PHANTOM READ가 발생할 수 있지만, InnoDB에서는 독특한 특성 때문에 REPEATABLE READ 격리 수준에서도 PHANTOM READ가 발생하지 않는다. 

- 일반적인 온라인 서비스 용도의 데이터베이스는 READ COMMITED와 REPEATABLE READ 중 하나를 사용한다. 

- 오라클 같은 DBMS에서는 주로 READ COMMITED 수준을 많이 사용하며, MySQL에서는 REPEATABLE READ를 주로 사용한다.

<br/>

### **🍳 5.4.1 READ UNCOMMITED**

어떤 트랜잭션의 작업이 완료되지 않았는데도, 다른 트랜잭션에서 볼 수 있는 데이터 불일치 현상

READ UNCOMMITED 격리 수준에서는 아래 이미지와 같이 각 트랜잭션의 변경 내용이 COMMIT이나 ROLLBACK 여부와 상관없이 다른 트랜잭션에서 보인다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/71bdf323-e739-495b-8450-d54e0730f6f9)

- 사용자 A는 emp_no가 500000이고 first_name이 "Lara"인 새로운 사원을 INSER한다.

- 사용자 B가 emp_no=500000인 사원을 검색하는데, 사용자 A가 INSERT한 사원의 정보를 커밋되지않은 상태에서도 조회할 수 있다.

여기서 문제는 사용자 A가 처리 도중 알 수 없는 문제가 발생해 INSERT된 내용을 롤백한다고 하더라도 사용자 B는 "Lara"가 정상적인 사원이라고 생각하고 계속 처리할 것이라는 점이다.


**<span style="color:#3366ff">어떤 트랜잭션에서 처리한 작업이 완료되지 않았는데도 다른 트랜잭션에서 볼 수 있는 현상</span>을  <span style="color:#ff6600">더티 리드(DIRTY READ)</span>**라 하고, 더티 리드가 허용되는 격리 수준이 `READ UNCOMMITED`이다. 

더티 리드 현상은 데이터가 나타났다가 사라졌다 하는 현상을 초래하므로 애플리케이션 개발자와 사용자를 상당히 혼란스럽게 만들 것이다.

더티 리드를 유발하는 READ UNCOMMITED는 RDBMS 표준에서는 트랜잭션의 격리 수준으로 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준이므로, MySQL을 사용한다면 최소한 READ COMMITED 이상의 격리 수준을 사용할 것을 권장한다.

<br/>

### **🍳 5.4.2 READ COMMITED**

`READ COMMIED`는  **<span style="color:#ff6600">오라클 DBMS에서 기본으로 사용되는 격리 수준</span>**

온라인 서비스에서  **<span style="color:#ff6600">가장 많이 선택되는 격리 수준</span>**

이 레벨에서는 위에서 언급한  **<span style="color:#3366ff">더티 리드같은 현상은 발생하지 않는다.</span>** 

어떤 트랜잭션에서 데이터를 변경했더라도 COMMIT이 완료된 데이터만 다른 트랜잭션에서 조회할 수 있기 때문이다.

#### **READ COMMITED 동작 방식**

아래 이미지는 READ COMMITED 격리 수준에서 사용자 A가 변경한 내용이 사용자 B에게 어떻게 조회되는지 보여준다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/801210fb-80fc-4d8c-bbc4-278835b76f23)

사용자 A가 트랜잭션을 시작하여 데이터를 변경하였고, 아직 커밋은 하지 않은 상태다.

그러면 테이블은 먼저 갱신되고, 언두 로그로 변경 전의 데이터가 백업된다.

이때 사용자 B가 데이터를 조회하려고 하면, READ COMMITTED에서는 커밋된 데이터만 조회할 수 있으므로, 언두 로그에서 변경 전의 데이터를 찾아서 반환한다.

최종적으로 사용자 A가 트랜잭션을 커밋하면 그때부터 다른 트랜잭션에서도 새롭게 변경된 값을 참조할 수 있게 된다.

#### **NON-REPLEATABLE READ 문제**

하나의 트랜잭션 안에서 똑같은 SELECT 쿼리로 데이터를 여러번 요청했을 때, 항상 같은 결과를 가져오지 못하는 데이터 불일치 현상

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/00ba8cca-1255-4f4d-a3a2-5e6446c54580)

중간에 다른 트랜잭션에서 커밋한 데이터 때문에 한 트랜잭션 안에서 같은 SELECT문의 실행 결과가 달라지는 문제가 발생

이는 별다른 문제가 없어 보이지만, 사실 사용자 B가 하나의 트랜잭션 내에서 똑같은 SELECT 쿼리를 실행했을 때는 항상 같은 결과를 가져와야 한다는 `REPEATABLE READ 정합성`에 어긋나는 것이다.

- 일반적으로는 크게 문제가 되지 않을 수 있지만, 금전적인 내용을 다루는 경우 주의해야 한다.

  - 오늘 입금된 금액의 총합을 조회하는 상황에서 NON-REPEATABLE READ가 발생할 경우,
총합을 계산하는 SELECT 쿼리가 실행될 때 마다 다른 결과를 가져올 수 있기 때문

<br/>

### **🍳 5.4.3 REPEATABLE READ**

- InnoDB 스토리지 엔진에서 기본으로 사용되는 격리 수준

바이너리 로그를 가진 MySQL 서버에서는 최소 REPEATABLE READ 격리 수준 이상을 사용해야 한다.

이 격리 수준에서는 READ COMMITED 격리 수준에서 발생하는 NON-REPEATABLE READ 부정합이 발생하지 않는다. 

**<span style="color:#ff6600">InnoDB 스토리지 엔진은 트랜잭션이 ROLLBACK될 가능성에 대비해 변경 전 레코드를 언두(Undo) 공간에 백업해두고 실제 레코드 값을 변경</span>**하는데, 이러한 변경 방식을 **<span style="color:#ff6600">MVCC</span>**라고 한다.


REPEATABLE READ는 이 MVCC를 위해 언두 영역에 백업된 이전 데이터를 이용해 동일 트랜잭션 내에서는 **<span style="color:#ff6600">동일한 결과를 보여줄 수 있게 보장</span>**한다. 

사실 READ COMMITED도 MVCC를 이용해 COMMIT되기 전의 데이터를 보여주는데, 이 둘의 차이는 언두 영역에 백업된 레코드의 여러 버전 가운데 몇 번째 이전 버전까지 찾아 들어가야 하느냐에 있다.


모든 InnoDB의 트랜잭션은 **<span style="color:#ff6600">고유한 트랜잭션 번호(순사적으로 증가하는 값)</span>**을 가진다.

언두 영역에 백업된 모든 레코드에는 변경을 발생시킨 트랜잭션의 번호가 포함돼 있다. 

언두 영역의 백업된 데이터는 InnoDB 스토리지 엔진이 불필요하다고 판단되는 시점에 **<span style="color:#ff6600">주기적으로 삭제</span>**한다. 

REPEATABLE READ 격리 수준에서는 MVCC를 보장하기 위해 실행 중인 트랜잭션 가운데 가장 오래된 트랜잭션 번호보다 트랜잭션 번호가 앞선 언두 영역의 데이터는 삭제할 수 없다.

가장 오래된 트랜잭션 번호 이전의 트랜잭션에 의해 변경된 모든 언두 데이터가 필요한 것은 아니다. 

더 정확하게는 특정 트랜잭션 번호의 구간 내에서 백업된 언두 데이터가 보존돼야 한다.


#### **REPEATABLE READ 격리 수준 작동 방식**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ec7632dc-5c69-4302-9429-b2d79906727f)

- 사용자 A의 트랜잭션 번호는 12이며, 사용자 B의 트랜잭션 번호는 10이다.
- 사용자 A가 first_name을 "Toto"로 변경하고 커밋을 수행한다.
- 사용자 B가 emp_no=500000인 사원을 A 트랜잭션의 변경 전후 각각 한 번씩 SELECT했는데 결과는 항상 "Lara"라는 값을 가져온다.

사용자 B가 BEGIN 명령으로 트랜잭션을 시작하면서 10번이라는 트랜잭션 번호를 부여받았는데, 그때부터 사용자 B는 10번 트랜잭션 안에서 실행되는 모든 SELECT 쿼리는 트랜잭션 번호가 10번(자신의 트랜잭션 번호)보다 작은 트랜잭션 번호에서 변경한 것만 보게 된다.

또한, REPEATABLE READ 격리 수준에서도 아래와 같은 부정합이 발생할 수 있다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/161b9b84-e3d9-4238-86df-ac7df497f96e)

사용자 B는 BEGIN 명령으로 트랜잭션을 시작한 후 SELECT을 수행하기 때문에 두 번의 SELECT 쿼리 결과는 똑같아야 한다. 

하지만 사용자 B가 실행하는 두 번의 `SELECT ... FOR UPDATE` 쿼리 결과는 서로 다르다.

다른 트랜잭션에서 수행한 변경 작업에 의해 레코드가 보였다 안 보였다 하는 현상은 <span style="color:#ff6600">팬텀 리드(PHANTOM READ)</span>라고 한다. 

`SELECT ... FOR UPDATE` 쿼리는 SELECT하는 레코드에 쓰기 잠금을 걸어야 하는데, 언두 레코드에는 잠금을 걸 수 없다. 

`SELECT ... FOR UPDATE`나 `SELECT ... LOCK IN SHARE MODE`로 조회되는 레코드는 언두 영역의 변경 전 데이터를 가져오는 것이 아니라 현재 레코드의 값을 가져오게 되는 것이다.

<br/>

### **🍳 5.4.4 SELIALIZABLE**

**<span style="color:#ff6600">가장 단순한 격리 수준</span>**이면서 동시에 **<span style="color:#ff6600">가장 엄격한 격리 수준</span>**으로, 그만큼 **<span style="color:#ff6600">동시 처리 성능도 다른 트랜잭션 격리 수준보다 떨어진다.</span>**


InnoDB 테이블에서 기본적으로 순수한 SELECT 작업은 아무런 레코드 잠금도 설정하지 않고 실행된다. 

하지만 트랜잭션의 격리 수준이 SERIALIZABLE로 설정되면 읽기 작업도 공유 잠금(읽기 잠금)을 획득해야만 하며, 동시에 다른 트랜잭션은 그러한 레코드를 변경하지 못하게 된다. 

즉, 한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없다는 것이다.

SERIALIZABLE 격리 수준에서는 일반적인 DBMS에서 일어나는 "PHANTOM READ"라는 문제가 발생하지 않지만, InnoDB 스토리지 엔진에서는 갭 락과 넥스트 키 락 덕분에 REPEATABLE READ 격리 수준에서도 이미 "PHANTOM READ가 발생하지 않기 때문에" 굳이 SERIALIZABLE을 사용할 필요성은 없어 보인다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> 백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드 1 / 백은빈, 이성욱 지음. (2021). Web.
{:.prompt-info}

>백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드. 2 / 백은빈, 이성욱 지음 (2021). Web.
{:.prompt-info}
