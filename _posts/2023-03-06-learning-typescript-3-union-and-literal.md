---
layout: post
title: 러닝타입스크립트 3장 - 유니언과 리터럴
author: admin
date: 2023-03-06 00:00:00 +900
lastmod: 2023-03-06 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, LEARNING TYPESCRIPT] # 대문자로 작성
tags: [typescript, learning typescript] # 소문자로 작성
---

> 기존 블로그에 작성했던 스터디 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `TypeScript`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---

<br/>

# **🌈 유니언과 리터럴**

- 유니언(union): 값에 허용된 타입을 두 개 이상의 가능한 타입으로 확장하는 것 

- 내로잉(narrowing): 값에 허용된 타입이 하나 이상의 가능한 타입이 되지 않도록 좁히는 것 

<br/>

## **📖 3.1 유니언(Union)**

<br/>

값이 정확히 어떤 타입 인지 모르지만 두 개 이상의 옵션 중하나라는 것을 알고 있는 경우 사용할 수 있다.

타입스크립트는 가능한 값 또는 구성 요소 사이에 `|` 연산자를 사용해 유니언 타입임을 나타낸다. 

```js
// // something: string | number
let something :string| number;

// something: string | number
let something2 = Math.random() > 0.5 ?"leekoby"  : 1;

// "string"과 "number" 모두 갖고 있는 메서드
something2.toString();

// ❌ Error : Property 'toFixed' does not exist on type 'string'.
something2.toFixed(); // "number"만 갖고 있는 메서드

// ❌ Error : Property 'toUpperCase' does not exist on type 'number'.
something2.toUpperCase();//"string" 만 갖고 있는 메서드
```

<br/>

---

<br/>

## **📖 3.2 내로잉(narrowing)**

<br/>

<strong style="color:#3366ff">내로잉(narrowing)</strong>

- 값의 타입(union)의 범위를 좁히는 과정 
- 값이 이전에 유추된 것보다 더 구체적인 타입임을 유추하는 것 

<strong style="color:#3366ff">타입가드(type guard)</strong>
- 타입을 좁히는 데 사용할 수 있는 논리적 검사

<br/>

### **3.2.1 값 할당을 통한 내로잉**

타입스크립트는 변수의 타입을 할당된 값의 타입으로 좁힌다. 

```js
let admiral : number| string;
admiral ="leekoby";

admiral.toUpperCase(); //OK : string

// ❌ Error : Property 'toFixed' does not exist on type 'string'. Did you mean 'fixed'?
admiral.toFixed();
```

```js
let admiral : number| string='leekoby';

admiral.toUpperCase(); //OK : string

// ❌ Error : Property 'toFixed' does not exist on type 'string'. Did you mean 'fixed'?
admiral.toFixed();

```

<br/>

### **3.2.2 조건 검사를 통한 내로잉**

조건문으로 유니언 타입을 좁힐 수 있다. 

```ts
let something = Math.random() > 0.5 ? "leekoby" : 1;

// type이 number 일 때로 타입 좁히기
if (something === 2) {
  // something: 2
  something.toFixed();
}
// type이 string 일 때로 타입 좁히기
if (something === "leekoby") {
  // something: "leekoby"
  something.toUpperCase();
}

// ❌ Error : Property 'toUpperCase' does not exist on type 'number'.
something.toUpperCase();
```

<br/>

### **3.2.3 typeof 검사를 통한 내로잉**

`typeof` 연산자를 사용하여 타입을 좁힐 수 있음. 

`typeof` 검사는 자주 사용하는 실용적인 방법

```js
let something = Math.random() > 0.5 ? "leekoby" : 1;

// type이 number 일 때로 타입 좁히기
if (typeof something === 'number') {
    something.toFixed();
}
// type이 string 일 때로 타입 좁히기
if (typeof something === 'string') {
    something.toUpperCase();
}

// ! 를 사용한 논리 부정과 else 문도 가능 
// type이 number 일 때로 타입 좁히기
if (!(typeof something === 'number')) {
    something.toUpperCase();
} else something.toFixed();
// type이 string 일 때로 타입 좁히기

// 삼항연산자도 사용가능
typeof something === "string" ? something.toUpperCase():something.toFixed()

// ❌ Error : Property 'toUpperCase' does not exist on type 'number'.
something.toUpperCase();
```

<br/>

---

<br/>

## **📖 3.3 리터럴 타입(literal type)**

좀 더 구체적인 버전의 원시 타입

원시 타입 값 중에 하나가 아닌 **<span style="color:#ff6600">특정 원싯값</span>**으로 알려진 타입을 의미한다.

- `string` 이 아닌 "leekoby"라는 구체적인 타입을 리터럴 타입이라고 한다.

```ts
// const something:"leekoby"
const something ="leekoby"
```

<br/>

### **3.3.1 리터럴 할당 가능성** 

동일한 원시 타입이라고 해도 서로 다른 리터럴 타입이면 할당할 수 없다. 

```ts
let something: "leekoby" = "leekoby";
something = "Byron"; // ❌ Error: Type '"Byron"' is not assignable to type '"leekoby"'.
```

<br/>

✅ 리터럴 타입은 원시 값에 할당 가능 
❌ 원시 값은 리터럴 타입에 할당 불가

```ts
let sayHi: "Hi"
sayHi = "Bye" //❌ 오류발생

sayHi= "Hi"
let something = ""
something = sayHi // ✅가능
```

<br/>

## **💻 3.4 엄격한 null 검사(strict null checking)**

타입이 필요한 위치에서 null 값을 허용하는 많은 타입 시스템
		
	const something: string = null;
    
타입스크립트는, `strictNullChecks` 옵션을 통하여 엄격한 `null` 검사 설정을 할 수 있다.
일반적으로 엄격한 `null` 체크를 활성화해야만 `null` 또는 `undefined` 값으로 인한 오류로 부터 안전한지 쉽게 파악가능 

```ts
let something: string;

// strictNullChecks: true
something = null; // ❌ Error: Type 'null' is not assignable to type 'string'.

// strictNullChecks: false
something = null; // 문제 없음
```

>🔑 엄격한 `null`검사를 활성화하는 것을 권장

<br/>

### **3.4.2 참 검사를 통한 내로잉**

`Javascript`에서는 `falsy`(`0, -0, 0n, "", false, null, undefined, NaN`) 같은 `falsy` 값을 제외하고 모두 참이다. 이를 이용하여 타입 추론을 할 수 있다.

```ts
let sth = Math.random() > 0.5
    ? "leekoby"
    : undefined;

if(sth){
    sth.toUpperCase(); //✅
}
sth.toUpperCase();
// ❌ Error : sth' is possibly 'undefined'.
```

> 💢 논리 연산자의 부족한 점 : 
- 참 여부 확인 외에 다른 기능은 제공하지 않음.
- `falsy` 값이라면 `""` 같은 빈 문자열인지 `undefined`인지 알수 없음 

```ts
let sth = Math.random() > 0.5 && "hello"
if (sth) {
    // sth: string
    sth
} else {
    // sth : string | false
    sth
}

```

<br/>

### **3.4.3 초깃값이 없는 변수** 

<br/>

`javascript`에서는 초깃값이 없는 변수는 기본적으로 `undefined`가 된다. 

`Typescript` 에서는 값이 할당될 때까지 변수가 `undefined`임을 이해하고, 변수의 속성을 사용하려고 시도하면 오류를 발생시킨다. 

```ts
let sth: string;
sth.length;
// ❌ Error : Variable 'sth' is used before being assigned.


let someth: string | undefined;

someth?.length; //✅

someth = "leekoby";
someth.length; // ✅
```

<br/>

## **💻 3.5 타입 별칭**

<strong style="color:#3366ff">타입 별칭(type alias)</strong> :
  - 재사용하는 타입에 더 쉬운 이름을 할당하는 것 
  - `type` 새로운이름 = `type` 형태이며 `pascalCase`로 작성
  
```ts
type sth = string | number;
let something: sth = "leekoby";

type sth2 = boolean | number | string | null | undefined;
let first: sth2;
let second: sth2;
let third: sth2;

```

<br/>

### **3.5.1 타입 별칭은 `Javascript`엔 없다**
타입 별칭은 `annotion`처럼 자바스크립트로 컴파일되지 않는다. (런타임에 존재하지 않음)

위의 코드를 컴파일한 결과
```ts
"use strict";
let something = "leekoby";
let first;
let second;
let third;
```
또한 타입 별칭은 타입 시스템에만 존재하므로 런타임 코드에서 참조할 수 없다. 

```ts
console.log(sth, sth2)
// ❌ Error : 'sth' only refers to a type, 
// but is being used as a value here.

// ❌ Error : 'sth2' only refers to a type,
// but is being used as a value here.
```

<br/>

### **3.5.2 타입 별칭 결합**

타입 별칭은 다른 타입 별칭을 참조할 수 있다. 

```ts
type Id = number | string;
type Idmaybe = Id | undefined | null
// IdMaybe의 타입은 number | string | undefined | null
```
```ts
type Idmaybe = Id | undefined | null
type Id = number | string;
```

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}

> [Typescript 핸드북](https://typescript-kr.github.io/pages/unions-and-intersections.html){:target="_blank"}
{:.prompt-info}
