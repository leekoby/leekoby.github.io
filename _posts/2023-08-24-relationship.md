---
layout: post
title: 관계 (Relationship)
author: admin
date: 2023-08-24 06:00:00 +900
lastmod: 2023-08-24 06:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, SQLD]
tags: [sql, sqld, relationship]
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br/>

# **🌈 관계 (Relationship)**

## **💻 관계란?** 

- 엔터티 간의 관련성을 의미

- 어떠한 연관성이 있는지 타입을 분류하여 **`존재 관계`**와 **`행위 관계`**로 나눌 수 있다.

- 존재 관계는 두 개의 엔터티가 **<span style="color:#ff6600">존재 여부</span>**의 관계가 있는 것 

- 행위 관계는 두 개의 엔터티가 어떤 **<span style="color:#ff6600">행위에 의한 관련성</span>**이 있는 것

<br/>

## **💻 관계의 종류**

### **🔍 존재 관계**  

**<span style="color:#ff6600">존재 관계는 엔터티 간의 `상태`를 의미</span>**

엄마와 아기처럼 존재 자체로 연관성이 있는 관계를 의미 

- 직원과 부서

- 학생과 학과 엔터티 

- 고객이 은행에 회원가입을 하면, 관리점이 할당되고, 그 할당된 관리점에서 고객을 관리

![존재 관계 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a296d705-2761-455e-b105-e76b582d4c29)

<br/>

### **🔍 행위 관계**

**<span style="color:#ff6600">행위관계는 엔터티 간에 어떤 행위가 있는 것</span>**

특정한 행위를 함으로써 연관성이 생기는 관계를 의미 

- 회원과 주문

- 학생과 출석부 엔터티 

- 증권 회사는 계좌를 개설하고 주문을 발주

![행위 관계 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/cb1e6033-bd8d-48b8-bff0-b5eb946c381d)

<br/>

## **💻 표기법** 

|  | | 
| :--: | :-- |  
| 관계명 <br/>(Membership)   | - 관계의 이름   |      
| 관계차수 <br/>(Cardinality)   | - 관계에 참여하는 수 <br/> - `1:1`, `1:M`, `M:N`과 같은 관계의 `기수성`을 나타낸다.   |
| 관계선택사양(선택성) <br/>(Optionality)   | - 관계가 필수 관계인지, 선택 관계인지 나타낸다.  |      

<br/>

### **🔍 관계명 (Membership)**

- 엔터티와 엔터티가 어떤 관계를 맺고 있는지 나타내주는 문장

- 모든 관계는 두 개의 관계명을 가지고 있다. 
  
  - 각 엔터티의 관점에서 관계명을 하나씩 가지기 때문

![관계명 (Membership) image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2600b122-832c-4598-b279-a63c55ad92b0)

<br/>

> 관계명은 반드시 `명확한 문장`으로 표현해야하며 `현재형`이여야 한다. <br/>
{:.prompt-warning}

| 바람직하지 않은 관계명  | 바람직한 관계명 | 
| :--: | :--: |  
| 연관성이 있다. | 주문한다.   |       
| 관계가 있다.   | 소속된다.   |       
| 출석을 했다.   | 출석을 한다.   |       

<br/>

### **🔍 관계차수 (Cardinality)** 

두 개의 엔터티 간에 **<span style="color:#ff6600">관계에 참여하는 수</span>**를 의미

- 예를 들어 한 명의 고객은 여러 개의 계좌를 개설할 수 있다. 
  
  - 이러한 경우는 `일대다 관계`가 된다. 

- 일반적으로 `일대일(1:1)`, `일대다(1:M)`, `다대다(M:N)` 형식으로 구분

|  | | 
| :--: | :-- |  
|  일대일(1:1)  | ![일대일(1:1) image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c3289921-2f8e-4a14-aacd-7789883e44ad)  |      
|  일대다(1:M)  | ![일대다(1:M) image](https://github.com/leekoby/leekoby.github.io/assets/118284808/17c35b13-8190-492b-b663-b06a0234b1e6)   |      
|  다대다(M:N)  |  ![다대다(M:N) image](https://github.com/leekoby/leekoby.github.io/assets/118284808/cafd13b4-9321-4040-9f06-03526e3c2eb9)  |      

<br/>

**1대1 관계**

1:1 관계는 `완전 1대1` 관계와 `선택적 1대1` 관계가 있다. 

- 선택적 1대1관계를 표현하자면, 한 명의 고객은 하나의 고객등급이 부여되거나 고객등급이 없을 수도 있다.

![선택적 1대1 관계 image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2b09471b-aead-497b-8890-fc3eda2ba405)

<br/>   

| 종류 |설명 | 
| :- | :- |  
|완전 1대1|하나의 엔터티에 관계되는 엔터티의 관계가 하나인 경우로, <br/>반드시 존재한다.|
|선택적 1대1|하나의 엔터티에 관계되는 엔터티의 관계가 하나이거나 없을 수도 있다.|

<br/>

**1대N 관계** 

1대N 관계는 엔터티에 행이 하나 있을 때 다른 엔터티의 값이 여러개 있는 관계

- 예를 들어 고객은 여러 개의 계좌를 가질 수 있다.

<br/>

**M대N 관계**

M대N  관계는 두 개 엔터티가 서로 여러 개의 관계를 가지고 있는 것이다.

- 예를 들어 한 명의 학생이 여러 개의 과목을 수강할 수 있다. 반대로 한 개의 과목은 여러 명의 학생이 수강한다. 그래서 M대N 관계가 발생한다.

관계형 데이터베이스에서 M대N 관계의 조인(Join)은 **<span style="color:#ff6600">카테시안 곱이 발생</span>**한다. 

- M대N 관게를 `1대N`, `N대1`로 해소해야 한다.

<br/>

### **🔍 관계선택사양 (Optionality)**

|  | | 
| :--: | :-- |  
| 필수적 관계 (**`ㅣ`**)   |  - `반드시 하나가 있어야 하는 관계`이다. <br/> - e.g.) 고객이 반드시 있어야 계좌를 개설할 수 있다. |      
| 선택적 관계 (**`O`**)  |  - `없을 수도 있는 관계`이다. <br/> - 고객은 있지만, 계좌가 없을 수도 있다면 선택적 관계가 된다.  |

관계선택사양은 이 관계가 필수요소인지 선택사항인지를 나타내는 말

필수적 관계는 반드시 하나는 존재해야 하는 관계이고 선택적 관계는 없을 수도 있는 관계이다.

**<span style="color:#3300ff">필수적 관계</span>**는 **`ㅣ`**로 표현되고 **<span style="color:#3300ff">선택적 관계</span>**는 **`O`**으로 표현된다.

- 주문은 반드시 하나 이상의 주문상품이 존재해야 하므로 주문과 주문상품의 관계는 **`필수`**

- 학생이 강의에 출석을 할지 말지는 학생의 선택사항이므로 학생과 출석부 엔터티의 관계는 **`선택`**  

![관계선택사양 (Optionality) image](https://github.com/leekoby/leekoby.github.io/assets/118284808/555f2050-bedc-4d5e-b230-b95bacaa19e4)

<br/>

## 🎯 **추가 내용**

>**<span style="color:#3366ff">엔터티 간의 관계를 정의할 때 고려해야할 사항</span>**
>
> - 두 엔터티 사이를 이어주는 **<span style="color:#ff6600">`동사`가 존재</span>**
> - **<span style="color:#ff6600">조합이 되는 정보가 존재</span>**
> - **<span style="color:#ff6600">영향력 있는 관계가 존재</span>**
> - **<span style="color:#ff6600">관심있는 연관규칙 존재</span>**
> - **<span style="color:#ff6600">관계 연결에 대한 규칙 서술</span>**
{:.prompt-info}

<br/>

## 📚 **레퍼런스**

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
{:.prompt-info }

> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
{:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.1 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-1/dashboard){:target="_blank"}
{:.prompt-info }