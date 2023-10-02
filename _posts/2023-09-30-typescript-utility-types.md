---
layout: post
title: Utility Types
author: admin
date: 2023-09-30 00:00:00 +900
lastmod: 2023-09-30 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT, COMMON] # 대문자로 작성
tags: [typescript, util] # 소문자로 작성
---

> 해당 포스트는 `typescript util`의 사용법에 부족함을 느껴서 필요한 내용을 정리한 포스트입니다.<br/>
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 Utility Types**

## **utility types로 알아보기**

유틸리티 타입은 이미 정의해 놓은 타입을 변환할 때 사용하기 좋은 문법

유틸리티 타입을 꼭 쓰지 않더라도 기존의 인터페이스, 제네릭 등의 기본 문법으로 타입을 변환할 수 있지만 유틸리티 타입을 쓰면 훨씬 더 간결한 문법으로 타입을 정의할 수 있다.

[링크](https://www.typescriptlang.org/docs/handbook/utility-types.html)

```ts
interface Profile{
  name : string,
  age : number,
  married : boolean
}

const leekboy : Profile = {
  namber: 'leekoby',
  age : 33,
  married: false
}
```

<br/>

### **🍳 Partial**

특정 타입의 부분 집합을 만족하는 타입을 정의

```ts
const newKoby = Partial<Profile>{
  name : 'leekoby',
  age : 33
}
```

`Partial` 은 일부분만 필요할 때 필요한 부분만을 쓸 수 있도록 하는 것

🔻 Partial

```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```

<br/>

### **🍳 Pick**

```ts
const newKoby = Pick<Profile, 'name' | 'age'> = {
  name : 'leekoby',
  age : 33
}
```

위의 Partial 과 같은 동작을 한다. 

Profile에서 name과 age를 선택한다. 는 의미 

🔻 Pick

```ts
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```

<br/>

### **🍳 Exclude**

```ts
type A = Exclude<keyof Profile, 'married'>
```

type A는 'name' | 'age'가 된다. 

키 중에서 제외하고 싶은 키를 선택할 수 있다.

```ts
type Animal = 'Cat' | 'Dog' | 'Human'
type Animal2 = Exclude<Animal, 'Human'>
// Animal2 = 'Cat' | 'Dog'
```

```ts
type Exclude<T, U> = T extends U ? never : T;
```

<br/>

### **🍳 Extract**

`Extract`는 `Exclude`의 반대 

Pick 이랑 유사한 느낌

```ts
type Animal = 'Cat' | 'Dog' | 'Human'
type Human = Extract<Animal, 'Human'>
// Human = 'Human'
```

```ts
type Extract<T, U> = T extends U ? T : never;
```

<br/>

### **🍳 Omit**

위에 작성한 Pick을 다음과 같이 수정할 수 있다.

```ts
const newKoby : Pick<Profile, Exclude<keyof Profile, 'married'>> = {
 name : 'leekoby',
  age : 33
}
```

Omit으로 수정해보면 

```ts
const newKoby = Omit<Profile, 'married'> = {
  name : 'leekoby',
  age : 33
}
```

결과는 같으나 방식은 다르다. Profile에서 `married`를 제외한 결과를 나타낸다.

많은 속성 중에서 일부만 제외하고 필요할 때 매우 유용하다.

```ts
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

<br/>

### **🍳 Required**

```ts
interface Profile {
  name?: string,
  age?: number,
  married?:boolean
}

const koby : Required<Profile>{
  name : 'koby',
  // age : 33, ❌ age 속성이 필수 입니다.
  married: false
}
```

옵셔널을 필수 속성으로 만들 수 있다.

```ts
type Required<T> = {
    [P in keyof T]-?: T[P];
};
```

`-?` 옵셔널을 제거해줌

<br/>

### **🍳 Readonly**

```ts
interface Profile {
  name?: string,
  age?: number,
  married?:boolean
}

const koby : Readonly<Profile>{
  name : 'koby',
  age : 33, 
  married: false
}

❌ koby.name = 'leekoby' // 에러 읽기 전용값으로 수정할 수 없음
```

수정할 수 없고 값만 가져다 쓸 수 있게 만듦

```ts
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```

<br/>

### **🍳 Record**

객체를 표현하는 한가지 방법

```ts
interface Obj {
  [key:string] : number;
}

const a: Obj = { a:3, b:5, c:7}

const b: Record<string, number> = { a:3, b:5, c:7}
```

```ts
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```


<br/>

### **🍳 NonNullable**

```ts
type A = string | number | null | undefined | boolean

type B = NonNullable<A>
```

null과 undefined를 제외함

```ts
type NonNullable<T> = T extends null | undefined ? never : T;
```

<br/>

### **🍳 Parameters**

```ts
function zip (x: number, y: string, z: boolean): {x: number, y: string, z :boolean} {
  return {x, y, z}
}

type Params = Parameters<typeof zip> // type Params = [x: number, y: string, z: boolean]

type First = Params[0] //number
type Second = Params[1] //string
type Third = Params[2] //boolean

```

```ts
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;
```

함수에 대한 제한을 둘때 아래와 같이 한다.

```ts
(...args: any) => any
```

infer는 타입스크립트가 추론하도록 하는 것인고, extends에서만 사용할 수 있다.

> 추론 조건 ? 초론 성공 시의 값 : 추론 실패시의 값

<br/>

### **🍳 ReturnType**

위 코드에서 리턴 타입을 추론하도록 수정하려면

```ts
type R<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : never;

type Ret = R<typeof zip> // Ret = {x: number, y: string, z :boolean}
```

```ts
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

<br/>

### **🍳 ConstructorParameters**

> abstract new (...args: any) => any

생성자 제한 형태

```ts
class A {
  a: string;
  b: number;
  c: boolean;
  constructor(a : string, b : number, c : boolean){
    this.a = a;
    this.b = b;
    this.c = c;
  }
}

const c = new A('123',456,true)
type C = ConstructorParameters<typeof A> //typeof 클래스가 생성자
// type C = [a : string, b : number, c : boolean]
```

```ts
type ConstructorParameters<T extends abstract new (...args: any) => any> = T extends abstract new (...args: infer P) => any ? P : never;
```

<br/>

### **🍳 InstanceType**

```ts
class A {
  a: string;
  b: number;
  c: boolean;
  constructor(a : string, b : number, c : boolean){
    this.a = a;
    this.b = b;
    this.c = c;
  }
}

const c = new A('123',456,true)
type I = InstanceType<typeof A>
//type I = A

const a : A = new A('123',456,true) //  인스턴스(new)
```

```ts
type InstanceType<T extends abstract new (...args: any) => any> = T extends abstract new (...args: any) => infer R ? R : any;
```

<br/>

### **🍳 기타**
```ts
/**
 * Convert string literal type to uppercase
 */
type Uppercase<S extends string> = intrinsic;

/**
 * Convert string literal type to lowercase
 */
type Lowercase<S extends string> = intrinsic;

/**
 * Convert first character of string literal type to uppercase
 */
type Capitalize<S extends string> = intrinsic;

/**
 * Convert first character of string literal type to lowercase
 */
type Uncapitalize<S extends string> = intrinsic;

function applyStringMapping(symbol: Symbol, str: string) {
    switch (intrinsicTypeKinds.get(symbol.escapedName as string)) {
        case IntrinsicTypeKind.Uppercase: return str.toUpperCase();
        case IntrinsicTypeKind.Lowercase: return str.toLowerCase();
        case IntrinsicTypeKind.Capitalize: return str.charAt(0).toUpperCase() + str.slice(1);
        case IntrinsicTypeKind.Uncapitalize: return str.charAt(0).toLowerCase() + str.slice(1);
    }
    return str;
}

/**
 * Marker for contextual 'this' type
 */
interface ThisType<T> { }
```
<br/>

---

<br/>

## 📚 **레퍼런스**

> [[리뉴얼] 타입스크립트 올인원 : Part1. 기본 문법편](https://www.inflearn.com/course/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%98%AC%EC%9D%B8%EC%9B%90-1#reviews){:target="_blank"}
{:.prompt-info}

> [[리뉴얼] 타입스크립트 올인원 : Part1. 기본 문법편 강의교안](https://github.com/zerocho/ts-all-in-one){:target="_blank"}
{:.prompt-info}

> [실전 프로젝트로 배우는 타입스크립트](https://www.inflearn.com/course/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%8B%A4%EC%A0%84/dashboard){:target="_blank"}
{:.prompt-info}