---
layout: post
title: Real MySQL 04. 아키텍처 - 3. MyISAM 스토리지 엔진 아키텍처
author: admin
date: 2023-09-20 00:00:00 +900
lastmod: 2023-09-20 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [MY SQL, REAL MYSQL 1]  # 대문자로 작성
tags: [database, mysql] # 소문자로 작성
---

> 해당 포스트는 `MySQL`를 학습하며 필요한 내용을 정리한 포스트입니다.<br/>
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 4. 아키텍처**

## **💻 4.3 MyISAM 스토리지 엔진 아키텍처**

MyISAM 스토리지 엔진의 성능에 영향을 미치는 요소인 키 캐시와 운영체제의 캐시/버퍼 

![MyISAM 스토리지 엔진 구조](https://github.com/leekoby/leekoby.github.io/assets/118284808/d86665c4-da16-4e87-a763-179ae17a4f59)

### **🍳 4.3.1 키 캐시**

InnoDB의 버퍼 풀과 비슷한 역할을 하는 MyISAM의 키 캐시(Key Cache, 키 버퍼라고도 불림)

키 캐시는 인덱스만을 대상으로 한다. 

또한 인덱스의 디스크 쓰기 작업에 대해서만 부분적으로 버퍼링 역할을 한다.

키 캐시의 효율을 구하는 수식 

```
키 캐시 히트율(Hit Rate) = 100 - (Key_reads / Key_read_quests * 100)
```

- Key_reads 는 인덱스를 디스크에서 읽어들인 횟수를 저장하는 상태 변수 
- Key_read_quests는 키 캐시로부터 인덱스를 읽은 횟수를 저장하는 상태 변수 

<br/>

### **🍳 4.3.2 운영체제의 캐시 및 버퍼**

MyISAM 테이블의 인덱스는 키 캐시를 이용해 디스크를 검색하지 않고도 충분히 빠르게 검색할 수 있다.

MyISAM은 디스크로의 I/O를 해결할 캐시나 버퍼링 기능을 가지고 있지 않기 때문에 빈번한 디스크 I/O 호출을 발생시킬 수 있다. 

따라서 MySQL 의 최대 물리 메모리를 40% 이상 넘지 않도록 설정하고, 나머지 메모리 공간을 운영체제가 사용하여 운영체제의 캐시 공간을 마련하는 것이 좋다.

<br/>

### **🍳 4.3.3 데이터 파일과 프라이머리 키(인덱스) 구조**

InnoDB 스토리지 엔진을 사용하는 테이블은 프라이머리 키에 의해서 클러스터링되어 저장되는 반면, MyISAM 테이블은 프라이머리 키에 의한 클러스터링 없이, INSERT되는 순서대로 데이터 파일에 저장되어 힙(Heap) 공간처럼 활용된다. 

따라서 저장되는 레코드는 모두 ROWID라는 물리적 주솟값을 가지며 모든 인덱스들은 이 값을 포인터로 가진다.

MyISAM 테이블에서 ROWID는 가변길이와 고정 길이 두 가지 방법으로 저장될 수 있다. 


- 가변길이 
  
  - 자주 사용되지는 않지만 테이블을 생성할 때 MAX_ROWS 옵션을 사용할 수 있는데, 이 옵션을 명시하면 최대로 가질 수 있는 레코드가 한정된 테이블을 생성
  - MAX_ROWS 옵션에 의해 레코드의 개수가 한정되면 ROWID 값으로 4바이트 정수를 사용 
  - 이 때 레코드가 INSERT 된 순번이 ROWID로 사용 

- 고정길이 
  
  - MAX_ROWS 옵션을 설정하지 않으면 `myisam_data_pointer_size` 시스템 변수에 저장된 바이트 수만큼의 공간을 사용 
  - `myisam_data_pointer_size` 시스템 변수의 기본값은 7이므로 ROWID는 2바이트부터 7바이트까지 가변적인 ROWID를 갖게 된다. 
  - 첫 번째 바이트는 ROWID의 길이를 저장하고 나머지 공간은 실제 ROWID를 저장
  - 가변적인 ROWID를 가지면 데이터 파일에서 레코드 위치(offset)가 ROWID로 사용
<br/>

---

<br/>

## 📚 **레퍼런스**

> 백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드 1 / 백은빈, 이성욱 지음. (2021). Web.
{:.prompt-info}

>백은빈, and 이성욱. Real MySQL 8.0 : 개발자와 DBA를 위한 MySQL 실전 가이드. 2 / 백은빈, 이성욱 지음 (2021). Web.
{:.prompt-info}
