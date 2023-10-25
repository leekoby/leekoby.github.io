---
layout: post
title: 러닝타입스크립트 9장 - 타입제한자
author: admin
date: 2023-03-20 00:00:00 +900
lastmod: 2023-03-20 00:00:00 +900
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

# **🌈 9. 타입제한자**

## **📖 9.1 top 타입**

top 타입은 시스템에서 가능한 모든 값을 나타내는 타입

모든 다른 타입의 값은 타입이 top인 위치에 제공될 수 있다. 

- 즉 모든 타입은 top 타입에 할당할 수 있다. 

<br/>

### **9.1.1 any 다시보기**

`any` 타입은 모든 타입에의 위치에 사용될 수 있다는 점에서 `top` 타입처럼 작동할 수 잇다. 

`any`는 할당 가능성 또는 멤버에 대해 타입 검사를 수행하지 않는다.

즉, 모든 타입에 할당이 가능하면서, 타입스크립트의 도움을 전혀 받을 수 없다.

`any`를 잘못 사용하면 런타임에 오류가 발생하는 경우를 체크할 수 없게 된다.

어떤 값이든 될 수 있음을 나타내려면 `unknown`타입이 훨씬 안전하다. 

```ts
function greet(name:any){
  console.log(`Hi, ${name.toUpperCase()}`)
}

greet({name:'leekoby'})
// ❌ Error : RuntimeError : name.toUpperCase is not a function
```
<br/>

### **9.1.2 unknown** 

타입스크립트에서 `unknown` 타입은 진정한 `top` 타입

`any` 처럼 모든 타입을 전달할 수 있지만, `unknown`타입의 값을 훨씬 더 제한적으로 취급

- `unknown` 타입의 값의 속성에 직접적으로 접근할 수 없음

- `top` 타입이 아닌 타입에서 할당할 수 없음

즉, `unknown`은 어떤 타입이라도 할당될 수 있지만, `unknown` 타입에 접근 할 수 있는 방법은 제한되어 있다. 

- `instanceof`

- `typeof`

- `type assertion` 

- 위와 같이 값의 타입이 제한된 경우 

`any`와는 다르게 사용에 제한이 걸리기 때문에 훨씬 더 안전하다

```ts
function greet(name: unknown) {
  if (typeof name === 'string') {
    console.log(`Hi ${name}`)
  } else {
    console.log(`I'm off`)
  }
}
greet('leekoby') // "Hi leekoby" 
greet({});  // "I'm off" 
```

<br/>

---


<br/>

## **🛡️ 9.2 타입 서술어 (사용자 정의 타입 가드)**

제한된 검사로 `instanceof`, `typeof`를 이용해서 직접 타입을 좁힐 수 있습니다.

하지만 로직을 함수로 감싸면 타입을 좁힐 수 없게 된다. 


```ts
function isNumOrString(value: unknown) {
  return ['number', 'string'].includes(typeof value)
  // value를 인자로 받고 그 값이 number인지 string인지 boolean으로 반환함
}

function logVal(value: number | string | null | undefined) {
  // 타입스크립트는 boolean값을 반환한다는 사실만 알고, 인수의 타입을 좁히기 위함인지는 알 수 없음 
  if (isNumOrString(value)) {
    // value의 타입은 : number | string | null | undefined
    value.toString();
    // ❌ Error : 'value' is possibly 'null' or 'undefined'.
  } else {
    console.log('Value does not exist', value)
  }
}
```

<strong style="color:#3366ff">타입서술어<sup>type predicate</sup> | 사용자 정의 타입 가드<sup>user-defined type guard</sup></strong>

인수가 특정 타입인지 여부를 나타내기 위해 `boolean` 값을 반환하는 함수를 위한 특별한 구문 

타입 서술어의 반환 타입은 매개변수의 이름, `is` 키워드, 특정타입으로 선언할 수 있다.

```ts
const isNum = (value: unknown): value is number => {
  return typeof value === 'number'
}

const serial: unknown = 112389123;

if (isNum(serial)) {
  // v : nunber;
  serial.toFixed();
}
```

타입 서술어는 단순히 `boolean`값만 반환하는 것이 아니라 인수가 더 구체적인 타입임을 나태는 것이라 생각할 수 있다. 

이미 한 인터페이스의 인스턴스로 알려진 객체가 더 구체적인 인터페이스의 인스턴스인지 여부를 검사하는데 자주 사용됨. 

```ts
interface Comedian {
  funny: boolean
}

interface StandupCoemdian extends Comedian {
  routin: string;
}
function isStandupComedian(value: Comedian): value is StandupCoemdian {
  return 'routin' in value
}

function workWithcomedian(value: Comedian) {
  if (isStandupComedian(value)) {
    
    // value : StandupCoemdian 타입
    console.log(value.routin) // ✅
  }
              //value : Comedian
  console.log(value.rotine)

    // ❌ Error : Property 'rotine' does not exist on type 'Comedian'.
}
```

타입서술어는  `false`조건에 타입을 좁히기 때문에 타입 서술어가 입련된 타입 이상을 검사하는 경우 예상치 못한 결과를 얻을 수 있으니 주의하자.  

```ts
function isLongString(input: string | undefined): input is string {
  return !!(input && input.length >= 5);
}

function workWithText(text: string | undefined) {
  if (isLongString(text)) {

    //text: string 타입
    console.log('long text:', text.length)
  } else {

    console.log('short texxt', text?.length);

    // ❌ Error :  Property 'length' does not exist on type 'never'.
  }
}
```

타입 서술어는 속성이나 값의 타입을 확인하는 것 이상을 수행해 잘못 사용하기 쉬우므로 가능하면 피하는 것이 좋다. 

<br/>

---

<br/>

## **📖 9.3 타입 연산자**

키워드나 기존 타입의 이름만 사용해 모든 타입을 나타낼 수는 없다. 

기존 타입의 속성 일부를 변환해 두 타입을 결합하는 새로운 타입을 생성해야할 수도 있다.

<br/>

### **9.3.1 `keyof`**

자바스크립트 객체는 일반적으로 `string` 타입인 동적값을 사용하여 검색된 멤버를 갖는다.

타입 시스템에서 이러한 키를 표현하려면 상당히 까다로울 수 있다. 

`string` 같은 포괄적인 원시 타입을 사용하면 컨테이너 값에 유효하지 않은 키가 허용된다. 

```ts
interface Ratings {
  audience: number;
  critics: number;
}

function getRating(ratings: Ratings, key: string): number {
  return ratings[key]
  // ❌ Error :  
  /*
    Element implicitly has an 'any' type because expression
    of type 'string' can't be used to index type 'Ratings'.
      No index signature with a parameter of type 'string' 
      was found on type 'Ratings'.
   */
}

// ✅
function getRating2(ratings: Ratings, key: keyof Ratings): number {
  return ratings[key]
}

const ratings: Ratings = { audience: 66, critics: 88 };

getRating(ratings, 'audience') // ✅

getRating(ratings, 'not valid') // ❌ 허용되지만 사용하면 안됨
```

다른 옵션은 허용되는 키를 위한 리터럴 유니언 타입을 사용하는 것,

이 경우 컨테이너 값에 존재하는 키만 적절하게 제한하는 것이 정확하다. 

```ts
interface Ratings {
  audience: number;
  critic: number;
}

function getRating(ratings: Ratings, key: 'audience' | 'critic'): number {
  return ratings[key] // ✅
}

const ratings : Ratings = {audience : 66, critic: 84};

getRating(ratings,'audience') // ✅

getRating(ratings , ' not valid')
// ❌ Error : Argument of type '" not valid"' is not 
//            assignable to parameter of type '"audience" | "critic"'.
```

타입스크립트에는 기존에 존재하는 타입을 사용하고, 해당 타입에 허용되는 모든 키의 조합을 반환하는 `keyof` 연산자를 제공한다. 

타입 에너테이션처럼 타입을 사용하는 모든 곳에서 타입 이름 앞에 `keyof` 연산자를 배치

```ts
interface Ratings {
  audience: number;
  critic: number;
}

function getRating(ratings: Ratings, key: keyof Ratings): number {
  return ratings[key] // ✅
}

const ratings : Ratings = {audience : 66, critic: 84};

getRating(ratings,'audience') // ✅

getRating(ratings , ' not valid')
// ❌ Error : Argument of type '" not valid"' is not 
//            assignable to parameter of type '"audience" | "critic"'.
```

`keyof` 는 존재하는 타입의 키를 바탕으로 유니언 타입을 생성 

<br/>

### **9.3.2 `typeof`**

제공되는 `값`의 타입을 반환해주는 연산자

`typeof`는 값의 타입을 직접 작성하는게 복잡할 경우 사용하면 유용하다.

```ts
const original = {
  medium: 'movie',
  title: 'mean Girls'
};

let adaptation: typeof original;

if (Math.random() > 0.5) {
  adaptation = { ...original, medium: ' play' }
} else {
  adaptation = { ...original, medium: 2 }
}         // ❌ Error : Type 'number' is not assignable to type 'string'.
```

자바스크립트의 `typeof` 연산자는 타입에 대한 문자열 이름을 반환

우연히 같은 단어를 사용할뿐, 타입스크립트의 `typeof` 연산자는 타입스크립트에서만 사용할 수 있고 컴파일 된 자바스크립트 코드에는 나타나지 않음

<br/>

### **keyof typeof**

`typeof`는 값의 타입을 검색하고 , `keyof`는 타입에 허용된 키를 검색한다. 

두 키워드를 함께 연결해 값의 타입에 허용된 키를 간결하게 검색할 수 있다. 

두 키워드를 함께 사용하면 `typeof` 연산자를 `keyof` 타입 연산자와 함께 작업할 때 매우 유용하다. 

```ts
const ratings = {
  imdb: 8.4,
  metacritic: 82,
}

function logRaing(key: keyof typeof ratings) {
  console.log(ratings[key]);
}

logRaing('imdb') // ✅

logRaing('invalid');
// ❌ Error : Argument of type '"invalid"' is not assignable
//            to parameter of type '"imdb" | "metacritic"'.
```

`keyof`와 `typeof`를 결합해서 사용하면 명시적 인터페이스 타입이 없는 객체에 허용된 키를 나타내는 타입에 대한 코드를 작성하고 업데이트하는 수고를 줄일 수 있다. 

<br/>

---

<br/>

## **📖 타입 어서션 (타입 단언)**

타입스크립트는 코드가 <strong style="color:#3366ff">`강력하게 타입화`</strong>될 때 가장 잘 작동한다. 

- 코드의 모든 값이 정확히 알려진 타입을 가지는 경우

- 타입 검사기가 복잡한 코드를 이해할 수 있도록 top 타입과 타입가드 기능을 제공 

그러나 경우에 따라 타입을 추론할 수 없는 불가피한 경우가 존재한다.

- `JSON.parse()` 같은 경우는 실제로 실행해보기 전에는 반환 타입을 유추할 수 없기 때문에 `any`를 반환

- `fetch()`나 `catch()`의 첫 번째 인자인 `Error` 객체도 마찬가지.
( `fetch()는 any`, `catch는 unknown` )


타입스크립트는 값의 타입에 대한 타입 시스템의 이해를 재정의하기 위한 구문으로 <strong style="color:#3366ff">타입 어서션<sup>type assertion</sup></strong>을 제공한다. 

다른 타입을 의미하는 값의 타입 다음에 `as` 키워드를 배치한다. 

```ts
const rawData = '["grace","frankie"]';

JSON.parse(rawData);
// 타입 : any

JSON.parse(rawData) as string[];
// 타입 : string[]

JSON.parse(rawData) as [string, string];
// 타입 [string, string]

JSON.parse(rawData) as  '["grace","frankie"]';
// 타입  ["grace","frankie"]

```

타입 어서션은 타입스크립트 타입 시스템에만 존재하며 자바스크립트로 컴파일 될 때 다른 타입 시스템 구문과 함께 제거됨. 

> 타입스크립트 모범 사례는 가능한 한 타입 어서션을 사용하지 않는 것. 

<br/>

### **9.4.1 포착된 오류 타입 어서션**

자바스크립트에서 `try ~ catch`를 사용하는 경우 `Error` 객체를 이용하는 것이 모범 사례지만, 문자열 리터럴 또는 다른 의외의 값을 발생시키기도 한다. 

코드 영역이 Error 클래스의 인스턴스를 발생시킬거라 확신한다면 타입 어서션을 사용해 포착된 어서션을 오류로 처리할 수 있다. 

```ts
try {
  //오류 발생시키는 코드
} catch (error) {
  console.warn((error as Error).message)
}
```

발생된 오류가 예상된 오류 타입인지 확인하기 위해 `instanceof` 검사와 같은 타입 내로잉을 사용하는 것이 안전하다.

```ts
try {
  //오류 발생시키는 코드
} catch (error) {
  console.warn(error instanceof Error ? error.message : error)
}
```

<br/>

### **9.4.2 non-null 어서션**

`!`을 사용해서 `null` 과 `undefined` 타입을 제외시킨다. 

```ts
// 타입 추론 : Date | undefined
let str = Math.random() > 0.5
  ? "string"
  : undefined

str.length
// ❌ Error : 'str' is possibly 'undefined'.

// 타입 string
str as string;
str!;

```

<br/>

### **9.4.3 타입 어서션 주의사항**

`any` 타입과 마찬가지로 타입 어서션은 타입 시스템에 필요한 하나의 도피 수단. 

`any` 타입을 꼭 필요한 경우가 아니라면 가능한 한 사용하지 않아야 한다. 

값의 타입에 대해 어서션하는 것보단 코드를 나타내는 더 정확한 타입을 갖도록 하자. 

어서션은 종종 잘못되기도 한다. 작성 당시에 이미 잘못되었거나 코드베이스가 변경됨에 따라 잘못될 수도 있다. 

```ts
const seasonCounts = new Map([
  ["broad city", 5],
  ["community", 6],
])

// 타입 : string
const knownValue = seasonCounts.get('i like you~')!;

console.log(knownValue.toUpperCase());

```

<br/>

#### **어서션 vs 선언**

어서션을 사용해서 타입을 강제하는 것과 타입 애너테이션을 사용해서 강제하는 것에는 차이가 있다. 

타입 검사기는 변수의 타입 애너테이션에 대한 변수의 초깃값에 대해 할당 가능성을 수행하지만 타입 어서션은 타입 검사 중 일부를 건너뛰도록 명시적으로 지시한다. 

```ts
interface Something {
  name: string;
  age: number;
}

// ❌ Error: Property 'age' is missing in type '{ name: string; }' but required in type 'Something'.
const s1: Something = {
  name: "",
};

const s2 = {
  name: "",
} as Something; // ❌ 허용되지만 런타임 시 오류 발생  
```
타입 애너테이션을 사용하거나 타입스크립트가 초깃값에서 변수의 타입을 유추하도록 하는 것이 바람직하다. 

<br/>

#### **어서션 할당 가능성**

타입 어서션은 일부 값의 타입이 약간 잘못된 상황에서 필요한 작은 도피 수단일 뿐.

타입 중 하나가 다른 타입에 할당 가능한 경우에만 두 타입간의 타입 어서션을 허용 

서로 완전히 관련이 없는 두 타입 사이에 타입어서션이 있는 경우 타입스크립트가 타입 오류를 감지

```ts
// ❌ Error : Conversion of type 'string' to type 'number' may be a mistake 
//            because neither type sufficiently overlaps with the other. 
//             If this was intentional, convert the expression to 'unknown' first.
const a = "b" as number;
```

하나의 타입에서 값을 완전히 관련 없는 타입으로 전환해야 하는 경우 `이중 타입 어서션`을 사용한다.

1. 먼저 값을 `any`나 `unknown`과 같은 `top` 타입으로 전환 

2. 그 결과를 관련 없는 타입으로 전환

```ts
const a = "b" as unknown as number;
```

> 허용은 되지만 이렇게 사용하면 안된다. 
>
> 이중 타입 어서션은 위험하고 코드의 타입이 잘못되었다는 징후
{:.prompt-warning}

<br/>

---

<br/>

## **📖 9.5 const 어서션**

`const` 어서션은 배열, 원시 타입, 값 별칭 등 모든 값을 상수로 취급해야 함을 나타내는 데 사용

특히 `as const`는 모든 타입에 세 가지 규칙을 적용한다

- 배열은 가변 배열이 아니라 읽기 전용 튜플로 취급  

- 리터럴은 일반적인 원시 타입과 동등하지 않고 리터럴로 취급  

- 객체의 속성은 읽기 전용으로 간주 

```ts
// 타입 : (number | string)[]
let a1 = [0, ''];

// 타입 : readonly [0,'']
let a2 = [0, ''] as const


// 타입 : { b: number }
let b1 = { b:0 };

// 타입 : readonly { b: 0 }
let b2 = { b:0 } as const
```

<br/>

### **9.5.1 리터럴에서 원시 타입으로** 

리터럴 값을 일반적인 원시 타입으로 확장하기보다 특정 리터럴로 이해하는 것이 유용할 수 잇다. 

특정 필드가 더 구체적인 리터럴 값을 갖도록 할 때 사용 

```ts
// 타입 () => string
const getName = () => 'leekoby'

// 타입 () => 'leekoby'
const getNameConst = () => 'leekoby' as const
```

```ts
interface Joke {
  quote: string;
  style: 'story' | 'one-liner';
}

function tellJoke(joke: Joke) {
  if (joke.style === 'one-liner') {
    console.log(joke.quote);
  } else {
    console.log(joke.quote.split('\n'))
  }
}
// 타입 : {quote : string; style : 'one-liner'}
const narrowJoke = {
  quote: 'ppap',
  style: 'one-liner' as const
};

tellJoke(narrowJoke) // ✅

// 타입 : {quote : string; style : string}
const wideObject = {
  quote: 'aapa',
  style: 'one-liner'
}

tellJoke(wideObject);
/* ❌ Error : 
  Argument of type '{ quote: string; style: string; }' 
  is not assignable to parameter of type 'Joke'.
  Types of property 'style' are incompatible.
    Type 'string' is not assignable to type '"story" | "one-liner"'.
 */
```

<br/>

### **9.5.2 읽기 전용 객체**

객체에 `const` 어서션을 사용하면 모든 속성이 `readonly`가 되고 값은 구체적인 타입으로 변한다.

배열은 `readonly` 튜플이 되고, 원시 타입은 `리터럴`이 되고, 객체 또한 `readonly`가 된다.

재귀적으로 적용되어 객체 내부의 모든 속성에 적용이 된다.

```ts
function describePreference(preference: 'maybe' | 'no' | 'yes') {
  switch (preference) {
    case 'maybe':
      return 'A';
    case 'no':
      return 'B'
    case 'yes':
      return 'c'
  }
}

const preferenceMutable = {
  movie: 'maybe',
  standup: 'yes'
};

describePreference(preferenceMutable.movie);
// ❌ Error : Argument of type 'string' is not assignable 
//            to parameter of type '"maybe" | "no" | "yes"'.

preferenceMutable.movie = 'no' //✅


// 타입 : readonly {readonly movie: 'maybe', readonly standup: 'yes'}
const preferenceReadOnly = {
  movie: 'maybe',
  standup: 'yes',
} as const

describePreference(preferenceReadOnly.movie) //✅

preferenceReadOnly.movie = 'no'
// ❌ Error : Cannot assign to 'movie' because it is a read-only property.
```

<br/>

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}