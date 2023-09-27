---
layout: post
title: 러닝타입스크립트 7장 - 인터페이스
author: admin
date: 2023-03-15 00:00:00 +900
lastmod: 2023-03-15 00:00:00 +900
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

# **🌈 7. 인터페이스**

- 인터페이스는 연관된 이름으로 객체 형태를 설명하는 또 다른 방법

- `Type Alias`으로 된 객체 타입과 유사하지만 일반적으로 더 읽기 쉬운 메세지, 더 빠른 컴파일 성능, 클래스와의 더나은 상호 운용성을 위해 선호된다. 

<br/>

## **📖 7.1 타입 별칭 vs. 인터페이스**

```ts 
type Example = { 
	born: number;
    name: string;
};

interface Example2{
  born: number;
  name: string;
}
```

- 두 구문은 거의 같다.

- 인터페이스에 대한 타입스크립트의 할당 가능성 검사와 오류 메시지는 객체 타입에서 실행되는 것과 거의 동일하다.


<br/>

### **타입 별칭과 인터페이스의 차이점**

- 인터페이스는 속성 증가를 위해 병합할 수 있다.

  - 내장된 전역 인터페이스 또는 npm 패키지와 같은 외부 코드를 사용할 때 유용하다.

- 인터페이스는 클래스가 선언된 구조의 타입을 확인하는데 사용할 수 있다.

- 인터페이스에서 타입스크립트 타입 검사기가 더 빨리 작동한다.

  - 내부적으로 더 쉽게 캐시할 수 있는 명명된 타입을 선언하기 때문. 

- 인터페이스는 이름 있는(명명된) 객체로 간주되므로 오류 메시지를 좀 더 쉽게 읽을 수 있다.

<br/>

---

<br/>

## **📖 7.2 속성 타입**

- 타입스크립트는 인터페이스가 모델링할 수 있도록 유용한 타입 시스템 도구를 제공한다.

> 속성 타입은 별칭 객체 타입에도 사용할 수 있다.

<br/>

### **7.2.1 선택적 속성**
`?:`로 선택적(optional) 속성을 나타낼 수 있다.

```ts
interface Movie {
  title: string;
  time?: number;
}

const movie:Movie = {
  title: '아저씨',
  time: 2
}

const book : Movie = {
  title: '러닝 타입스크립트',
}
```

<br/>

### **7.2.2 읽기 전용(readonly) 속성**

- 속성 이름 앞에 `readonly` 키워드를 추가해서 재할당하지 못하도록 할 수 있다.

-  인터페이스에서만 사용할 수 있다.

  - 타입스크립트에서만 존재

  - 객체의 인터페이스를 선언하는 위치에서만 사용되고 실제 객체에는 적용되지 않는다.

```ts
interface Page { readonly text: string }

function read(page: Page) {
  // ✅ 속성을 읽기 가능 
  console.log(page.text);

 // text 속성을 수정하는 것은 에러가 발생한다. 
  page.text += "!";
  // ❌ Error : Cannot assign to 'text' because it is a read-only property.
}
```

<br/>

---

<br/>

## **📖 7.2.3 함수와 메서드**

- 메서드 구문: member(): void와 같이 객체의 멤버로 호출되는 함수로 선언

- 속성 구문: member: () => void와 같이 독립 함수와 동일하게 선언

```ts
interface myFunc {
  property:()=>string;
  method():string;
}

const func :myFunc ={
  property: ()=>'',
  method(){
    return "";
  },
}
func.property();
func.method();
```

`?:` 를 사용하여 선택적 속성 사용가능

```ts
interface myFunc {
  property?: () => string;
  method?(): string;
}
```

#### **메서드와 속성의 차이점**

- 메서드는 `readonly`로 선언할 수 없지만 속성은 가능하다.

- 인터페이스 병합은 메서드와 속성을 다르게 처리한다.

- 타입에서 수행되는 일부 작업은 메서드와 속성을 다르게 처리한다.

#### **현 시점에서 추천하는 스타일 가이드**

- 기본 함수가 `this`를 참조할 수 있다는 것을 알고 있다면 메서드 함수 사용(일반적으로 클래스의 인스턴스에서 사용)

- 반대의 경우 속성 함수 사용

<br/>

### **7.2.4 호출 시그니처**

- 인터페이스와 객체 타입은 호출 시그니처로 선언 가능 

- 값을 함수처럼 호출하는 방식에 대한 타입 시스템의 설명

- 호출 시그니처가 선언한 방식으로 호출되는 값만 인터페이스에 할당 가능

- 호출 시그니처는 함수의 매개변수와 반환 타입을 지정

- 콜론(`:`) 대신 화살표 (`=>`)로 표시 

```ts
type FunctionAlias = (input: string) => number;

interface CallSignature {
  (input: string): number;
}
// 타입 : (input: string) => number
const typeFunctionAlias: FunctionAlias = input => input.length; // ✅

// 타입 : (input: string) => number
const typeCallSignature: CallSignature = input => input.length; // ✅
```

호출 시그니처는 사용자 정의 속성을 추가로 갖는 함수를 설명하는데 사용 할 수 있다.

```ts
interface FunctionWithCount {
  count: number;
  (): void;
}

let hasCallCount: FunctionWithCount;

function keepsTrackOfCalls() {
  //property keepsTrackOfCalls.count : number
  keepsTrackOfCalls.count += 1;
  console.log(`I've been called ${keepsTrackOfCalls.count} types!`);
}

keepsTrackOfCalls.count = 0;

hasCallCount = keepsTrackOfCalls; // ✅

function doesNotHaveCount() {
  console.log("No idea!");
}

//number 타입인 count가 존재하지 않음
hasCallCount = doesNotHaveCount;
// ❌ Error : Property 'count' is missing in type 
// '() => void' but required in type 'FunctionWithCount'.
```

<br/>

### **7.2.5 인덱스 시그니처**

- 타입스크립트는 인덱스 시그니처 구문을 제공해 인터페이스의 객체가 임의의 키를 받고,해당 키 아래의 특정 타입을 반환할 수 있음을 나타낸다.

- 자바스크립트 객체 속성 조희는 암묵적으로 키를 문자열로 반환하기 때문에 인터페이스의 객체는 문자열 키와 함께 가장 일반적으로 사용된다.

- 인덱스 시그니처는 일반 속성 정의와 유사하지만, 키 다음에 타입이 있고 `[i: string]:` 같이 배열의 대괄호를 가진다.

```ts
// WordCounts 인터페이스는 number 타입의 값을 갖는 모든 string 키를 허용하는 것으로 선언
// 이런 타입의 객체는 값이 number면 string 키가 아닌 그 어떤 키도 바인딩 할 수 없다.
interface WordCounts {
  [i: string]: number;
}

const counts: WordCounts = {};

counts.apple = 0; // OK
counts.banana = 1; // OK

counts.cherry = false; 
// ❌ Error: Type 'boolean' is not assignable to type 'number'.
```

<br/>

#### **속성과 인덱스 시그니처 혼합**

- 인터페이스는 명시적으로 명명된 속성과 포괄적인 용도의 `string` 인덱스 시그니처를 한 번에 포함할 수 있다.

```ts
//모든 속성을 number 타입으로 선언했고. 추가적으로 Oroonoko 속성이 존재해야한다.
interface HistoricalNovels {
  Oroonoko: number;
  [i: string]: number;
}

const novels: HistoricalNovels = {
  Outlander: 1991,
  Oroonoko: 1688,
};

// Oroonoko 타입이 없다.
const missingOroonoko: HistoricalNovels = {
  Outlander: 1991,
};
// ❌ Error: Property 'Oroonoko' is missing in type '{ Outlander: number; }' 
// but required in type 'HistoricalNovels'.
```

- 속성과 인덱스 시그니처를 혼합해서 사용하는 일반적인 타입 시스템 기법 중 하나는 인덱스 시그니처의 원시 속성보다 명명된 속성에 대해 더 구체적인 속성 타입 리터럴을 사용하는것이다.

```ts
// ChapterStarts를 사용하는 모든 객체의 preface 속성은 반드시 0!
// 다른 모든 속성은 number를 가져야한다.
interface ChapterStarts {
  preface: 0;
  [i: string]: number;
}

const correctPreface: ChapterStarts = {
  preface: 0,
  night: 1,
  shopping: 5,
};

//0이 아니기에 오류 발생
const wrongPreface: ChapterStarts = {
  preface: 1, 
  // ❌ Error: Type '1' is not assignable to type '0'.
};
```

<br/>

#### **숫자 인덱스 시그니처**

- 자바스크립트가 암묵적으로 객체 속성 조회 키를 문자열로 반환하지만 때로는 객체의 키로 숫자만 허용하는 것이 바람직할 수 있다.

- 키로 `string` 대신 `number` 타입을 사용할 수 있지만,

- 명명된 속성은 포괄적인 용도의 `string` 인덱스 시그니처의 타입으로 할당할 수 있어야 한다.

```ts
interface MoreNarrowNumbers {
    [i: number]: string;
    [i: string]: string | undefined;
}

const mixesNumbersAndString: MoreNarrowNumbers = {
    0: '',
    key1: '',
    key2: undefined,
}
interface MoreNarrowStrings {
    [i: number]: string | undefined; 
    // ❌ Error : 'number' index type 'string | undefined' 
    //            is not assignable to 'string' index type 'string'.
    [i: string]: string;
}
```

위 코드오류에 대한 해석 

> 'number' 인덱스 타입 'string | undefined'은(는) 'string' 인덱스 타입 'string'에 할당할 수 없습니다.

이 에러는 객체의 인덱스 타입이 예상하는 타입과 다른 경우 발생한다. 

위의 에러는 객체의 인덱스 타입이 `string`으로 예상되는데, 실제로는 `string | undefined`으로 지정되어 있어서 발생한 것이다.

해결 방법으로는 

- 객체의 인덱스 타입을 `string | undefined`에서 `string`으로 변경하거나, 

- 해당 인덱스에 대한 값을 `undefined`이 아닌 항상 `string`으로 지정해 주는 것이다.

<br/>

#### **7.2.6 중첩 인터페이스**

- 객체 타입도 중첩 될 수 있는 것처럼 인터페이스 타입도 자체 인터페이스 타입 혹은 객체 타입을 속성으로 가질 수 있다.

```ts
//Novel은 setting 속성을 포함한다.
interface Novel {
  author: {
    name: string;
  };
  setting: Setting;
}

interface Setting {
  place: string;
  year: number;
}

let myNovel: Novel;

// OK
myNovel = {
  author: {
    name: "Jane Austen",
  },
  setting: {
    place: "England",
    year: 1812,
  },
};

myNovel = {
  author: {
    name: "Jane Austen",
  },
  setting: {
    // ❌ Error : Property 'place' is missing in type 
    // '{ year: number; }' but required in type 'Setting'.
    year: 1812,
  },
};
```

<br/>

---

<br/>

## **📖 7.3 인터페이스 확장**

- `extends`를 사용하여 다른 인터페이스의 모든 멤버를 복사해서 선언할 수 있는 확장된 인터페이스를 허용한다.

```ts
interface Writing {
  title: string;
}

interface Novella extends Writing {
  pages: number;
}

// OK
let myNovella: Novella = {
  pages: 190,
  title: "Ethan Frome",
};

let missingPages: Novella = {
  // ❌ Error:  Property 'pages' is missing in type 
  //            '{ title: string; }' but required in type 'Novella'.
  title: "Ethan Frome",
};
```

<br/>

### **7.3.1 재정의된 속성**

- 파생 인터페이스는 다른 타입으로 속성을 다시 선언해 기본 인터페이스의 속성을 재정의하거나 대체 할 수 있다.

```ts
interface WithNullableName {
  name: string | null;
}

//null을 허용하지 않도록 적잘하게 재정의
interface WithNoneNullableName extends WithNullableName {
  name: string;
}


// name에는 number | string을 허용 되지 않는다.
//number | string은 string | null에 할당할 수 없기 때문이다.
/*
  ❌ Error :
  Interface 'WithNumericName' incorrectly extends interface 'WithNullableName'.
  Types of property 'name' are incompatible.
  Type 'string | number' is not assignable to type 'string | null'.
  Type 'number' is not assignable to type 'string'.
 */
interface WithNumericName extends WithNullableName {
  name: number | string;
}
```

<br/>

### **7.3.2 다중 인터페이스 확장**

- 타입스크립트의 인터페이스는 여러 개의 인터페이스를 확장해서 선언할 수 있다. 

  - 모든 기본 인터페이스의 모든 멤버들을 받는다. 

```ts
interface GivesNumber {
  giveNumber(): number;
}

interface GivesString {
  giveString(): string;
}

interface GivesBothAndEither extends GivesNumber, GivesString {
  giveEither(): number | string;
}

function useGivesBoth(instance: GivesBothAndEither) {
  instance.giveEither(); // type: string | number
  instance.giveNumber(); // type: number
  instance.giveString(); // type: string
}
```

<br/>

---

<br/>

## **📖 7.4 인터페이스 병합**

### **인터페이스의 중요한 특징**

- 서로 병합하는 능력 
  - 두 개의 인터페이스가 동일한 이름으로 동일한 스코프에 선언된 경우, 
 
  - 선언된 모든 필드를 포함하는 더 큰 인터페이스가 코드에 추가된다.

```ts
interface Merged{
  fromFirst: string;
}

interface Merged {
  fromSecond: number;
}

// 다음과 같음:
interface Merged {
  fromFirst: string;
  fromSecond: number;
}
```

> 🚨 가독성이 나빠지므로 병합은 사용하지 않는 것이 좋다.
> 
> 외부 패키지 또는 내장 전역 인터페이스 보강하는데 유용함
{:.promt-warning}

```ts
interface Window{
  myEnvironmentVariable : string;
}
window.myEnvironmentVariable; //타입 : string
```

<br/>

### **7.4.1 이름이 충돌되는 멤버**

- 병합된 인터페이스는 타입이 다른 동일한 이름의 속성을 여러번 선언할 수 없다. 

- 속성이 이미 인터페이스에 선언되어 있다면 나중에 병합된 인터페이스에서도 동일한 타입을 사용해야 한다. 

```ts
interface InterfaceA{
  same:(input:boolean) => string;
  differenct : (input:string) => string;
}

interface InterfaceA{
  same:(input:boolean) =>string // ✅
  // input의 타입이 다르기 때문에 에러가 발생
  differenct:(input:number) =>string;
  // ❌ Error : Subsequent property declarations must have the same type.  
  //            Property 'differenct' must be of type '(input: string) => string', 
  //            but here has type '(input: number) => string'.
}
```

- 병합된 인터페이스는 동일한 이름과 다른 시그니처를 가진 메서드는 정의할 수 있다. 

  ➡ 메서드에 대한 `함수 오버로드` 발생

```ts
interface InterfaceA {
  differenct(input: string): string;
}

interface InterfaceA {
  differenct(input: number): string;
}
```

<br/>

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}
