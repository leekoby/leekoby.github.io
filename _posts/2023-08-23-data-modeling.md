---
layout: post
title: 데이터 모델링의 이해
author: admin
date: 2023-08-23 03:00:00 +900
lastmod: 2023-08-25 22:20:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]
tags: [sql, sqld, data modeiling]
---

> **해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.**<br />
{:.prompt-info}

<br />

> **이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.**<br />
{:.prompt-tip}

<br />

# 🌈 **데이터 모델의 이해**

## 💻 **모델링이란?**

> **데이터베이스의 모델링은 '현실 세계를 단순화하여 표현하는 기법**<br />
{:.prompt-tip}

> **<span style="color:#3366ff">현실 세계</span> 데이터베이스로 표현하기 위해서 <span style="color:#ff6600">추상화</span>한다.**

>**<span style="color:#3366ff">고객의 업무프로세스</span>를 <span style="color:#ff6600">이해해야 한다.</span>**

>**<span style="color:#3366ff">업무 프로세스를 이해한 후</span> <span style="color:#ff6600">데이터 모델링 표기법을 사용해서 모델링</span>한다.**

>**<span style="color:#3366ff">쉽게 이해할 수 있도록</span> <span style="color:#ff6600">복잡하지 않게 모델링</span>**

>**고객의 <span style="color:#3366ff">업무 프로세스를 추상화하고, 소프트웨어를 분석, 설계</span> 하면서 점점 더 <span style="color:#ff6600">상세 해진다</span>**

>**<span style="color:#3366ff">객의 비즈니스 프로세스를 이해하고</span> <span style="color:#ff6600">비즈니스 프로세스의 규칙을 정의</span>**

>**<span style="color:#3366ff">정의된 비즈니스규칙</span>을  <span style="color:#ff6600">데이터 모델로 표현</span>**

<br/>

> **<span style="color:#3366ff">모델링이 갖춰야할 조건</span>**<br />
> 1. <span style="color:#ff6600">현실세계를 반영</span>해야 한다.<br />
> 2. <span style="color:#ff6600">단순화</span>하여 표현해야 한다.<br />
> 3. 관리하고자 하는 데이터를 모델로 설계한다. <br />
{:.prompt-tip}

<br />




> **<span style="color:#3366ff">데이터 모델링시에 고려사항 </span>**<br/>
> - <span style="color:#ff6600">데이터 중복</span><br/>
> - <span style="color:#ff6600">비유연성</span><br/>
> - <span style="color:#ff6600">비일관성</span> <br/>
{:.prompt-info}

<br />

## 💻 **모델링의 특징**

<br />

**1. 추상화(Abstraction)** 

- 현실 세계를 일정한 형식으로 표현하는 것

- 공통적인 특징을 찾고 간략하게 표현

**2. 단순화(Simplification)**

- 복잡한 현실 세계를 정해진 표기법으로 단순하고 쉽게 표현하는 것

**3. 명확화(Clarity)**

- 불분명함을 제거하고 명확하게 해석할 수 있도록 기술하는 것

<br />

> **데이터베이스의 모델링은 '<span style="color:#ff6600">현실세계를 추상화, 단순화, 명확화하기 위해 일정한 표기법에 의해 표현</span>하는 기법'**<br />
{:.prompt-tip}

<br />

>**<span style="color:tomato">좋은 모델링의 요건</span>**<br/>
> - **중복배제**<br/>
> - **Business Rule**<br/>
> - **완전성**<br/>
{:.prompt-tip}

<br/>

>**<span style="color:tomato">카디널리티(Cardinality)</span>**
>
>**하나의 릴레이션에서 튜플(레코드, 행)의 전체 개수를 의미**한다.<br/>
>두 개의 엔터티 간 관계에서 참여자의 수를 표현하는 일반적인 표현 방법으로 `1:1`, `1:M`, `M:N` 방식이 있다.
>
>- "카디널리티(Cardinality)"는 데이터베이스 설계와 모델링에서 중요한 개념으로, 특정 데이터 세트의 고유한 요소의 수를 나타낸다.<br/>
>- 예를 들어, "성별"이라는 열에 '남성', '여성' 두 가지 값만 존재한다면, 이 열의 카디널리티는 2이다. 반면에 "사용자 ID"와 같은 열은 각 사용자에 대해 고유한 값을 가지므로, 이러한 열의 카디널리티는 해당 테이블의 전체 행 수와 같을 것이다.
> - 또한, 관계형 데이터베이스에서 "카디널리티"는 종종 두 테이블 사이의 관계를 설명하는 데 사용된다. <br/>
> - 예를 들어, 한 사람이 여러 개의 전화 번호를 가질 수 있고 하나의 전화 번호가 한 사람에게만 속할 수 있는 경우, 이 관계는 "일대다(One-to-Many)" 카디널리티를 가진다고 할 수 있다.
{:.prompt-tip}

<br/>

>**<span style="color:tomato">선택도</span>**<br/>
>**특정 조건에 의해서 선택될 것으로 예상되는 레코드의 비율을 의미**하며 조건절에서 요청한 값의 범위/전체값으로 계산되어진다. <br/>
> - "선택도(Selectivity)"는 데이터베이스 쿼리 최적화에 사용되는 개념으로, 특정 조건에 맞는 행들이 전체 행 중에서 차지하는 비율을 뜻한다. <br/> 
> - 즉, 특정 쿼리가 얼마나 '선택적'인지, 즉 테이블의 행들 중 몇 %를 선택하는지를 나타내는 지표다.<br/>
> - 예를 들어, "성별"이라는 열에서 '여성'인 행이 전체 행의 50%를 차지한다면, '성별이 여성인' 조건의 선택도는 0.5(50%)가 된다. 
> - 이와 반대로 "사용자 ID"와 같은 칼럼이 있다면, 각 사용자 ID는 고유하므로 특정 사용자 ID를 선택하는 쿼리의 선택도는 매우 낮을 것이다.<br/>
{:.prompt-tip}

<br />


## 💻 **모델링의 세 가지 관점**

**1. 데이터 관점 (What, Data)** <br />
`데이터 위주`의 모델링<br />
어떤 데이터들이 업무와 관련이 있는지, 그 데이터들 간에는 어떤 관계가 있는지에 대해 모델링하는 방법<br />

**2. 프로세스 관점 (How, Process)**<br />
`프로세스위주`의 모델링 <br />
이 업무가 실제로 처리하고 있는 일은 무엇인지<br />
앞으로 처리해야하는 일은 무엇인지 모델링하는 방법<br />

**3. 데이터와 프로세스의 상관 관점 (Data vs Process, interaction)**<br />
`데이터와 프로세스의 관계`를 위주로한 모델링<br />
프로세스의 흐름에 따라 데이터가 어떤 영향을 받는지<br />


| 관점 (View)| 설명 |
|:----------|:------------------|
| 데이터    | - 비지니스 프로세스에서 사용되는 데이터를 의미한다. <br />- 구조 분석, 정적 분석     | 
| 프로세스  | - 비지니스 프로세스에서 수행하는 작업을 의미한다. <br /> - 시나리오 분석, 도메인 분석, 동적 분석    | 
| 데이터와 프로세스 | - 프로세스와 데이터 간의 관계를 의미한다. <br /> - CRUD(Create, Read, Update, Delete) 분석 | 

<br/>

## 💻 **모델링의 세 가지 단계**

### **1. 개념적 데이터 모델링 (Conceptual Data Modeling)**

고객의 비즈니스 프로세스를 분석하고 업무 전체에 대해서 데이터 모델링

복잡하게 표현하지 않고 중요한 부분을 위주로 모델링하는 단계

**<span style="color:#ff6600">업무적 관점</span>**에서 모델링하며 기술적인 용어는 가급적 사용하지 않음

**<span style="color:tomato">엔터티(Entity)</span>**와 **속성(Attribute)**을 도출하고 개념적 ERD(Entity Relationship Diagram)를 작성

전사적 데이터 모델링 수행 시 행해지며 **<span style="color:#ff6600">추상화 레벨이 가장 높은 모델링</span>**

**<span style="color:#ff6600">업무 중심적</span>**이고 **<span style="color:#ff6600">포괄적</span>**인 수준의 모델링이 진행

<br />

> **<span style="color:#3366ff">엔터티란? </span>** <br />
> 실체, 객체라는 의미로 실무적으로는 앤터티라고 부른다고 한다. <br />
> 즉, 업무에 필요하고 유용한 정보를 저장하고 관리하기 위한 집합적인 것
> 엔터티는 `사람`, `장소`, `사건`, `개념` 등의 `명사`에 해당 <br />
> <span style="color:tomato">엔터티는 업무상 관리가 필요한 관심사에 해당한다.</span><br />
{:.prompt-info}

<br />

### **2. 논리적 데이터 모델링 (Logical Data Modeling)**

>**<span style="color:#3366ff">개념적 모델링을</span> <span style="color:#ff6600">논리적 모델링으로 변환</span> 하는 작업**

>**<span style="color:#3366ff">식별자를 도출</span>하고  <span style="color:#ff6600">필요한 모든 릴레이션을 정의</span>**

>**<span style="color:#3366ff">정규화를 수행</span>해서 데이터 모델의  <span style="color:#ff6600">독립성을 확보</span>**

>**<span style="color:#3366ff">재사용성</span>이 <span style="color:#ff6600">가장 높은 모델링</span>**
>**<span style="color:#3366ff">정규화의 기본 목표</span>는  <span style="color:#ff6600">테이블 간에 중복된 데이터를 허용하지 않는 것</span>**

>**<span style="color:#3366ff">중복된 데이터를 허용하지 않음</span>으로써  <span style="color:#ff6600">무결성(Integrity)를 유지, DB의 저장 용량 감소</span>**

>**<span style="color:#3366ff">데이터 베이스 모델</span>에 대한  <span style="color:#ff6600">Key,속성, 관계등을 모두 표현</span>**
>**<span style="color:#3366ff"></span> <span style="color:#ff6600"></span>**


<br/>


### **3. 물리적 데이터 모델링(Physical Data Modeling)**

>**<span style="color:#3366ff">데이터베이스</span>를 <span style="color:#ff6600">실제 구축,</span> 테이블, 인덱스, 함수 등을 생성.**

>**<span style="color:#3366ff">성능, 보안, 가용성 등</span>의 <span style="color:#ff6600">물리적인 성격을 고려해서 구축</span>**

>**<span style="color:#3366ff"></span> <span style="color:#ff6600"></span>**

<br />

| 데이터 모델링 단계          | 설명        |
|:--------------|:-----------------|
| 개념적 모델링 | - `전사적 관점`에서 기업의 데이터를 모델링한다. <br/> - `추상화 수준이 가장 높은 수준의 모델링`이다. <br/> - 계층형 데이터 모델, 네트워크 모델, 관계형 모델에 관계없이 `업무 측면에서 모델링`한다.   |
| 논리적 모델링 | - 특정 데이터베이스 모델에 종속한다. <br /> - 식별자를 정의하고 관계, 속성 등을 모두 표현한다. <br /> - 정규화를 통해서 `재사용성을 높인다.`    |
| 물리적 모델링 | - 구축할 데이터베이스 관리 시스템에 테이블, 인덱스 등을 생성하는 단계이다. <br /> -`성능, 보안, 가용성 등을 고려하여 데이터베이스를 구축`한다. | 

<br />

## 💻 **데이터의 독립성**
![데이터의 독립성](https://github.com/leekoby/leekoby.github.io/assets/118284808/e467e2e5-e95c-428e-b139-8424dc6480ef)

### 🔎 **3층 스키마 (3-Level Schema)**

**<span style="color:#ff6600">사용자, 설계자, 개발자가 데이터베이스를 보는 관점에 따라 데이터베이스를 기술하고 이들 간의 관계를 정의한 ANSI 표준이다.</span>**

ANSI-SPARC(American National Standards Institute, Standards Planning And Requirements Committee) 아키텍처는 1975년에 제안된 데이터베이스 관리 시스템(DBMS)의 추상적인 설계표준이다.

ANSI-SPARC 아키텍처에서는 스키마를 3단계 구조로 나눈다. 이렇게 분리하는 목적은 데이터베이스에 대한 사용자들의 관점과 데이터 베이스가 실제로 표현되는 물리적인 방식을 분리하기 위함이다. <br />

**<span style="color:#3366ff">3층 스키마</span>는 데이터베이스의  <span style="color:#ff6600">독립성</span>을 확보하기 위한 방법**

**<span style="color:#3366ff">데이터의 독립성을 확보하면</span> <span style="color:#ff6600">데이터 복잡도 감소, 데이터 중복 제거, 사용자 요구사항 변경에 따른 대응력 향상, 관리 및 유지보수 비용절감 등의 장점</span>**

3단계 계층으로 분리해서 독립성을 확보하는 방법으로 각 계층을 **<span style="color:#ff6600">뷰(View)</span>**라고도 한다. 

**<span style="color:#3366ff">데이터베이스가 존재하는 목적 중의 하나</span>는  <span style="color:#ff6600">사용자에게 데이터를 보여줄 수 있는 뷰를 제공하는 것</span>**


### 🔎 **3단계 스키마 구조** 

**1. 외부 스키마 (External Schema)**

**<span style="color:#ff6600">사용자의 관점 </span>**: Multiple User's View 단계로 각 사용자가 보는 데이터베이스의 스키마를 정의한다. 

View 단계로 여러 개의 사용자 관점으로 구성되는 것

DB의 각 사용자나 응용 프로그래머가 접근하는 DB 

**2. 개념 스키마 (Conceptual Schema)**

**<span style="color:#ff6600">통합된 관점</span>** :  Community View of DB 단계로 모든 사용자가 보는 데이터베이스의 스키마를 통합하여 전체 데이터베이스를 나타내는 것이다. 

데이터베이스에 저장되는 데이터들을 표현하고 데이터들 간의 관계를 나타낸다. 

모든 사용자 관점을 통합한 조직 전체 관점의 통합적인 표현 

**3. 내부 스키마 (Internal Schema)**

**<span style="color:#ff6600">물리적인 관점</span>** : Physical Representation 단계로 **<span style="color:#ff6600">물리적인 저장 구조</span>**를 나타낸다.

실직적인 데이터의 저장 구조나 컬럼 정의, 인덱스 등이 포함된다. 

![3층 스키마 구조 이미지](https://github.com/leekoby/leekoby.github.io/assets/118284808/ac06ea3b-5e92-418b-9c35-650bac5f45b0)

|구조 |설명 | 
|:------------|:-----------------|
|외부 스키마 (External Schema) | - `사용자 관점`, 업무상 관련이 있는 데이터 접근이다. <br /> - 관련 데이터베이스의 뷰(View)를 표시한다. <br /> -응용 프로그램이 접근하는 데이터베이스를 정의한다.  | 
|개념 스키마 (Conceptual Schema) | - `설계자 관점, 조직 전체적인 괌점`, 사용자 전체 집단의 데이터베이스 구조이다. <br /> - 전체 데이터 베이스 내의 규칙과 구조를 표현한다. <br /> -통합 데이터베이스 구조이다. | 
|내부 스키마 (Internal Schema) | - `개발자 관점`, 데이터베이스의물리적 저장 구조이다. <br /> - 데이터 저장 구조, 레코드 구조, 필드 정의, 인덱스 등을 의미한다. | 

<br/>


### 🔎 **3단계 스키마 구조가 보장하는 독립성**

ANSI-SPARC 아키텍처에서 이렇게 스키마를 3단계 구조로 나누는 이유는 데이터베이스에 대한 사용자들의 관점과 데이터베이스가 실제로 표현되는 물리적인 방식을 분리하여 독립성을 보장하기 위한 것

**1. 논리적 독립성** : 개념 스키마가 변경되어도 외부 스키마는 영향받지 않는다. 

**2. 물리적 독립성** : 내부 스키마가 변경되어도 외부/개념 스키마는 영향받지 않는다.


## 💻 **ERD (Entity Relationship Diagram)** 

시스템에 어떤 엔터티들이 존재하며 그들 간에 어떤 관계가 있는지 나타내는 다이어그램이다. 

<br/>

### 🔎 **ERD 표기 방식** 

1. **Peter Chen** : 주로 대학교재에서 사용하는 표기법으로 실무에서 사용하는 경우는 드물다. <br />

2. **IDEF1X** (Integration Definition for Information Modeling) : 실무에서 사용하는 경우도 있으며 ERWin에서 사용되는 모델이기도 하다. <br />

3. **IE/Crow's Foot**: 까마귀발 표기법이라고도 부르며 가장 많이 사용한다. ERWin, ERStudio에서 사용되는 모델이다. <br />

4. **Min-Max/ISO** : 각 엔터티의 참여도를 좀 더 상세하게 나타내는 표기법이다. <br />

5. **UML** : 소프트웨어 공학에서 주로 사용되는 모델이다. <br />

6. **Case*Method/Barker** : Oracle에서 사용되는 모델로 Crow's Foot과 비슷하다. <br/>

<br/>

### 🔎 **IE / Crow's Foot 표기법** 

![IE/Crow's Foot 표기법](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcO5sbX%2Fbtqyg9qA61z%2FAVeys7L8XhHskMSRJx8Skk%2Fimg.png){: width="500" height="500" } 

> 관계의 1:N관계에서 N쪽에 새발을 표식으로 하고 
> 
> 선택, 필수 참여관계에서 선택참여에 `○` , 필수 참여에 `|`로 표시한다.<br.>
{:.prompt-tip}

<br />

### 🔎 **ERD 작성 순서** <br />
어떤 표기법을 사용하든 ERD를 작성하는 순서는 공통된 룰이며, 다음의 표기 순서를 따른다. <br />

1. 엔터티를 도출하고 그린다.  <br />
(업무에서 관리해야 하는 집합을 도출한다. )

2. 엔터티를 적절하게 배치한다. <br />
(엔터티를 도출한 후 엔터티를 배치한다. 중요한 엔터티를 왼쪽 상단에 배치한다.)

3. 엔터티 간의 관계를 설정한다. <br />

4. 관계명을 서술한다. <br />
(엔터티 간의 어떤 행위나 존재가 있는지 표현한다.)

5. 관계의 참여도를 표현한다. <br />
(관계 참여도는 한 개의 엔터티와 다른 엔터티 간의 참여하는 관계수를 의미한다. 즉, "고객이 여러 개의 계좌를 개설할 수 있다." 와 같은 의미를 표현하는 것이다. )

6. 관계의 필수/선택 여부를 기입한다. <br />
(필수는 반드시 존재해야 하는 것이다. 예를 들어 "모든 고객은 반드시 하나의 계좌를 개설해야 한다" 와 같은 의미를 표현한다.)

<br/>

## 💻 **데이터 모델링 고려 사항**

### **1️⃣ 데이터 모델의 독립성** 
독립성이 확보된 모델은 고객의 업무변화에 능동적으로 대응할 수 있다. 

독립성을 확보하기 위해서는 중복된 데이터를 제거해야한다. 

데이터 중복을 제거하는 방법이 바로 `정규화`이다. 

<br/>

### **2️⃣ 고객 요구사항의 표현** 

데이터 모델링으로 고객과 데이터 모델러 간에 의사소통을 할 수 있어야하므로, 고객의 요구사항을 간결하고 명확하게 표현해야 한다. 

<br />

### **3️⃣ 데이터 품질 확보**

데이터베이스 구축시에 데이터 표준을 정의하고 표준 준수율을 관리해야 한다.

데이터 표준을 확보해야 데이터 품질을 향상 시킬 수 있다. 


<br/>

## 🎯 **추가 내용**

> ### **데이터베이스 무결성을 보장하기 위한 방법들**
>
> **<span style="color:#3366ff">기본 키 (Primary Key)</span>: <span style="color:#ff6600">각 테이블에 기본 키를 설정하여, 각 레코드의 고유함을 보장. 기본 키는 NULL 값을 가질 수 없으며, 중복된 값을 가질 수 없다.</span>** 
> 
>**<span style="color:#3366ff">외래 키 (Foreign Key)</span>:  <span style="color:#ff6600">외래 키를 이용하여 두 테이블 간의 관계를 정의하고, 참조 무결성을 보장. 외래키는 참조하는 주테이블의 기본키 값들 중 하나를 가져야 하며, 그렇지 않은 잘못된 데이터 입력을 막는다.</span>**
>
>**<span style="color:#3366ff">체크 제약 조건 (Check Constraints)</span>:  <span style="color:#ff6600">체크 제약 조건을 사용하면 컬럼에 입력될 수 있는 값의 범위를 지정할 수 있어서 도메인 무결성을 보장.</span>**
>
>**<span style="color:#3366ff">고유 제약 조건 (Unique Constraints)</span>:  <span style="color:#ff6600">고유 제약 조건은 컬럼에 중복된 값을 가지지 못하게 하여 엔터티의 고유함을 보장.</span>**
>
>**<span style="color:#3366ff">NOT NULL 제약 조건</span>:  <span style="color:#ff6600">NOT NULL 제약 조건은 해당 컬럼에 NULL 값을 허용하지 않도록 한다.</span>**
>
>**<span style="color:#3366ff">트랜잭션 관리</span>:  <span style="color:#ff6600">ACID 속성(Atomicity 원자성, Consistency 일관성, Isolation 고립성, Durability 지속성) 을 만족시키는 트랜잭션 관리를 통해 데이터 무결성을 유지할 수 있다.</span>**
>
>**<span style="color:#3366ff">데이터 유효성 검사</span>:  <span style="color:#ff6600">애플리케이션 단에서도 데이터 유효성 검사 로직을 구현하여 잘못된 데이터 입력을 사전에 방지할 수 있다.</span>**
>
>**<span style="color:#3366ff">트리거 (Trigger)</span>:  <span style="color:#ff6600">데이터베이스에서 자동으로 실행되도록 설정하는 프로그램으로서 INSERT, UPDATE 및 DELETE 작업 전/후에 원하는 SQL문(데이터 확인 및 변경 등) 을 실행시켜 데이터 무결성을 확보할 수 있다.</span>**
>
>**<span style="color:#3366ff">어플리케이션 단에서의 데이터 유효성 검사</span>: <span style="color:#ff6600">애플리케이션 단에서도 데이터 유효성 검사 로직을 구현하여 잘못된 데이터 입력을 사전에 방지할 수 있다.</span>**
> - 입력 검증: 사용자나 외부 시스템으로부터 받은 입력값이 유효한지 검사
> - 비즈니스 규칙 적용: 비즈니스 로직에 따른 규칙을 적용하여 데이터의 일관성을 유지
> - 트랜잭션 관리: 어플리케이션에서도 DBMS와 마찬가지로 트랜잭션을 활용하여 여러 작업들 사이에 일관성 있는 상태를 유지하고 원자적인 연산을 보장
> - 오류 처리 및 복구 메커니즘: 예상치 못한 오류 발생 시 적절하게 처리하고 안전한 상태로 복구하는 메커니즘
> - 데이터 접근 권한 관리: 사용자별로 접근 가능한 데이터와 기능을 제한하여 부적절한 변경으로부터 데이터를 보호
{:.prompt-info}





#### 👉

<br/>

## 📚 **레퍼런스**

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
{:.prompt-info }

> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
{:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.1 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-1/dashboard){:target="_blank"}
{:.prompt-info }