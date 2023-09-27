---
layout: post
title: 러닝타입스크립트 4장 - 객체
author: admin
date: 2023-03-07 00:00:00 +900
lastmod: 2023-03-07 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT, LEARNING TYPESCRIPT] # 대문자로 작성
tags: [typescript, learning typescript] # 소문자로 작성
---

> 기존 블로그에 작성했던 스터디 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `TypeScript`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---

<br/>

# **🌈 객체**

## **📖 4.1 객체 타입**

- `{...}` 구문을 사용해서 객체 리터럴을 생성하면, TS는 해당 속성을 기반으로 새로운 객체 타입 또는 타입 형태를 고려한다.

- 해당 객체 타입은 객체의 값과 동일한 속성명과 원시 타입을 갖는다.

- 값의 속성에 접근하려면 value.멤버또는value["멤버"]를 구문을 사용한다.

```ts
const someone = {
	born : 1990,
  	name : "leekoby" 
};
someone["born"] // 타입 : number
someone.name // 타입 : string

someone.hello; 
// ❌ Error : Property 'hello' does not exist on type '{ born: number; name: string; }'.
```

- 객체 타입은 `Typescript`가 `Javascript`를 이해하는 방법에 대한 핵심 개념

- `null`과 `undefined`를 제외한 모든 값은 그 값에 대한 실제 타입의 멤버 집합을 가짐. 

- 그렇기 때문에 `Typescript`는 모든 값의 타입을 확인하기 위해서 객체 타입을 이해해야한다.

<br/>

### **4.1.1 객체 타입 선언**

- 기존 객체에서 직접 타입을 유추하는 방법도 굉장히 좋지만, 객체의 타입을 명시적으로 선언하고 싶음

- 명시적으로 타입이 선언된 객체와는 별도로 객체의 형태를 설명하는 방법 필요

- 객체 타입은 객체 리터럴과 유사하게 보이지만 필드 값 대신 타입을 사용해 설명

```ts
let someone = {
    born: 1990,
    name: "leekoby"
};

someone = {
    born: 1333,
    name: "kobykoby"
};
someone["born"] // 타입 : number
someone.name // 타입 : string

someone = "cube"
// ❌ Error : Type 'string' is not assignable to type '{ born: number; name: string; }'.
```

<br/>

### **4.1.2 별칭 객체 타입** 

- 객체 타입을 계속 장성하는 일은 효율성이 떨어진다. 

- 객체의 타입에 타입 별칭을 할당해 사용하는 방법이 더 일반적이다.

- ✅ 대부분의 타입스크립트 프로젝트는 객체 타입을 설명할 때 interface 키워드 사용을 선언한다.

```ts
type koby = {
    born: number;
    name: string;
}

let someone: koby
someone = {
    born: 1990,
    name: "leekoby"
};

someone = {
    born: 1333,
    name: "kobykoby"
};
someone["born"] // 타입 : number
someone.name // 타입 : string

someone = "cube"
// ❌ Error : Type 'string' is not assignable to type 'koby'.
```

<br/>

>대부분의 `Typescript` 프로젝트는 객체 타입을 설명할 때 `interface`

# **🦆 4.2 구조적 타이핑**

- 타입스크립트의 타입 시스템은 구조적으로 타입화 되어 있다.

  - 즉, 타입을 충족하는 모든 값을 해당 타입의 값으로 사용할 수 있다.

  - 매개변수나 변수가 특정 객체 타입으로 선언되면 타입스크립트에 어떤 객체를 사용하든 해당 속성이 있어야 한다

```ts
type WithFirstName = {
 firstName: string; 
};

type WithLastName = {
  lastName: string;
}

const hasBoth = {
  firstName: "Koby",
  lastName: "Lee"
};

// ✅ hasBoth는 string 타입의 "firstName"을 포함함
let WithFirstName: WithFirstName = hasBoth;

// ✅ hasBoth는 string 타입의 "lastName"을 포함함
let WithLastName: WithLastName = hasBoth;
```

- <strong style="color:#3366ff">덕 타이핑 ( `JavaScript` )</strong>: 
  - 런타임에서 사용될 때까지 객체 타입을 검사하지 않는 것

- <strong style="color:#3366ff">구조적 타이핑 ( `TypeScript` )</strong>: 
  - 정적 시스템이 타입을 검사하는 것

<br/>

### **4.2.1 사용검사**

객체 타입에 정의한 값을 필수적으로 선언해야 하고, 타입의 정보와도 일치해야한다.

```ts
type Sth = {
  name: string;
  age: number;
};

//  Error: Property 'age' is missing in type '{ name: string; }' but required in type 'Sth'
const sth : Sth = {
  name: "Aatrox",
};

// Error: Type 'string' is not assignable to type 'number'
const sthBoth: Sth = {
  name: "Aatrox",
  age: "10",
};
```


<br/>

### **4.2.2 초과 속성 검사**
초기에 정한 타입보다 더 많은 속성을 사용하려하면 타입 오류가 발생

객체 리터럴이 아닌 경우에는 구조적 타이핑에 의해서 오류가 발생하지 않음

```ts
type Sth = {
    name: string;
    age: number;
};

// (3)
const Sth1: Sth = {
    name: "leekoby",
    age: 33,
    activity: 'working',
    // ❌ Error : Type '{ name: string; age: number; activity: string; }' is not assignable to type 'Sth'.
    // Object literal may only specify known properties, and 'activity' does not exist in type 'Sth'.

};

// (4)
const Sth = {
    name: "kobykoby",
    age: 22,
    activity: 'working',
};
// (4)
const Sth2: Sth = Sth;

// ❌ Error : Property 'activity' does not exist on type 'Sth'.
Sth2.activity;
```

<br/>

### **4.2.3 중첩된 객체 타입**

`Javascript` 객체는 다른 객체의 멤버로 중첩될 수 있으므로 타입스크립트의 객체 타입도 타입 시스템에서 중첩된 객체 타입을 나타낼 수 있어야한다

- 중첩 객체의 속성의 형태를 자체 별칭 객체 타입으로 추출하면 오류 메시지에 더 많은 정보를 담을 수 있다

```ts
type Someone = {
    author: {
        firstName: string;
        lastName: string;
    };
    name: string;
};

const someoneMatch: Someone = {
    author: {
        firstName: 'koby',
        lastName: 'lee'
    },
    name: 'Typescript',
};

const someoneMismatch : Someone = {
    author: {
        name: "React"  
/**
  ❌ Error: Type '{ name: string; }' is not assignable 
  to type '{ firstName: string; lastName: string; }'.
  Object literal may only specify known properties, and 'name' 
  does not exist in type '{ firstName: string; lastName: string; }'.
 */
    },  
    name: 'JavaScript',
};
```

객체의 속성으로 타입을 사용할 수 있기 때문에 위처럼 사용하는 것보다는 아래와 같이 사용하는 것이 가독성이 더 좋고, 오류 메세지도 보다 명확해진다.

```ts
type Someone = {
    firstName: string;
    lastName: string;
}
type doSomethong = {
    who: Someone;
    activity: string;
}
const someoneMismatch: doSomethong = {
    who: {
        name: "leekoby"
        /**
          ❌ Error : Type '{ name: string; }' is not assignable to type 'Someone'.
          Object literal may only specify known properties, 
          and 'name' does not exist in type 'Someone'.
          */
    },
    activity: 'run',
};
```

<br/>

### **4.2.4 선택적 속성**

`?:`을 이용해서 선택적 속성을 부여할 수 있다.

선택적 속성 `?:` 대신 undefined를 사용할 수 있음 

그러나 `undefined`를 유니언으로 선언했다면 속성 값을 반드시 선언한다.

```ts
type Someone = {
  name: string;
  age?: number;
  work: boolean | undefined;
};
const person1: Someone = {
  name: "Aatrox",
  age: 26,
  work: false,
};
// ❌ Property 'work' is missing in type '{ name: string; age: number; }' but required in type 'Someone'.
const person2: Someone = {
  name: "Aatrox",
  age: 26,
};
const person3: Someone = {
  name: "Aatrox",
  work: false,
};
// `?:` 대신 undefined 
const person4: Someone = {
  name: "Aatrox",
  age: 26,
  work: undefined,
};
```

<br/>

## **📖 4.3 객체 타입 유니언**

- TS 코드에서는 속성이 조금 다른, 하나 이상의 서로 다른 객체 타입이 될 수 있는 타입을 설명할 수 있어야 한다.

- 속성값을 기반으로 해당 객체 타입 간에 타입을 좁혀야 할 수 도 있다.

<br/>


### **4.3.1 유추된 객체 타입 유니언**

- 변수에 여러 객체 타입 중 하나가 될 수 있는 초깃값이 주어지면 TS는 해당 타입을 객체 타입 유니언으로 유추한다.

- 유니언 타입은 가능한 각 객체 타입을 구성하고 있는 요소를 모두 가질 수 있다.

- 객체 타입에 정의된 각각의 가능한 속성은 비록 초깃값이 없는 선택적 타입이지만 각 객체 타입의 구성 요소로 주어진다.

```ts
const poem = Math.random() > 0.5
    ? { name: "leekoby", pages: 9 }
    : { name: "kimcube", rhymes: true }
/*
type:
{
    name : string;
    pages : number;
    rhymes?: undefined;  
}
|
{
    name : string;
    pages ?: undefined;
    rhymes : boolean;  
}
 */
poem.name //string
poem.pages; // number | undefined
poem.rhymes; // booleans | undefindes

```
다음 `poem` 값은 항상 `string` 타입인 `name`이고, `pages`와 `rhymes`는 있을 수도 있고 없을 수도 있다.

<br/>

### **4.3.2 명시된 객체 타입 유니언 & 4.3.3 객체 타입 내로잉**

- 객체 타입의 조합을 명시하면 객체 타입을 더 명확히 정의할 수 있다.
- 값이 타입이 객체 타입인 유니언이라면 일반적으로는 모든 유니언에 존재한느 값만 사용할 수 있다. 
- 타입 내로잉을 통해서 타입을 좁혀서 좀 더 정확하게 타입을 추론하고 제어할 수 있다.

```ts
type Someone = {
  name: string;
  age: number;
};
type Stranger = {
  name: string;
  marriage: boolean;
};
let person: Someone | Stranger;
person = Math.random() > 0.5 ? { name: "leekoby", age: 33 } : { name: "kimcuby", marriage: true };
person.name;
person.age;
// ❌ Error: Property 'age' does not exist on type 'Someone | Stranger'.
//           Property 'age' does not exist on type 'Stranger'.

person.marriage;
// ❌ Error: Property 'marriage' does not exist on type 'Someone | Stranger'.
//           Property 'marriage' does not exist on type 'Someone'.
if ("age" in person) {
  person.age;
} else {
  person.marriage;
```
- 잠재적으로 존재하지 않는 객체의 멤버에 대한 접근을 제한하면 코드의 안전을 지킬 수 있다.
- 값이 여러 타입 중 하나일 경우, 모든 타입에 존재하지 않는 속성이 객체에 존재할 거라 보장 X
- 모든 타입에 존재하지 않은 속성에 접근하기 위해 타입을 좁혀야 하는 것처럼 객체 타입 유니언도 타입을 좁혀야한다.


- 타입 검사기가 유니언 타입 값에 특정 속성이 포함된 경우에만 코드 영역을 실행할 수 있음을 알게 되면, 값의 타입을 해당 속성을 포함하는 구성 요소로만 좁힌다.

- 코드에서 객체의 형태를 확인하고 타입 내로잉이 적용된다.


> Typescript는 `if(poem.pages)`와 같은 형식으로 참 여부 확인을 허용하지 않는다. 
- 존재하지 않는 객체의 속성에 접근하려고 시도하면 타입 가드처럼 작동하는 방식으로 사용되더라도 타입 오류로 간주


```js
if(poem.pages) {} 
// ❌ Error : Property 'pages' does not exist on type 'Someone | Stranger'.
//            Property 'pages' does not exist on type 'Someone'. 
```

<br/>



<br/>

### **4.3.4 판별된 유니언**

- `Javascript`와 `Typescript`에서 유니언 타입으로 된 객체의 또 다른 인기 있는 형태는 객체의 속성이 객체의 형태를 나타내도록 하는 것, 이러한 형태를 `판별된 유니언` 이라고 한다.

- 객체의 타입을 가리키는 속성이 판별 값

- `Typescript`는 코드에서 판별 속성을 사용해 타입 내로잉 수행

- 판별된 유니온을 이용해서 타입 내로잉을 하는 것이 좋다.

- 판별된 유니온이란 각 객체마다 각자의 태그를 붙여서 해당 태그로 타입을 구분할 수 있도록 판별하는 것을 의미

```ts
type PoemPage = {
    name: string,
    pages: number
    type: "pages"
}

type PoemRhymes = {
    name: string;
    rhymes: boolean;
    type: "rhymes"
}
type Poem = PoemPage | PoemRhymes;
const poem:Poem = Math.random() > 0.5
    ? {name: "leekoby", pages: 9, type: "pages"}
    : {name: "kimcube", rhymes: true, type: "rhymes"}

if(poem.type === "pages"){
    console.log(`${poem.pages}`)
}else{
    console.log(`${poem.rhymes}`)
}

poem.type; // type : "pages" | "rhymes"
poem.pages; 
// ❌ Error : Property 'pages' does not exist on type 'Poem'.
//         Property 'pages' does not exist on type 'PoemRhymes'.
```
<br/>



## **📖 4.4 교차 타입**

인터섹션(`&`)을 이용하여 각 타입에 선언된 모든 속성이 합집합인 타입이 된다

```ts
type Stranger1 = {
  name: string;
  age: number;
};
type Stranger2 = {
  name: string;
  marriage: boolean;
};

declare const person: Stranger1 & Stranger2;

/*
person ={
    name: string,
    age: number,
    marriage: boolean,
}
*/

person.name;
person.age;
person.marriage;
```
<br/>


교차 타입과 판별된 유니온을 같이 사용한 예시

```ts
type Stranger1 = {
  age: number;
  type: "stranger1"
};
type Stranger2 = {
  marriage: boolean;
  type: "stranger2"
};

declare const person: { name: string } & ( Stranger1 | Stranger2 );

// "name: string"을 가지면서 "Stranger1"인 경우
if(person.type === "stranger1")  {
  person.age;
  person.name;
}
// "name: string"을 가지면서 "Stranger2"인 경우
else {
  person.name;
  person.marriage;
}
```

<br/>

### **4.4.1 교차 타입의 위험성**

교차 타입은 유용한 개념이지만, 혼동을 가져오기 쉽기 때문에 가능한 코드를 간결하게 유지해야한다.


> **🚨 교차 타입을 잘못 사용하면 불가능한 타입인 `never`가 생성될 위험이 있다.**
{:prompt-danger}

- 교차 타입은 잘못 사용하기 쉽고 사용 불가능한 타입을 생성한다.

- 원시 값은 동시에 여러 타입이 될 수 없기에 교차 타입의 구성 요소로 함께 결합할 수 없다.

- 두 개의 원시 타입을 함께 시도하면 `never` 키워드로 표시되는 `never` 타입이 된다.

- `never`는 프로그래밍 언어에서 `bottom` 타입 또는 `empty` 타입을 뜻 한다.

- `bottom` 타입은 값을 가질 수 없고 참조할 수 없는 타입

```ts
// type SthType = never
type SthType = number & string;
```

대부분의 `Typescript` 프로젝트에서는 `never` 타입을 거의 사용하지 않지만 불가능한 상태를 나타내기 위해 가끔 사용한다.


<br/>

# 📚 레퍼런스

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}

> [Typescript 핸드북](https://typescript-kr.github.io/pages/unions-and-intersections.html){:target="_blank"}
{:.prompt-info}
