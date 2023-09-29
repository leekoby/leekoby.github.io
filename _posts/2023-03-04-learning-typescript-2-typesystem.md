---
layout: post
title: 러닝타입스크립트 2장 - 타입 시스템
author: admin
date: 2023-03-04 00:00:00 +900
lastmod: 2023-03-04 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT, LEARNING TYPESCRIPT] # 대문자로 작성
tags: [typescript, learning typescript] # 소문자로 작성
image:
  path: https://www.hanbit.co.kr/data/books/B9711663545_l.jpg
  width: 400
  height: 250
  alt: 러닝 타입스크립트
---

> 기존 블로그에 작성했던 스터디 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `TypeScript`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---

<br/>

# **📖 타입**

<br/>

## **💻 2.1 타입의 종류**

`타입`이란 `Javascript`에서 다루는 값의 **<span style="color:#ff6600">형태</span>**에 대한 설명이다.

<br/>

### **🍳 형태란?**

값에 존재하는 속성과 메서드 그래고 내장되어 있는 `typeOf` 연산자가 설명하는 것을 의미 


- 일곱 가지 기본 원시 타입
  
  1. `null`
  
  2. `undefined`
  
  3. `boolean`
  
  4. `string`
  
  5. `number`
  
  6. `BingInt`
  
  7. `symbol`
  
<br/>

### **🍳 2.1.1 타입 시스템**

타입 시스템(type system)은 프로그래밍 언어가 프로그램에서 가질 수 있는 타입을 이해하는 방법에 대한 규칙 집합. 

- 기본적인 타입스크립트의 타입 시스템 동작 
  
  1. 코드를 읽고 존재하는 모든 타입을 이해
  
  2. 각 값이 초기 선언에서 가질 수 있는 타입을 확인
  
  3. 각 값이 추후 코드에서 어떻게 사용될 수 있는지 모든 방법 확인
  
  4. 값의 사용법이 타입과 일치하지 않으면 사용자에게 오류 표시 
  
<br/>

### **🍳 2.1.2 오류 종류**

자주 접하게 되는 두 가지 오류 

- **구문 오류** : 
  - 타입스크립트가 자바스크립트로 변환되는 것을 차단한 경우

- **타입 오류** : 
  - 타입 검사기에 따라 일치하지 않는 것이 감지된 경우 
  
<br/>

#### **구문 오류**

- `Typescript`가 코드로 이해할 수 없는 잘못된 구문을 감지할때 발생

- 고문 오류가 발생하는 경우 컴파일을 차단
  (`javascript` 파일을 얻지 못하거나 얻더라도 예상과 다를 수 있음)

<br/>

```ts
let let someting; 
// Error : ',' expected
```


> 타입스크립트는 구문 오류와는 상관없이 자바스크립트 코드를 출력하기 위해서 최선을 다함. 
> 
> 하지만 원하는 출력 결과가 아닐 수 있음. 
> 
> 따라서 컴파일 된 `Javascript`를 실행하기 전에 구문 오류를 수정한는 것이 좋음
{:.prompt-tip} 

<br/>

#### **타입 오류**

`Typescript`의 `inference`로 오류를 감지했을 때 발생

오류가 발생했다고 컴파일을 차단하지 않음

> `Javascript` 코드가 원하는 대로 실행되지 않을 가능성이 있다는 신호를 타입 오류로 알려준다. 
> 
> `Javascript`를 실행하기 전에 타입 오류를 확인하고 발견된 문제를 먼저 해결하는 것이 가장 좋다.
{:.prompt-tip}

<br/>

---

<br/>

## **💻 2.2 할당 가능성**

`Typescript`는 변수의 초깃값을 읽고 해당 변수가 허용되는 타입을 결정

`Typescript`에서 함수 호출이나 변수에 값을 제공할 수 있는지 확인하는 것을 **<span style="color:#ff6600">할당 가능성(assignability)</span>**이라고 한다.

즉, 전달된 값이 예상된 타입으로 할당 가능한지 확인

<br/>

## **💻 2.3 annotation**

초기 타입을 유추할 수 없는 변수는 **<span style="color:#ff6600">진화하는 any</span>**라고 부른다.

```ts
let rocker;  // type : any
rocker="Joan Jett"  // type : string

rocker.toUpperCase(); //OK

rocker = 19.58; // type : number
rocker.toPrecision(1); //OK

rocker.toUpperCase();
//Property 'toUpperCase' does not exist on type 'number'.
```

<br/>


`typescript`는 초깃값을 할당하지 않고 변수의 타입을 선언할 수 있는 구문인 `type annotation` 이 존재한다. 

`type annotation`은 변수 이름 뒤에 배치되며 콜론(:)과 타입이름을 차례대로 기재한다. 

```ts
let rocker:string;
rocker="Joan Jett"
```

<br/>

이러한 `Type annotation`은 `Typescript`에만 존재하며 런타임 코드에 영향을 주지도 않고, 컴파일 시 제거된다. 


> 초기 할당으로 `TypeChecker`에 의해서 `inference`가 가능할 경우 `anntation`까지 중복해서 사용하지 않는 것이 좋다.
> (경우에 따라 다름)
{:.prompt-tip}

<br/>

## **💻 2.4 타입 형태** 

타입의 형태가 정해졌다면 `Typescript`는 객체에서 어떤 멤버 속성이 존재하는지 알고 있기 때문에 해당 타입에서 사용가능한 속성을 자동완성할 수 있고, 잘 못된 부분이 있는지도 확인 가능하다. 

```ts
// Type Annotation으로 구체적으로 명시가능 
// 하지만 Inference가 가능한 경우 오히려 효율과 가독성이 떨어질 수 있음 
const user: { firstName: string; age: number; } = {
  firstName: "hoseung",
  age: 33,
};
//Property 'middleName' does not exist on type '{ firstName: string; age: number; }'.
user.middleName;
```

`Typescript`는 객체의 형태에 대한 이해를 바탕으로 할당 가능성뿐만 아니라 객체 사용과 관련된 문제도 알려준다. 

<br/>

## **💻 2.5 Module**
한 모듈에서 다른 파일에 선언된 변수와 동일한 이름으로 선언된 변수는 다른 파일의 변수를 가져오지 않는 이상 이름 충돌이 발생하지 않는다. 
(`export`로 내보내지 않으면 다른 파일에서 사용할 수 없다)


```ts
// a.ts
export const shared = "koby";

// b.ts
export const shared = "koby";

// index.ts
import { shared } from "./a";
// Error: Import declaration confilcts with local declaration of 'shared'

export const shared = 'koby'
//Error : Individual declarations in merged declaration 
//'shared' must be all exported or all local.
```
그러나 파일이 `script`일 경우 `Typescript`는 해당 파일을 `global scope`로 간주하므로 모든 `script`가 파일의 내용에 접근할 수 있다. 

<br/>

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}

> [모듈 & 네임스페이스 시스템 이해하기 \| Inpa Dev](https://inpa.tistory.com/entry/TS-%F0%9F%93%98-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EB%AA%A8%EB%93%88-%EB%84%A4%EC%9E%84%EC%8A%A4%ED%8E%98%EC%9D%B4%EC%8A%A4-%EC%8B%9C%EC%8A%A4%ED%85%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0){:target="_blank"}
{:.prompt-info}

> [ Modules \| Typescript 공식문서 ](https://www.typescriptlang.org/ko/docs/handbook/modules.html){:target="_blank"}
{:.prompt-info}

