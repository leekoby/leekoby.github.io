---
layout: post
title: 러닝타입스크립트 5장 - 함수
author: admin
date: 2023-03-13 00:00:00 +900
lastmod: 2023-03-13 00:00:00 +900
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

# **🌈 5. 함수**

## **📖 5.1 함수 매개변수**

명시적 타입 정보가 선언되지 않으면 절대 타입을 알 수 없다.

타입스크립트가 이를 `any` 타입으로 간주하며 매개변수의 타입은 무엇이든 될 수 있다.

- 변수와 마찬가지로 타입스크립트를 사용하면 `Type Annotation`으로 함수 매개변수의 타입을 선언 할 수 있다.

- 코드를 유효한 TS 구문으로 만들기 위해 함수 매개변수에 적절한 `Type Annotation`을 추가할 필요는 없다.

- 타입 오류로 오류를 알리지만 이미 시작 된 자바스크립트는 계속 실행된다.

```ts
function buildName(firstName: string, lastName: string) {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // 오류, 너무 적은 매개변수
let result2 = buildName("Bob", "Adams", "Sr.");  // 오류, 너무 많은 매개변수
let result3 = buildName("Bob", "Adams");         // 정확함
```

<br/>

### **5.1.1 필수 매개변수**

- 자바스크립트에서는 인수의 수와 상관없이 함수를 호출 할 수 있다.

- 반면 타입스크립트에서는 함수에 선언된 모든 매개변수가 필수라고 가정한다

- 그렇기 때문에 함수가 잘못된 수의 인수로 호출되면 오류로 이의를 제기한다.

- 함수에 필수 매개변수를 제공하도록 강제하면 예상되는 모든 인숫값을 함수내에 존재하도록 만들어 타입 안정성 강화한다.

<br/>


### **5.1.2 선택적 매개변수** 

- 자바스크립트에서 함수 매개변수가 제공되지 않으면 함수 내부의 인숫값은 `undefined`으로 기본값이 설정된다.

- 매개변수를 제공할 필요가 없을 때도 있고, `undefined` 값을 위해 의도적으로 사용할 수 있다.

- 타입스크립트에서 이러한 매개변수에 인수를 제공하지 못하는 경우, 타입 오류를 보고 하지 않으려면 `?:`를 사용 매개변수가 선택적이라고 표시할 수 있다.

- 함수 호출에 선택적 매개변수를 제공할 필요는 없다.

- 선택적 매개변수에는 항상 `| undefined`가 유니언 타입으로 추가되어 있다.

```ts
function announceSong(song:string, singer?:string) {
    console.log(song);
    if (singer) {
        console.log(singer);
    }

    console.log(singer);
}
announceSong("cookie");
announceSong("cookie", undefined);
announceSong("cookie", "NewJeans");
```

🍀 `?:`와 `| undefined`의 차이

- `?:`는 선택적 매개변수는 항상 암묵적으로 `undefined`가 될 수 있다. 

- `|undefined`는 값이 명시적으로 `undefined`일지라도 제공되어야한다.

- 함수에서 사용되는 모든 선택적 매개변수는 마지막 매개변수여야 한다.

```ts
function announceSong(song?:string, singer : string) { 
  // ❌ Error : A required parameter cannot follow an optional parameter.
```

<br/>

### **5.1.3 기본 매개변수**  

- 자바스크립트에서 선택적 매개변수를 선언할 때 `=`와 같이 값이 포함된 기본값을 제공할 수 있다.

- 선택적 매개변수에는 기본적으로 값이 제공되기 때문에 타입스크립트에서 암묵적으로 함수 내부에 `|undefined` 유니언 타입이 추가된다.

- 타입스크립트의 `Type Inference`은 초기 변숫값과 마찬가지로 기본 함수 매개변수에 대해서도 유사하게 작동한다.

- 매개변수에 기본값이 있고 `Type Annotation`이 없는 경우, 기본 값을 기반으로 매개 변수 타입 유추한다.

```ts
                                // (parameter) rating:number
function rateSong(song: string, rating = 0) {
    console.log(`${song} gets ${rating}/5 star!`)
}

rateSong("Cookie"); // ✅
rateSong("Cookie", 5); // ✅
rateSong("Cookie", undefined) // ✅

rateSong("Header", "100")
// ❌ Error: Argument of type 'string' is not assignable to parameter of type 'number'.
```
- `rating`은 `number` 타입으로 `Inference`되지만, 함수를 호출하는 코드에서 선택적 `number | undefined`가 된다.

<br/>

### **5.1.4 나머지 매개변수**

- 자바스크립트의 일부 함수는 임의의 수의 인수로 호출할 수 있도록 만들어진다.

- `spread operator`는 함수 선언의 마지막 매개변수에 위치하고, 매개변수에서 시작해 함수에 전달된 "나머지" 인수가 모두 단일 배열에 저장되는 것을 의미한다.

- 타입스크립트는 이러한 나머지 매개변수의 타입을 일반 매개변수와 유사하게 선언할 수 있다.

- 인수 배열을 나타 내기 위해 끝에 `[]` 구문이 추가해야 한다.

```ts
const mapping = (...args: number) => args.map(Number);
// ❌ Error : A rest parameter must be of an array type.

const mapping2 = (...args: number[]) => args.map(Number);
```

<br/>

---

<br/>

## 📖 **5.2 반환 타입**

- 타입스크립트는 `지각적`(알아서 깨닫는. 또는 그런 것.)이다. 

- 함수가 반환할 수 있는 가능한 모든 값을 이해하면 함수가 반환하는 타입을 알 수 있다.

```ts
// function singSong(songs:string[]):number
function singSong(songs:string[]){
    return songs.length
}
```

- 위의 예시에서 `number` type을 `return`하는 것으로 추론한다.

- 함수에 다른 값을 가진 여러 개의 반환문을 포함하고 있다면, 타입스크립트는 반환 타입을 가능한 모든 반환 타입의 조합으로 유추된다.

```ts
// function getSongAt(songs: string[], index: number) : string | undefined
function getSongAt(songs: string[], index: number) {
    return index < songs.length
        ? songs[index]
        : undefined
}
```

<br/>

## **5.2.1 명시적 반환 타입**

- 변수와 마찬가지로 타입 애너테이션을 사용해 함수의 반환 타입을 명시적으로 선언하지 않는 것이 좋다.

- 그러나 함수에서 반환 타입을 명식적으로 선언하는 방식이 매우 유용할 때가 있다.

  - 가능한 반환값이 많은 함수가 항상 동일한 타입의 값을 반환하도록 강제한다.
  
  - 타입스크립트는 재귀 함수의 반환 타입을 통해 타입을 유추하는 것을 거부한다.
  
  - 수백 개 이상의 타입스크립트 파일이 있는 매우 큰 프로젝트에서 타입스크립트의 타입 검사 속도를 높일 수 있다.
  
반환문이 반환 타입으로 할당할 수 없는 값을 반환하는 경우 할당 가능성 오류 표시

- 함수 선언 일 때 `{` 앞에 배치

```ts
function test(songs:string[], count =0): number{
    return songs.length ? test(songs.slice(1), count +1) : count;
} 
```

- 화살표 함수 일 때 `=>` 앞에 배치

```ts
const singSongRecursive = (songs: string[], count =0): number =>
    songs.length ? singSongRecursive(songs.slice(1), count +1) : count;
```

<br/>

---

<br/>

## **📖 5.3 함수 타입**

- 자바스크립트에서는 함수를 값으로 전달할 수 있다.

- 즉 함수를 가지기 위한 매개변수 또는 변수의 타입을 선언하는 방법이 필요하다.

함수 타입 구문은 화살표 함수와 유사하지만 함수 본문 대신 타입이 있다.

```ts
//매개 변수가 없고 string 타입을 리턴하는 함수
let nonthingInGivesString: () => string;
```

```ts
// string[] 매개변수, count 선택적 매개변수 , number 값을 반환하는 함수
let inputAndOutput:(songs: string[], count?: number) => number;
```

함수 타입은 콜백 매개변수를 설명하는데 자주 사용된다.

```ts
const songs = ["Juice", "Shake It Off", "What's Up"]

//getSongAt 매개변수의 타입을 index: number를 받고 string을 반환 하는 함수로 선언
function runOnSongs(getSongAt: (index: number) => string){
    for(let i = 0; i < songs.length; i += 1){
        console.log(getSongAt(i));
    }
}

function getSongAt(index: number){
    return `${songs[index]}`
}
//getSongAt을 전달 시 일치
runOnSongs(getSongAt); // ✅


function logSong(song: string){
    return  `${song}`
}

//string 타입을 사용하여 반환값을 가져오는데 실패
runOnSongs(logSong); 
// ❌ Error : Argument of type '(song: string) => string' is not 
// assignable to parameter of type '(index: number) => string'.
```

<br/>

### **5.3.1 함수 타입 괄호**

- 함수 타입은 다른 타입이 사용되는 모든 곳에 배치 가능하다(유니언 타입도 포함).

- 유니언 타입의 `Annotation` 함수 반환 위치를 나타내거나 유니언 타입을 감싸는 부분을 표시할 때 `괄호`를 사용한다.

```ts
//타입은 string | undefined 유니언을 반환하는 함수
let returnStringOrundefinde: () => string | undefined;

// 타입은 undefined 이거나  string을 반환하는 함수
let test: (() => string) | undefined;
```

<br/>

### **5.3.2 매개변수 타입 추론**

- 타입스크립트는 선언된 타입의 위치에 제공된 함수의 매개변수 타입을 유추할 수 있다.

```ts
let singer: (song: string) => string;
//singer 변수는 string의 매개 변수를 갖는 함수로 알려져있다.
// song : string
singer = function (song) {
    return song.toUpperCase();
}
```

- 함수를 매개변수로 갖는 함수에 인수로 전달된 함수는 해당 매개변수 타입도 잘 유추할 수 있다.

```ts
const songs = ["call", "me", "hi"]
//song : string;
//index : number;
songs.forEach((song,index) => {
    console.log(song)
    console.log(index)
})
```

<br/>

## **5.3.3 함수 타입 별칭**

- 함수 타입에서도 동일하게 타입 별칭 사용할 수 있다.

- 매개변수도 함수 타입을 참조하는 별칭을 사용 가능.

- 타입 별칭은 함수 타입에 유용하다.

   - 반복적으로 작성하는 매개변수, 반환 타입을 갖는 코드 공간 절약할 수 있다.
   
```ts
type StringToNumber = (input: string) => number;

let StringToNumber: StringToNumber;

StringToNumber = (input) => input.length;

StringToNumber = (input) => input.toUpperCase(); 
//❌ Error : Type 'string' is not assignable to type 'number'.
```

<br/>

---

<br/>

## **📖 5.4 그 외 반환 타입**

- void
- never

<br/>

### **5.4.1 void 반환 타입**

- 일부 함수는 어떤 값도 반환하지 않는다.

   - `return`이 없는 함수이거나 값을 반환 하지 않는 `return`을 가진 함수일 경우

   - 타입스크립트는 `void` 키워드를 사용해 반환 값이 없는 함수의 반환 타입을 확인 가능
   
```ts
function logSong(song: string | undefined): void{
    if(!song){
        return;
    }
    console.log(song)
    return true; 
    //❌ Error : Type 'boolean' is not assignable to type 'void'.
}
```

- void 사용 시 함수에서 반환되는 모든 값은 무시된다.

```ts
let songLogger: (song: string) => void;

songLogger = (song) => {
    console.log("${songs}");
}

songLogger("CookieCookie"); // Ok;
// 값을 반환하지 않음
```

- 자바스크립트의 함수는 실젯값이 반환되지 않으면 기본으로 모두 `undefined`를 반환한다.

- `void`는 `undefined`와 동일하지 않다. 

- `void`는 함수의 반환 타입이 무시 된다는 것을 의미한다

- `undefined`는 반환되는 리터럴 값이다. 

- `undefined`를 포함하는 대신 `void` 타입의 값을 할당 시 오류 발생

```ts
function returnsVoid(){
    return;
}
let lazyValue: string| undefined;
lazyValue = returnsVoid(); 
//❌ Error : Type 'void' is not assignable to type 'string | undefined'.
```
- `undefinded`와 `void`를 구분해서 사용하는 편히 좋다.

- 특히 `void`를 반환하도록 선언된 타입 위치에 전달된 함수가 **<span style="color:#ff6600">반환된 모든 값을 무시하도록 설정할 때 유용</span>**하다.

```ts
const records: string[] = []
function saveRecords(newRecords: string[]) {
    newRecords.forEach(record => records.push(record));
    saveRecords(["1", "Come On", "Cookie"])
    // forEach는 void를 반환하는 콜백을 받는다.
    // forEach에 제공 되는 함수는 원하는 모든 값을 반환할 수 있다.
    // records.push(record)는 number를 반환 하지만
    // 여전히 newRecords.forEach에 전달된 화살표 함수에 대한 반환값이 허용
}

```

- `void` 타입은 자바스크립트가 아닌 함수의 반환 타입을 선언하는데 사용하는 타입스크립트 키워드다.

- `void` 타입은 함수의 반환값이 자체적으로 반환될 수 있는 값도 아니고, 사용하기 위한 것도 아니라는 표시라는 것을 기억하자.


<br/>

### **5.4.2 never 반환 타입**

- 일부 함수는 값을 반환하지 않을 뿐만 아니라 반환할 생각도 없다.

- `never` 반환 함수는(의도적)오류를 발생 시키거나 무한 루프를 실행하는 함수

- 함수가 절대 반환하지 않도록 의도하려면 `:never` 타입 `Annotation`을 추가해 해당 함수를 호출한 후 모든 코드가 실행되 않음을 나타낸다.

```ts
//fail 함수는 오류만 발생 시키므로
//param 타입을 string으로 좁혀서 타입스크립트의 제어 흐름 분석을 돕는다!
function fail(message: string): never{
    throw new Error(message)
}

function workWithUnsafeParam(param: unknown){
    if(typeof param !== "string"){
        fail("${typeof Param}")
    }
    param.toUpperCase(); // ✅ param은 string 타입
}
```

> 🍀 `never`와 `void`의 차이
> 
> - `void`는 <strong style="color:#3366ff">아무것도 반환하지 않는 함수</strong>를 위한 것
> 
> - `never`은 <strong style="color:#ff6600">절대 반환하지 않는 함수</strong>를 위한 것
{:.prompt-tip}

<br/>

---

<br/>

## **📖 5.5 함수 오버로드**

- 일부 자바스크립트 함수는 선택적 매개변수와 나머지 매개변수만으로 표현할 수 없는 매우 다른 매개변수들로 호출될 수 있다.

- 이러한 함수를 <strong  style="color:#ff6600">오버로드 시그니처<sup> overload signature</sup></strong>라고 불리는 타입스크립트 구문으로 설명 가능하다.

- 하나의 최종 <<strong style="color:#ff6600">구현 시그니처<sup> implementation</sup></strong>와 함수의 본문 앞에 서로 다른 버전의 함수 이름, 매개변수, 반환 타입을 여러 번 선언한다.

- 오버로드된 함수 호출에 대해 구문 오류를 생성할지 여부를 결정할 때 타입스크립트는 함수의 <strong style="color:#3366ff">오버로드 시그니처</strong>만 확인한다.

-  <strong style="color:#3366ff">구현 시그니처</strong>는 함수의 내부 로직에서만 사용된다.

```ts
// 오버로드 시그니처
function creatDate(timestamp: number): Date;
function creatDate(month: number, day: number, year: number): Date;

// 구현 시그니처 
function creatDate(monthOrTimestamp: number, day?: number, year?: number) {
    return day === undefined || year === undefined 
    ? new Date(monthOrTimestamp)
    : new Date(year, monthOrTimestamp, day);
}

creatDate(554356800); // ✅
creatDate(7, 27, 1987); // ✅

creatDate(4, 1); 
// ❌ Error : No overload expects 2 arguments, 
//            but overloads do exist that expect either 1 or 3 arguments.
```
- 다른 파일 시스템 구문처럼 오버로드 시그니처도 지워진다.

>  🚨 함수 오버로드는 복잡하고 설명하기 어려운 함수 타입에 사용하는 최후의 수단이므로 함수를 단순하게 유지하고 가능하면 함수 오버로드를 사용하지 않는 것이 좋다. 
{:.prompt-warning}


<br/>

### **5.5.1 호출 시그니처 호환성**

- 오버로드된 함수의 구현에서 사용되는 구현 시그니처는 매개변수 타입과 반환 타입에 사용하는 것과 동일하다.

- 함수의 오버로드 시그니처에 있는 반환 타입과 각 매개변수는 구현 시그니처에 있는 동일한 인덱스의 매개변수에 할당할 수 있어야 한다. 

- 구현 시그니처는 모든 오버로드 시그니처와 호환 해야한다

```ts
function format(data:string):string // ✅
function format(data:string, needle:string,haystack:string):string // ✅

function format(getData:()=>string):string;
// ❌ Error : This overload signature is not compatible with its implementation signature.

function format(data:string, needle?:string,haystack?:string){
    return needle && haystack ? data.replace(needle,haystack) : data;
}
```

<br/>

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}
