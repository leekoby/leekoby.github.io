---
layout: post
title: Part1 Chapter 1-5 식별자 (Identifiers)
author: admin
date: 2023-08-24 18:10:00 +900
lastmod: 2023-08-24 18:10:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [DATABASE(DB), SQLD]
tags: [sql, sqld, data modeiling]
---


> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.<br />
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.<br />
{:.prompt-tip}

<br />

# 🌈 5. 식별자 (Identifiers)

## 💻 5.1 식별자란? <br /> 

- 모든 엔터티는 인스턴스를 가지고 있고 인스턴스는 속성으로 자신의 특성을 나타낸다. 

- 식별자는 이런 속성 중에 각각의 인스턴스를 구분 가능하게 만들어주는 대표 격인 속성을 의미

- <span style="color:tomato">식별자라는 것은 엔터티를 대표할 수 있는 `유일성`을 만족하는 속성</span>이다

- 일반적으로 회원ID, 계좌번호, 주민등록번호, 외국인 등록번호, 여권 번호 등이 있다.

<br />

## 💻 5.2 주식별자 <br /> 

- 주식별자는 기본키, PK (Primary Key)에 해당하는 속성이다.

- 하나의 속성이 주식별자가 될 수도 있고 여러 개의 속성이 주식별자가 될 수도 있다.

### **주식별자의 특성**

||| 
| :--: | :-- |  
|유일성|- 각 인스턴스에 유니크함을 부여하여 식별이 가능하도록 한다. <br/> - 주식별자는 엔터티의 인스턴스를 유일하게 식별한다.|
|최소성|- 유일성을 보장하는 최소 개수의 속성이어야 한다. <br/> - 주식별자는 최소성을 만족하는 키이다.|
|불변성|- 속성값이 되도록 변하지 않아야 한다. <br/> 주식별자는 자주 변경되지 않아야 한다.|
|존재성(`Not Null`)|- 속성값이 NULL일 수 없다.|
|대표성|- 주식별자는 엔터티를 대표할 수 있어야 한다.|

<br/>

### **키의 종류**

| 데이터베이스 키 | 설명 | 
| :-- | :-- |  
|기본키(Primary Key)|- 후보키 중에서 `엔터티를 대표`할 수 있는 키이다.|
|후보키(Candidate Key)|- 후보키는 `유일성`과 `최소성`을 만족하는 키이다. 불변성[x]|
|슈퍼키(Super Key)|슈퍼키는 `유일성은 만족`하지만 `최소성을 만족하지 않는 키`이다.|
|대체키(Alternate Key)|- 대체키는 여러 개의 후보키 중에서 `기본키를 선정하고 남은 키`이다.|
|외래키(Foreign Key)|- 하나 혹은 다수의 다른 테이블의 기본키 필드를 가리키는 것으로 <br/><span style="color:tomato">참조 무결성을 확인하기 위해서 사용</span>되는 키이다.<br/> - 즉, `허용된 데이터` 값만 데이터베이스에 `저장하기 위해`서 사용된다.|


<br />

## 💻 5.3 분류 <br /> 

- 식별자를 분류하는 방식은 여러가지로 나뉜다. <br/> 

- 대체적으로 대표성 여부, 스스로 생성되었는지 여부, 단일 속성의 여부, 대체 여부가 분류의 기준이 된다. <br/>

![식별자의 종류 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/295905dc-f95d-4233-a29d-35a64dd3194c){: width="800" height="400" } 

<br/>



### 🔍 5.3.1 대표성 여부 

- <span style="color:tomato">주식별자는 엔터티를 대표할 수 있는 식별자</span>이다.

- 예를 들어 회원 ID는 고객 엔터티의 주식별자가 된다. 

- 주식별자는 다른 엔터티와 참조관계로 연결될 수 있다. 

- <span style="color:tomato">보조 식별자는 `유일성과 최소성은 만족`하지만 `대표성을 만족하지 못한다.`</span>

|||
|:-:|:-|
|주식별자 (Primary Identifier)|- 유일성, 최소성, 불변성, 존재성을 가진 대표 식별자 <br/> - 다른 엔터티와 `참조 관계`로 연결|
|보조식별자 (Alternate Identifier)|- 인스턴스를 식별할 수는 있지만 대표 식별자가 아님 <br/> - 다른 엔터티와 `참조 관계를 가지지 못함` |

<br/>

### 🔍 5.3.2 스스로 생성되었는지 여부 

- 생성 여부에 따른 식별자의 종류에는 `내부 식별자`와 `외부 식별자`로 나뉜다.

- <span style="color:tomato">내부 식별자는 엔터티 내부에서 스스로 생성되는 식별자</span><br/> 예를 들면 `부서코드`, `종목 코드` 등을 말한다. 

- <span style="color:tomato">외부 식별자는 다른 엔터티와의 관계로 만들어지는 식별자</span><br/> 예를 들면 계좌 엔터티에 `회원ID`

|||
|:-:|:-|
|내부식별자 (Internal Identifier)|- 엔터티 내부에서 스스로 생성된 식별자|
|외부식별자 (Foreign Identifier)|- 다른 엔터티에서 온 식별자. 다른 엔터티와의 `연결고리 역할`|

<br/>

### 🔍 5.3.3 단일 속성의 여부 (속성의 수에 따라) 

- 속성의 수에 따른 식별자의 종류에는 `단일 식별자`, `복합 식별자`가 있다. 



|||
|:-:|:-|
|단일식별자(Single Identifier)|- <span style="color:tomato"> 하나의 속성으로 구성된 식별자</span><br/> e.g.) 고객 엔터티의 회원 ID |
|복합식별자(Composite Identifier)|- <span style="color:tomato"> 두 개 이상의 속성으로 구성된 식별자</span>|  

<br/>

### 🔍 5.3.4 대체 여부 

|||
|:-|:-|
|원조식별자(Original Identifier)(본질식별자)| - <span style="color:tomato">비지니스 프로세스에서 만들어지는 식별자</span><br/>- 가공되지 않은 원래의 식별자|
|대리식별자(Surrogate Identifier)(`인조식별자`)|- <span style="color:tomato">인위적으로 만들어지는 식별자</span><br/>- <span style="color:tomato">하나의 인조 속성을 다른 것으로 대체할 수 없다.</span><br/>- <span style="color:tomato">최대한 범용적인 값을 사용해서 유일한 값을 만들기 위한 것이다.</span><br/>- 주식별자의 속성이 두 개 이상인 경우 그 속성들을 하나로 묶어서 사용하는 식별자<br/>- 후보 식별자 중에서 주식별자로 선정할 것이 없거나 주식별자가 너무 많은 컬럼으로 되어있는 경우에 사용 그 예는 순서 번호를 사용하여 식별자를 만드는 것|

<br/>



>**<span style="color:#3366ff">대체 여부</span>에 따라  <span style="color:#ff6600">본질식별자/인조식별자</span>**<br/>
>**<span style="color:#3366ff">대표성</span>에 따라  <span style="color:#ff6600">주식별자/보조식별자</span>**<br/>
>**<span style="color:#3366ff">속성수</span>에 따라  <span style="color:#ff6600">단일식별자/복합식별자</span>**<br/>
{:.prompt-tip}

<br/>

## 💻 5.4 식별자 관계 vs. 비식별자 관계 <br /> 

### 🔍 5.4.1 식별자 관계 (Identification Relationship) <br />

-  부모  엔터티의 식별자가 자식 엔터티의 `주식별자`가 되는 관계 <br/>

- 주식별자는 반드시 존재해야 하므로(존재성) 부모 엔터티가 있어야 생성이 가능하며 <br/>`단일식별자`인지 `복합식별자`인지에 따라 `1:1` 이거나 `1:M`으로 결정된다. <br/>

- 자식 엔터티의 데이터가 부모 엔터티에 반드시 존재해야 한다. <br/>

- 식별관계는 `실선`으로 표현된다<br/>

<br/>

> **<span style="color:#ff6600">식별관계</span>** <br/>
> - 고객과 계좌 엔터티에서 고객은 독립적으로 존재할 수 있는 <span style="color:tomato">강한 개체(Strong Entity)</span>이다.<br/>
> - 강한 개체는 어떤 다른 엔터티에게 의존하지 않고 독립적으로 존재한다.<br/>
> - 강한 개체는 다른 엔터티와 관계를 가질 때 <span style="color:tomato">다른 엔터티에게 `기본키를 공유`한다.</span><br/>
> - <span style="color:tomato">외래키가 존재</span>한다.
> - 강한 개체는 식별 관계로 표현된다. <br/>
> - 즉, <span style="color:tomato">식별관계란 고객 엔터티의 기본키인 회원ID를 계좌 엔터티의 기본키의 하나로 공유하는 것</span>이다.<br/>
> 
> ![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ccecb3da-050e-4658-9881-341d5c6f9395){: width="500" height="300" } <br/>
> - 강한 개체의 기본키 값이 변경되면 식별관계(기본키를 공유 받은)에 있는 엔터티의 값도 변경된다. <br/>
> 여기서 계좌 엔터티는 <span style="color:tomato">약한 개체(Week Entity)</span>가 된다.<br/>
{:.prompt-info}


<br/>

### 🔍 5.4.2 비식별자 관계 (Non-Identification Relationship) <br />

- 부모 엔터티의 식별자가 자식 엔터티의 주식별자가 아닌 `일반 속성`이 되는 관계 <br/>

- 일반 속성의 속성값은 NULL이 될 수 있음로 부모 엔터티가 없는 자식 엔터티 생성이 가능 <br/>

- 같은 이유로 자식 엔터티가 존재하는 상태에서 부모 엔터티가 삭제될 수도 있다. <br/>

- 식별관계는 `점선`으로 표현된다 <br/>


<br/>

> **<span style="color:#ff6600">비식별관계</span>**<br/>
> - <span style="color:#ff6600">약한 연결관계를 표현하고, 약한 종속관계라고도 한다.</span>
> - <span style="color:tomato">비식별 관계는 강한 개체의 기본키를 다른 엔터티의 기본키가 아닌 일반 칼럼으로 관계를 가지는 것</span>이다.<br/>
> - 예를 들어 관리점 엔터티의 기본키는 지점 코드이고 고객 엔터티와 비식별 관계를 가지고 있다. <br/>
>  즉, 지점 코드는 고객 엔터티의 기본키가 아닌 일반 칼럼으로 참조된다. 비식별 관계는 점선으로 표현한다.<br/>
> ![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/956735ae-f609-486f-ac65-9a554e0cd17d){: width="500" height="300" } <br/>
{:.prompt-info}

<br/>

> **<span style="color:#ff6600">강한 개체 (Strong Entity)와 약한 개체 (Week Entity)</span>** <br/>
> - <span style="color:tomato">강한 개체는 누구에게도 지배되지 않는 독립적인 개체(Entity)이다.</span><br/>
> 
> - <span style="color:tomato">약한 개체는 개체의 존재가 다른 개체의 존재에 종속되어 있는 개체(Entity)이다.</span><br/>
{:.prompt-info}


<br />


# 🎯 **기출 내용 추가**

## ❌

👉

<br/>


# 📚 레퍼런스

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
{:.prompt-info }


> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
{:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.1 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-1/dashboard){:target="_blank"}
{:.prompt-info }

