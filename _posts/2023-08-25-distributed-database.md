---
layout: post
title: 분산데이터베이스 (Distributed Database)
author: admin
date: 2023-08-25 20:10:00 +900
lastmod: 2023-08-25 20:10:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [DATABASE(DB), SQLD]  # 대문자로 작성
tags: [sql, sqld, distributed-database] # 소문자로 작성
---


> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# **🌈 분산데이터베이스 (Distributed Database)**

## **💻 분산 데이터 베이스**

- 데이터베이스 시스템 구축 시에 **<span style="color:tomato">한 대의 물리적 시스템에 데이터베이스 관리 시스템을 설치하고 여러 명의 사용자가 데이터베이스 관리 시스템에 접속하여 데이터베이스를 사용하는 구조를 중앙 집중형 데이터베이스</span>**라고 한다.

- 또한 **<span style="color:tomato">물리적으로 떨어진 데이터베이스에 네트워크로 연결하여 단일 데이터베이스 이미지를 보여 주고 분산된 작업 처리를 수행하는 데이터베이스를 분산 데이터베이스</span>**라고 한다.

- 분산 데이터베이를 사용하는 고객은 시스템이 네트워크로 분산되어 있는지의 여부를 인식하지 못하면서, 자신만의 데이터베이스를 사용하는 것처럼 사용할 수 있다. 이처럼 데이터베이스는 **<span style="color:tomato">투명성을 제공해야 한다.</span>**

- 투명성은 분산 데이터베이스에서 중요한 요소이며 투명성의 종류에는 분할, 위치, 지역사상, 중복, 장애 및 병행 투명성이 있다.

<br/>

> **사상이란?**
> 
> 사상이라는 것은 매핑과 유사한 개념이라고 생각하면 된다. 
> 
> 특정 스키마가 바뀌더라도 매핑을 다시 함으로써 다른 계층의 스키마의 내용은 변하지 않아도 되는 것이다. <br/>
{:.prompt-info}

<br/>

### **분산 데이터베이스의 투명성 종류**

| 투명성 | 설명 |
| :---: | :--- |
| 분할 투명성 | - 고객은 하나의 논리적 릴레이션이 여러 단편으로 분할되어 <br/>각 단편의 사본이 여러 시스템에 저장되어 있음을 인식할 필요가 없다. |
| 위치 투명성 | - 고객이 사용하려는 데이터의 저장 장소를 명시할 필요가 없다. <br/>- 고객은 데이터가 어느 위치에 있더라도 동일한 명령을 사용하여<br/> 데이터에 접근할 수 있어야 한다. |
| 지역 사상 투명성 | - 지역 DBMS와 물적 데이터베이스 사이의 사상(매핑)이 보장됨에 따라 <br/>각 지역 시스템 이름과 무관한 이름이 사용 가능하다. |
| 중복 투명성 | - 데이터베이스 객체가 여러 시스템에 중복되어 존재함에도 <br/>고객과는 무관하게 데이터의 일관성이 유지된다. |
| 장애 투명성 | - 데이터베이스가 분산되어 있는 각 지역의 시스템이나 통신망에 <br/>이상이 발생해도, 데이터의 무결성은 보장된다. |
| 병행 투명성 | - 여러 고객의 응용 프로그램이 동시에 분산 데이터베이스에 대한<br/> 트랜잭션을 수행하는 경우에도 결과에 이상이 없다. |

<br/>

## **💻 분산 데이터 베이스 설계 방식**

분산 데이터베이스의 구축 방법에는 상향식 설계 방식과 하향식 설계 방식 두 가지가 있다. 

### **상향식 설계 방식** 

지역 스키마 작성 후 향후 전역 스키마를 작성하여 분산 데이터베이스를 구축한다.

<br/>

### **하향식 설계 방식**

전역 스키마 작성 후 해당 지역 사상 스키마를 작성하여 분산 데이터베이스를 구축한다.


![분산 데이터 베이스 설계 방식  image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6f56da71-f368-4c11-9582-0b1a9e8c4e1a){: width="500" height="500" }

<br/>

>분산데이터를 하향식 접근 방식으로 구축한다는 것은 기업 전체의 전사 데이터모델을 수렴하여 전역 스키마를 생성하고, 그 다음 각 지역별로 지역 스키마를 생성하여 분산 데이터베이스를 구축하는 것이다. 상향식 접근 방식은 지역별로 데이터베이스를 구축한 후에 전역 스키마로 통합하는 것이다.
{:.prompt-info}

>분산 데이터베이스를 구축하거나 운영할 때 동일한 데이터베이스 관리 시크템으로 분산 데이터베이스를 구축하는 것은 크게 어렵지 않다. 하지만 기업에 여러 종류의 데이터베이스 관리 시스템이 있으면 이기종 데이터베이스 관리 시스템으로 연동해야 한다. 이기종 데이터베이스 시스템으로 연동하기 위해서는 데이터베이스 미들웨어(ODBC, JDBC)를 사용해야 한다.
{:.prompt-info}

<br/>

## **💻 분산 데이터 베이스의 장점과 단점**

### **장점**

**<span style="color:tomato">신뢰성과 가용성</span>이 높다.**

- 분산 데이터베이스는 여러 시스템에 데이터를 분산하여 저장하므로,

- 하나의 시스템에 장애가 발생해도 다른 시스템에서 데이터를 제공할 수 있어 **`신뢰성과 가용성이 높다.`** 

**<span style="color:tomato">빠른 응답 속도</span>** 

- 병렬 처리를 통해 분산 데이터베이스는 빠른 응답 속도를 제공할 수 있다.

-  여러 시스템에서 동시에 작업을 처리하기 때문에 처리 성능을 향상시킬 수 있다. 

**<span style="color:tomato">용량 확장 용이성</span>**

- 새로운 시스템을 추가하여 분산 데이터베이스를 확장하는 것은 비교적 간단하다. 

- 즉, **`시스템 용량을 필요에 따라 쉽게 조정`**할 수 있다.

<br/>

### **단점**

**<span style="color:tomato">관리와 통제</span>의 어려움**

- 분산 데이터베이스는 여러 네트워크로 구성되어 있기 때문에 관리와 통제가 복잡해질 수 있다. 

- 다양한 시스템 간의 일관된 관리 및 모니터링을 위한 체계적인 접근 방식이 필요하다.

**<span style="color:tomato">보안 관리</span>의 어려움** 

- 분산된 환경에서 보안 관리는 복잡하다. 

- 다양한 시스템 간의 접근 제어, 사용자 인증 및 권한 부여 등 보안 요구사항을 충족하기 위해서는 체계적인 보안 전략과 메커니즘이 필요하다.

**<span style="color:tomato">데이터 무결성 관리</span>의 어려움**

- 여러 개의 동일한 데이터 사본들 사이의 일관성 유지와 갱신 문제 등으로 인해 데이터 무결성 관리가 어려울 수 있다. 

- 동기화 문제나 중복 업데이트 등으로 인한 일관성 유지가 중요하다.

<br/>

## 🎯 **기출 내용 추가**

### ❌

#### 👉

<br/>

# **📚 레퍼런스**

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
{:.prompt-info }


> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
{:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.1 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-1/dashboard){:target="_blank"}
{:.prompt-info }