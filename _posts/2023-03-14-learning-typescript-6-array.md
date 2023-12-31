---
layout: post
title: 러닝타입스크립트 6장 - 배열
author: admin
date: 2023-03-14 00:00:00 +900
lastmod: 2023-03-14 00:00:00 +900
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

# **🌈 6. 배열**

- 자바스크립트의 배열은 매우 유연하고 내부에 모든 타입의 값을 혼합해서 저장할 수 있다.

- 타입스크립트는 초기 배열 데이터 타입을 확인하고, 해당 데이터 타입에서만 작동하도록 제한한다.

```js
//Javascript
const elements = [true, null, undefined, 42];
elements.push("even", ["more"]); 
// element 배열의 값 : [true,null,undefined,42,"even", ["more"]]
```

```ts
//Typescript
const elements = [true, null, undefined, 42];
elements.push("even", ["more"]); 
// ❌ Error : Argument of type '"even"' is not assignable to parameter of type 'number | boolean | null | undefined'.
```

<br/>

## **📖 6.1 배열 타입**

배열을 저장하기 위한 변수는 초깃값이 필요하지 않다.

```ts
let arrayOfNumber: number[];
arrayOfNumber = [1,2,4,8,16]
```

> 배열 타입은 `Array<number>`와 같은 구문으로도 작성할 수 있다. 하지만 좀 더 간결한 `number[]`와 같은 형태를 선호함.

<br/>

### **6.1.1 배열과 함수 타입**

- 배열 타입은 함수 타입에 무엇이 있는지를 구별하는 `괄호`가 필요한 구문 컨테이너의 예이다.

   - `괄호`는 `Annotation`의 어느 부분이 함수 반환 부분이고 어느 부분이 배열 타입 묶음인지 나타내기 위해 사용 

```ts
// 타입은 string 배열을 반환하는 함수
let createStrings: () => string[];

// 타입은 각각의 string을 반환하는 함수 배열
let stringCreator: (()=> string)[];
```

<br/>

### **6.1.2 유니언 타입 배열**

- 배열의 각 요소가 여러 선택 타입 중 하나임을 나타낼 때 유니언 타입 사용

```ts
// 타입은 string 또는 number 배열
let stringOrArrayOfNumber: string | number[];

// 타입은 각각 number 또는 string인 요소의 배열 
let arrayOfStringOrNumber: (string|number) [];
```

<br/>

### **6.1.3 any 배열의 진화**

- 초기에 빈 배열로 설정된 변수에서 타입 애너테이션을 포함하지 않으면 타입스크립트는 배열을 `any[]`로 취급한다.

```ts
// 타입: any[]
let values = [];

// 타입:string[]
values.push("something")

// 타입: (number|string)[]
values[0] = 0;
```

<br/>

### **6.1.4 다차원 배열**

- 2차원 배열 또는 배열의 배열은 두개의 `[ ]`를 갖는다.

- 3차원은 세개의 `[ ]`, 4차원은 네개의 `[ ]` 가 있다.

- 새로운 개념이 아닌 원래 타입을 가지며 끝에 `[ ]`가 있고 그 뒤에 `[ ]`를 추가한다.

```ts
let arr: number[][]

arr = [
    [1, 2, 3],
    [1, 2, 3],
    [1, 2, 3]
]

```

<br/>

---

<br/>

## **📖 6.2 배열 멤버**

- 타입스크립트는 배열의 멤버를 찾아서 해당 배열의 타입 요소를 되돌려주는 인덱스기반 접근 방식을 이해하는 언어다.

```ts
const stringAB = ["aaa", "bbb"]
// 타입은 : string
const strAB = stringAB[0]

//유니언 타입으로 된 배열의 멤버는 그 자체로 동일한 유니언 타입이다.
//const solOrDate: string | Date
const soldierOrDate = ["Deborah", new Date(1782,6,3)]
const solOrDate= soldierOrDate[0]
```

<br/>

### **🚨 6.2.1 주의사항 : 불안정한 멤버**

- 타입스크립트의 타입 시스템은 기술적으로 불안정하다고 알려져 있다.

- 대부분 올바른 타입을 얻을 수 있지만, 때로는 값 타입에 대한 타입 시스템의 이해가 올바르지 않다.

- 배열은 타입 시스템에서 불안정한 소스다.

   - 타입스크립트는 모든 배열의 멤버에 대한 접근이 해달 배열의 멤버를 반환한다고 가정하지만,
   
   - 자바스크립트는 배열의 길이보다 큰 인덱스로 배열 요소 접근 시 `undefined` 를 반환한다.
 
 
- 타입스크립트는 검색된 배열의 멤버가 존재하는지 의도적으로 확인 하지 않는다.

```ts
// 오류를 표시 하지 않는다.
function withElements(element: string[]){
             //element[9001] : string  
  console.log(element[9001].length); // ✅타입오류 없음
}

withElements(["It's","over"])
```

<br/>

---

<br/>

## **📖 6.3 스프레드와 나머지 매개변수**

### **6.3.1 스프레드**

- `spread operator`를 사용해 배열을 결합한다.

- 타입스크립트는 입력된 배열 중 하나의 값이 결과 배열에 포함될 것임을 이해한다.

- 입력된 배열이 동일한 타입이라면 출력 배열도 동일한 타입이다.

- 서로 다른 타입의 두 배열을 함께 스프레드해 새 배열을 생성하면 새 배열은 두 개의 타입 중 어느 하나의 요소인 유니언 타입 배열로 이해된다.

```ts
// 타입: string[]
const strArr = ["A", "B", "C"]
// 타입: number[]
const numArr = [11,22,33]
// 타입: (string|number)[]
const newArr = [...strArr, ...numArr]
```

<br/>

### **6.3.2 나머지 매개변수 스프레드**

- 타입스크립트는 나머지 매개변수로 배열을 스프레드하는 자바스크립트 실행을 인식하고 이에 대해 타입 검사를 수행한다.

- 나머지 매개변수를 위한 인수로 사용되는 배열은 나머지 매개변수와 동일한 배열 타입을 가져야한다.

- 나머지 매개변수로 `string` 값만 받는다면 `string[]` 타입 배열을 스프레드하는 것은 허용되지만, `number[]`는 허용되지 않는다.

```ts
function logWarriors(greeting: string, ...names: string[]) {
    for (const name of names) {
        console.log(greeting + ' ' + name + '!');
    }
}
const warriors = ['Cathay williams', "Lozen", "Nziga"]
logWarriors("Hello", ...warriors);

const birthYear = [1884, 1840, 1583];
logWarriors("Born in", ... birthYear);
// ❌ Error : Argument of type 'number' is not assignable to parameter of type 'string'.
```

<br/>

---

<br/>

## **📖 6.4 튜플**

- 자바스크립트 배열은 이론상 어떤 크기라도 될 수 있다.

- 때로는 `튜플`이라고 하는 고정된 크기의 배열을 사용하는 것이 유용하다.

- 튜플은 각 인덱스에 아렬진 특정 타입을 가지며 배열의 모든 가능한 멤버를 갖는 유니언 타입보다 더 `구체적`이다.

```ts
let ageAndName: [number, string]
ageAndName = [33, "koby"] //✅

ageAndName = [false, 'koby']
// ❌ Error : Type 'boolean' is not assignable to type 'number'.

ageAndName =[555]
// ❌ Error : Type '[]' is not assignable to type '[number, string]'.
//             Source has 0 element(s) but target requires 2.

```

<br/>

### **6.4.1 튜플 할당 가능성**

- 타입스크립트의 튜플 타입은 가변 길이 배열 타입보다 더 구체적으로 처리된다.

- 가변 길이의 배열 타입은 튜플 타입에 할당할 수 없다.

- 튜플은 길이가 다른 튜플은 서로 할당할 수 없다.

- `[boolean, number]`가 있는 것을 볼 수 있지만, 타입스크립트는 더 일반적인`(boolena | number)[]`로 유추한다.

```ts
// 타입 : (string|boolean)[]
const ageAndName = [false, "koby"]
```

#### **나머지 매개변수로서의 튜플**

- 튜플은 구체적인 길이와 요소 타입 정보를 가지는 배열로 간주되므로 함수에 전달할 인수를 저장하는 데 특히 유용하다.

- 타입스크립트는 `spread operator` 나머지 매개변수로 전달된 튜플에 정학환 타입 검사를 제공할 수 있다.

- 나머지 매개 변수 튜플을 사용하고 싶다면 여러 번 함수를 호출하는 인수 목록을 배열에 저장해 함께 사용 가능하다.

```ts
function logTrio(name: string, value: [number, boolean]){
    console.log(name)
    console.log(value[0], value[1])
}

const trios: [string, [number,boolean]][] = [
    ["A", [1,true]],
    ["B", [2,false]],
    ["C", [3,true]]
]


trios.forEach(trio => logTrio(...trio)) // ✅
trios.forEach(logTrio);
// ❌ Error : Argument of type '(name: string, value: [number, boolean]) => void' is not 
//            assignable to parameter of type '(value: [string, [number, boolean]], index: number, array: [string, [number, boolean]][]) => void'.
//            Types of parameters 'name' and 'value' are incompatible.
//            Type '[string, [number, boolean]]' is not assignable to type 'string'.
// [string, [number,boolean]] 전체를 전달하면 매개변수의 타입이 일치하지 않으므로 오류 발생
```

<br/>

### **6.4.2 튜플 추론**

- 타입스크립트로 생성된 배열을 튜플이 아닌 가변 길이의 배열로 취급한다.

- 배열이 변수의 초깃값 또는 함수에 반환값으로 사용되는 경우, 고정된 튜플이 아니라 유연한 크기의 배열로 가정한다.

```ts
// 반환 타입 : (string : number)[]
function firstCharAndSize(input: string) {
    return [input[0], input.length]
}
// firstChar 타입 : string | number
// size 타입 : string | number
const [firstChar, size] = firstCharAndSize("Cookie")

```

- 타입스크립트는 배열 타입 대신 구체적인 튜플 타입이어야 함을 다음 두 가지 방법으로 나타낸다. 

<br/>

#### **명시적 튜플 타입**

- 함수가 튜플 타입을 반환한다고 선언되고, 배열 리터럴을 반환한다면 튜플로 간주된다.

```ts
// 반환 타입 : [string, number]
function firstCharAndSize(input: string) :[string,number] {
    return [input[0], input.length]
}
// firstChar 타입 : string 
// size 타입 : string 
const [firstChar, size] = firstCharAndSize("Cookie")
```

<br/>

#### **const Assertion**

- 타입스크립트는 값 뒤에 넣을 수 있는 `const Assertion`인 `as const` 연산자를 지원한다.

- `const Assertion` 타입스크립트에 타입을 유추할 때 읽기 전용이 가능한 값 형식을 사용하도록 지시

- 배열 리터럴 뒤 `as const`가 배치되면 배열이 튜플로 처리되어야 함을 나타낸다.

```ts
// 타입: (string | number)[]
const unionArr = [1157, "toto"]

// 타입: readonly [1157, "toto"]
const readonlyArr = [1157, "toto"] as const
```

- `const Assertion`은 튜플로 전환을 넘어서 읽기 전용, 값 수정이 예상 되는 곳에서 사용할 수 없음을 나타낸다. 

- 읽기 전용 튜플은 함수 반환에 편리하다.


```ts
//반환 타입: readonly[string, number]
function firstCharAndSizeAsConst(input: string){
    return [input[0], input.length] as const
}
// firstChar : string
// size : number
const [firstChar, size] = firstCharAndSizeAsConst("Cookie")
```

<br/>

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}
