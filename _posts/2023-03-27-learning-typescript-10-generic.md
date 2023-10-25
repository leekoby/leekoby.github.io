---
layout: post
title: 러닝타입스크립트 10장 - 제네릭
author: admin
date: 2023-03-27 00:00:00 +900
lastmod: 2023-03-27 00:00:00 +900
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

# **🌈 10. 제네릭**

타입스크립트는 <strong style="color:#ff6600">제네릭</strong>을 사용해 타입 간의 관계를 알아낸다.

타입스크립트에서 함수와 같은 구조체는 <strong style="color:#ff6600">제네릭 타입 매개변수</strong>를 원하는 수만큼 선언할 수 있다.

타입 매개변수는 구조체의 각 인스턴스에 대해 <strong style="color:#ff6600">타입 인수</strong>라고 하는 서로 다른 타입을 제공 할 수 있지만, 타입 인수가 제공된 인스턴스 내에서는 일관성을 유지한다.

타입 매개변수는 `T`나 `U` 같은 단일 문자 이름 또는 `Key`,`Value` 같은 파스칼 케이스 이름을 갖는다.

<br/>

---

<br/>

## **📖 10.1 제네릭 함수**

매개변수 괄호 앞 `< >`로 묶인 타입 매개변수에 별칭을 배치해 함수를 제네릭으로 만든다.

- `input` 매개변수에 대한 타입 `T` 선언

- 타입스크립트는 함수의 반환 타입이 `T`를 유추

- `identity`가 호출 될 때 마다 `T`에 대한 다른 타입을 유추

```ts
// 제네릭 함수 - 함수 선언식 사용
function identity<T>(input: T) {
    return input
}

const a = identity("HI") //const a: "HI"
const b = identity(123) // const b: 123

const identify1 = <T = undefined>(input: T) => input
const identify2 = <T extends undefined>(input: T) => input
const identify3 = <T,>(input: T) => input

// 제네릭 함수 - 화살표 함수 사용
// .tsx 파일에서 jsx 구분과 충돌하므로 일부 제한이 있다. tsconfig 따로 설정해야 한다.
const identify = <T>(input: T) => input // error : Cannot find name 'T'.
```

<br/>

### **10.1.1 명시적 제네릭 호출 타입**

- 제네릭 함수를 호출할 때 타입스크립트는 함수가 호출되는 방식에 따라 타입 인수를 유추한다.

- 명시적으로 타입을 표현하지 않는다면 타입 인수를 유추할 수 없다.

- 기본값이 `unknown`으로 설정되는 것을 피하기 위해 타입스크립트에 해당 타입 인수가 무엇인지 명시적으로 알려주는 <strong style="color:#ff6600">명시적 제네릭 타입 인수</strong>를 사용해 함수를 호출해야한다.

```ts
function logWrapper<T>(callback: (input: T) => void) {
    return (input: T) => {
        console.log(input)
        callback(input)
    }
}

// 타입 : input:string => void
logWrapper((input: string) => {
    console.log(input.length)
})

// 명시적으로 타입을 표현한 곳이 없어 (input : unknown) => void
// Error : 'input' is of type 'unknown'.
logWrapper((input) => { console.log(input.length) })

// Input 제네릭을 위한 명시적 string 함께 제공, 
// 타입스크립트는 Input의 콜백 input 매개변수가 string 타입으로 해석 된다고 유추
// 타입: (input:string) => void
logWrapper<string>((input) => {
    console.log(input.length)
})
```
<br/>

### **10.1.2 다중 함수 타입 매개변수**

- 임의의 수의 타입 매개변수를 쉼표로 구분해 함수를 정의한다.

- 타입 매개 변수 모두 명시적으로 지정하거나 하지않아야한다.

```ts
function makeTuple<First, Second>(first: First, second: Second){
  return [first, second] as const;
}

// ✅ value : readonly [boolean, string] 타입
let tuple = makeTuple(true, "aaa") 

// ✅ 가능 : 두 개의 타입 인수가 제공
makeTuple<string, number>("a",1)

// ❌ Error: 두 개 모두 명시적으로 지정하거나 하지않아야한다.
makeTuple<string>("abc", 123);
```

> 제네릭 구조체에서 두 개보다 많은 타입 매개변수를 사용하지 말자. 
>
> 런타임 함수 매개변수처럼 많이 사용할수록 코드를 읽고 이해하는 것이 어려워진다. 
{:.promt-warning}

<br/>

---

<br/>

## **📖 10.2 제네릭 인터페이스**

인터페이스도 제네릭으로 선언할 수 있다.

- 타입 인수 `Box`로 선언된 객체를 생성하면 `inside`의 `<T>` 속성이 해당 타입 인수와 일치

```ts
interface Box<T> {
    inside: T
}

const stringBox: Box<string> = {
    inside:'abc',
}

const numberBox: Box<number> = {
    inside:123,
}

const incorrectBox: Box<number> = {
    inside:false,
}
// ❌ Error : Type 'boolean' is not assignable to type 'number'.
```

<br/>

### **10.2.1 유추된 제네릭 인터페이스 타입**

제네릭 함수와 마찬가지로 제네릭 인터페이스의 타입 인수는 사용법에서 유추할 수 있다.

타입스크립트는 선언된 위치에 제공된 값의 타입에서 타입 인수를 유추함

```ts
//타입 매개변수 Value 선언
//Value를 node의 매개 변수로 사용
//인수에 전달 된 값의 타입에 따라 Value 유추
interface LinkedNode<Value>{
	next?: LinkedNode<Value>;
	value: Value;
}

function getLast<Value>(node: LinkedNode<Value>): Value{
	return node.next ? getLast(node.next) : node.value;
}

// 유추된 Value 타입 인수: Date
let lastDate = getLast({
    value:new Date('03-27-2023')
})

// // 유추된 Value 타입 인수: string
let lastFruit = getLast({
	next: {
		value: "banana",
	},
	value: "apple",
})

// // 유추된 Value 타입 인수: number
let lastMismatch = getLast({
	next: {
		value: 123,
	},
	value: false,

    // ❌ Type 'boolean' is not assignable to type 'number'.
})
```

인터페이스가 타입 매개변수를 선언하는 경우, 해당 인터페이스를 참조하는 모든 타입 에너테이션은 이에 상응하는 타입 인수를 제공해야 한다.

```ts
interface CrateLike<T> {
    contents: T;
}

// let missingGeneric: CrateLike<string>으로 작성해야함
let missingGeneric: CrateLike = {
    // ❌ Error :Generic type 'CrateLike<T>' requires 1 type argument(s).
    contents: '??'
}
```

<br/>

---

<br/>

## **📖 10.3 제네릭 클래스**

인터페이스처럼 클래스도 나중에 멤버에서 사용할 임의의 수의 타입 매개변수를 선언 가능

클래스의 각 인스턴스는 타입 매개변수로 각자 다른 타입 인수 집합을 가짐

```ts
//Secret 클래스는 Key, Value 타입 매개 변수를 선언
//이를 맴버 속성, 생성자 매개변수 타입, 메서드의 매개변수, 반환타입으로 사용
class Secret<Key, Value>{
    key: Key;
    value: Value;

    constructor(key: Key, value: Value) {
        this.key = key;
        this.value = value;
    }

    getValue(key: Key): Value | undefined {
        return this.key === key
            ? this.value
            : undefined
    }
}
//타입: Secret<number, string>
const storage = new Secret(12345, "abc")

// 타입: string | undefined
const a = storage.getValue(2023); 
```

<br/>


### **10.3.1 명시적 제네릭 클래스 타입**


제네릭 클래스 인스턴스화는 제네릭 함수를 호출하는 것과 동일한 타입 인수 유추 규칙을 따름

- 함수 생성자에 전달된 매개변수의 타입으로부터 타입 인수를 유추할 수 있다면 유추된 타입 사용

- 생성자에 전달된 인수에서 클래스 타입 인수를 유추할 수 없는 경우 타입 인수의 기본값은 `unknown`

```ts
class CurriedCallback<Input>{
	#callback: (input: Input) => void;

	constructor(callback: (input: Input) => void){
		this.#callback = (input: Input) => {
			console.log("Input: ", input);
			callback(input);
		};
	}

	call(input: Input){
		this.#callback(input)
	}
}

	// ✅ 타입:  CurriedCallback<string>
	new CurriedCallback((input: string) => {
		console.log(input.length);
	});

	// // 타입: CurriedCallback<unknown>  타입이 명시되지 않음 
	new CurriedCallback((input) => {
		console.log(input.length) 
    // ❌ Error : input' is of type 'unknown'.
	})

	//클래스 인스턴스는 다른 제네릭 함수 호출과 동일하게 명시적 타입 인수를 제공하여 unknown 방지
	new CurriedCallback<string>((input) => {
		console.log(input.length) 
	})

    new CurriedCallback<string>((input:boolean)=>{
        /*
        Argument of type '(input: boolean) => void' is not 
        assignable to parameter of type '(input: string) => void'.
            Types of parameters 'input' and 'input' are incompatible.
                Type 'string' is not assignable to type 'boolean'.
         */
    })
```

<br/>

### **10.3.2 제네릭 클래스 확장**

제네릭 클래스는 `extends` 키워드 다음에 오는 기본 클래스로 사용 가능

- 기본 클래스에 대한 타입 인수를 유추하지 않음

- 기본 값이 없는 모든 타입 인수는 명시적 타입 애너테이션을 사용해 지정해야한다.

```ts
class Quote<T>{
	lines: T;

	constructor(lines: T){
		this.lines = lines;
	}
}

class SpokenQutoe extends Quote<string[]>{
	speak(){
		console.log(this.lines.join("\n"));
	}
}

new Quote("ABC ABC ACD DDD EEE").lines; // 타입: String
new Quote([4,8,15,16,23,42]).lines; // 타입: number[]

new SpokenQutoe(['Hello','World']); // 타입 : string[]

new SpokenQutoe([4,8,15,17,20,21]);
// ❌ Error: Type 'number' is not assignable to type 'string'.
```

제네릭 파생 클래스는 자체 타입 인수를 기본 클래스에 번갈아 전달 할 수 있다. 

타입 이름은 일치하지 않아도 된다. 

```ts
class Quote<T>{
    lines: T;

    constructor(lines: T) {
        this.lines = lines;
    }
}

class SpokenQutoe extends Quote<string[]>{
    speak() {
        console.log(this.lines.join("\n"));
    }
}

new Quote("ABC ABC ACD DDD EEE").lines; // 타입: String
new Quote([4, 8, 15, 16, 23, 42]).lines; // 타입: number[]

new SpokenQutoe(['Hello', 'World']); // 타입 : string[]

class AtrributeQuote<Value> extends Quote<Value>{
    speaker: string

    constructor(value: Value, speaker: string) {
        super(value);
        this.speaker = speaker
    }
}

// 타입 : AtrributeQuote<string>
// Quote<string> 확장 
new AtrributeQuote('Hello', 'World');

```

<br/>

### **10.3.3 제네릭 인터페이스 구현**

제네릭 클래스는 모든 필요한 매개변수를 제공함으로써 제네릭 인터페이스를 구현한다.

제네릭 인터페이스는 제네릭 기본 클래스를 확장하는 것과 유사하게 작동

기본 인터페이스의 모든 타입 매개변수는 클래스에 선언되어야 한다. 

```ts
interface ActungCredit<T> {
	role: T
}

class MoviePart implements ActungCredit<string>{
	role: string;
	speaking: boolean;

	constructor(role: string, speaking: boolean){
		this.role = role;
		this.speaking = speaking;
	}
}

const part = new MoviePart("HAHA", true)
part.role // 타입 : string


//타입 인수로 string을 제공함
class incorrectEX implements ActungCredit<string>{
	role: boolean; 
    /*
    ❌ Error : Property 'role' in type 'incorrectEX' 
    is not assignable to the same property 
    in base type 'ActungCredit<string>'.
        Type 'boolean' is not assignable to type 'string'.
    */  
}
```

<br/>

### **10.3.4 메서드 제네릭** 

클래스 메서드는 클래스 인스터스와 별개로 자체 제네릭 타입을 선언할 수 있다.

제네릭 클래스 메서드에 대한 각각의 호출은 각 타입 매개변수에 대해 다른 타입 인수를 갖는다.

```ts
// 클래스는 Key 타입을 선언하고 별도의 Value 제네릭 타입을 선언하는 createPair 메서드를 포함한다.
class CreatPairFactory<Key> {
	key: Key;
	constructor(key: Key){
		this.key = key;
	}
  //반환 타입은 {key: Key, value:Value로 유추}
	createPair<Value>(value: Value){
		return {key: this.key, value}
	}
}

// 타입: CreatePairFactory<string>
const factory = new CreatPairFactory("role")
// 타입: {key: string, value: number}
const numberPair = factory.createPair(10);
console.log(numberPair.key, numberPair.value) 
// "role",  10 

// 타입: {key: string, value: string}
const stringPair = factory.createPair('koby')
console.log(stringPair.key, stringPair.value) 
//"role",  "koby" 
```

<br/>

### **10.3.5 정적 클래스 제네릭**

클래스의 정적 멤버는 인스턴스 멤버와 구별되고 클래스의 특정 인스턴스와 연결되어 있지 않다.

클래스의 정적 멤버는 클래스 인스턴스에 접근할 수 없거나 타입 정보를 지정할 수 없다. 

정적 클래스 메서드는 자체 타입 매개변수를 선언할 수 있지만 클래스에 선언된 어떠한 매개변수에도 접근할 수 없다. 

```ts
class BothLogger<OnInstance>{
    instanceLog(value: OnInstance) {
        console.log(value);
        return value;
    }
	//static 정적멤버 
    static staticLog<OnStatic>(value: OnStatic) {
        // ❌ Error : Static members cannot reference class type parameters.
        // 클래스 인스턴스에 대해 OnInstance가 선언되었으므로 접근 불가
        let fromInstance: OnInstance;
    }
}
const logger = new BothLogger<number[]>;
logger.instanceLog([1,2,3])
// 유추된 타입 : number[]

BothLogger.staticLog([false,true]);
// 유추된 타입 : boolean[]


BothLogger.staticLog<string>('HELLO WORLD');
// 유추된 타입 : string
```

<br/>

---

<br/>

## **📖 10.4 제네릭 타입 별칭**

타입 인수를 사용해 제네릭을 만드는 마지막 구조체는 타입 별칭이다.

```ts
//타입 별칭에는 T를 받는 NUllish 타입과 같은 임의의 수의 타입 매개변수가 주어진다.
type NUllish<T> = T| null | undefined;

//제네릭 타입 별칭은 제네릭 함수의 타입을 설명하는 함수와 함께 사용됨
type CreatesValue<Input, Output> = (input: Input) => Output;

//타입: (input: string) 
let creator: CreatesValue<string, number>;

creator = text => text.toUpperCase();
// ❌ Error : Type 'string' is not assignable to type 'number'.
// string 형식을 number타입에 할당 불가능
```

<br/>

### **10.4.1 제네릭 판별된 유니언**

데이터의 성공적인 결과 또는 오류로 인한 실패를 나타내는 제네릭 결과 타입을 만들기 위해 타입 인수를 추가한다.

```ts
type Result<Data> = FailureResult | SuccessfulResult<Data>;

interface FailureResult{
	error: Error;
	succeeded: false;
}

interface SuccessfulResult<Data>{
	data: Data;
	succeeded: true;
}

//타입 내로잉을 위해 succeeded 판별
function handleResult(result: Result<string>){
	if(result.succeeded){
		//result: SuccessfulResult<string> 타입
		console.log(result.data)
	} else{
		// result: FailureResult 타입
		console.error(result.error)
	}
	result.data;
	// ❌ Error: Property 'data' does not exist on type 'Result<string>'.
	// ❌ Error: Property 'data' does not exist on type 'FailureResult'.
}
```

<br/>

---

<br/>

## **📖 10.5 제네릭 제한자**

제네릭 타입 매개변수의 동작을 수정하는 구문도 제공

<br/>

### **10.5.1 제네릭 기본값**

- 제네릭 타입이 타입 에너테이션에 사용되거나 `extends` 또는 `implements`의 기분 클래스로 사용되는 경우 각 타입 매개변수에 대한 타입 인수를 제공해야 한다고 했다.

- 타입 매개변수 선언 뒤 `=` 와 기본 타입을 배치해 타입 인수를 명시적으로 제공할 수 있다.

- 기본 값은 인수가 명시적으로 선언되지 않고 유추할 수 없는 모든 후속 타입에 사용

```ts
interface Qutoe<T = string>{
	value: T;
}

let explicit: Qutoe<number> = {value: 123};

let implicit: Qutoe = {value: "HaHaHa"}

// ❌ Error : Type 'number' is not assignable to type 'string'.
let mismatch: Qutoe = {value: 123};
```

- 타입 매개변수는 동일한 선언 안의 앞선 타입 매개변수를 기본값으로 가질 수 있다. 

- 각 타입 매개변수는 선언에 대한 새로운 타입을 도입하므로 해당 선언 이후의 타입 매개변수에 대한 기본값으로 사용할 수 있다.

```ts
interface KeyValuePair<Key, Value = Key> {
    key: Key;
    value: Value;
}

//타입 KeyValuePair<string, number>
let a: KeyValuePair<string, number> = {
    key: "a",
    value: 10
}
//타입 KeyValuePair<string, string>
let b: KeyValuePair<string> = {
    key: "a",
    value: "b"
}

let incorrect: KeyValuePair = {
    /*
    ❌ Error : Generic type 'KeyValuePair<Key, Value>' 
               requires between 1 and 2 type arguments.
     */
    
    key: 'rating',
    value: 10,
}
```

모든 기본 타입 매개변수는 기본 함수 매개변수처럼 선언 목록의 제일 마지막에 와야한다. 

기본값이 없는 제네릭 타입은 기본값이 있는 제네릭 타입 뒤에 오면 안된다. 

```ts
function inTheEnd<First, Second, Third = number, Fourth = string>() { } //✅

function inTheMiddle<First, Second = string, Third = number, Fourth>() { } 
// ❌ Error : Required type parameters may not follow optional type parameters.
```

<br/>

---

<br/>

## **📖 10.6 제한된 제네릭 타입**

- 기본적으로 제네릭 타입에는 클래스, 인터페이스, 원시값, 유니언, 별칭 등 모든 타입을 제공할 수 있다. 

- 일부 함수는 제한된 타입에서만 작동해야한다.

- 타입 매개변수를 제한하는 구문은 매개변수 이름 뒤에 `extends` 키워드를 배치하고 그 뒤에 이를 제한할 타입 작성한다.

```ts
interface WithLength {
    length: number;
}

function logWithLength<T extends WithLength>(input: T) {
    console.log(input.length)
    return input
}

logWithLength("abcabc") //타입 string
logWithLength([false, false]) //타입: boolean[]
logWithLength({ length: 123 }) // 타입 : {length: number}
logWithLength(new Date())
// ❌ Error :
//     Argument of type 'Date' is not assignable to parameter of type 'WithLength'.
//     Property 'length' is missing in type 'Date' but required in type 'WithLength'.

```

<br/>

### **10.6.1 keyof와 제한된 타입 매개변수** 

- `keyof` 연산자는 제한된 타입 매개변수와도 잘 작동

- `extends`와 `keyof`를 함께 사용하면 타입 매개변수를 이전 타입 매개변수의 키로 제한 가능

- 제네릭 타입의 키를 지정하는 유일한 방법

```ts
// T로 입력된 container 값과 container에서 검색할 수 있는 T의 key 중 하나의 key 이름을 받는다.
// Key 타입 매개변수는 keyof T로 제한되어 이 함수가 T[Key]를 반환 할 수 있음을 안다.
function get<T, Key extends keyof T>(container: T, key: Key){
	return container[key]
}

const roles = {
	favorite: "abc",
	others: ["aaa", "bbb", "ccc"],
};

const favorite = get(roles, "favorite") //타입 string
const others = get(roles, "others") //타입: string[]

const errors = get(roles, "extras") 
// ❌ Error : Argument of type '"extras"' is not 
//            assignable to parameter of type '"favorite" | "others"'.
```

`keyof` 가 없었다면 `key`매개변수를 올바르게 입력할 방법이 없다. 

```ts
function get<T>(container: T, key: keyof T) {
    return container[key]
}
const roles = {
    favorite: 'abc',
    others: ["aaa", "bbb", "ccc"],
}

// 타입 string | string[]
const found = get(roles, "favorite")
```

<br/>

---


<br/>

## **📖 10.7 Promise**

네트워크 요청과 같이 요청 이후 결과를 받기까지 대기가 필요

- resolve (성공적으로 완료)

- reject (실패) 

임의의 값 타입에 대해 유사한 작업을 나타내는 Promise의 기능은 타입스크립트의 제너릭과 잘 맞는다.

Promise는 타입스크립트의 타입 시스템에서 최종적으로 resolve된 값을 나타내는 단일 타입 매개변수를 가진 `Promise 클래스`로 표현된다. 

<br/>

### **10.7.1 Promise 생성**

- 타입스크립트에서 Promise 생성자는 단일 매개변수를 받도록 작성된다. 

- 결과적으로 값을 resolve 하려는 Promise를 만들려면 Promise의 타입 인수를 명시적으로 선언해야 한다.

- 명시적 제네릭 타입 인수가 없다면 기본적으로 매개변수 타입을 `unknown`으로 가정한다. 

```ts
//type : const unknown1: Promise<unknown>
const unknown1 = new Promise((resolve) => {
	setTimeout(() => resolve("Done"), 1000);
})

//const string2: Promise<string>
const string2= new Promise<string>((resolve) => {
	setTimeout(() => resolve("Done"), 1000);
})
```

Promise의 제네릭 `.then` 메서드는 반환되는 Promise의 resolve된 값을 나타내는 새로운 타입 매개 변수를 받는다.

```ts
// 타입 : Promise<string>
const textEx = new Promise<string>((resolve) =>{
	setTimeout(() => resolve("Done"), 1000);
})

// 타입 : Promise<number>
const lengthEx = textEx.then((text)=> text.length)
```

<br/>

### **10.7.2 async 함수**

`async` 키워드를 사용해 선언한 모든 함수는 Promise 반환

```ts
//async 함수이고 직접 number를 반환하기에 Promise<number>를 반환하는 것으로 간주

// 타입 : (text:string) => Promise<number>
async function lengthAfterSecond(text: string) {
	await new Promise((resolve) => setTimeout(resolve, 1000))
	return text.length;
}

// 타입 : (text:string) => Promise<number>
async function lengthImmediately(text: string) {
	return text.length;
}
```

Promise를 명시적으로 언급하지 않더라도 `async` 함수에서 수동으로 선언된 반환 타입은 항상 Promise 타입이 된다. 

```ts
async function givesPromiseForString(): Promise<string> {
	return "Done!"
}

async function givesString(): string {
// ❌ Error : The return type of an async function 
//             or method must be the global Promise<T> type.
	return "Done!"
}
```

<br/>

---

<br/>

## **📖 10.8 제네릭 올바르게 사용하기**

- 제네릭은 코드에서 유연성을 제공할 수 있지만 복잡해질 수 있다.

- 모범사례는 필요할 때만 제네릭을 사용하고 무엇을 위해 사용하는지 명확히 해야한다.

> 타입스크립트로 작성하는 대부분의 코드에서는 혼동을 일으킬 정도로 제네릭을 많이 사용해서는 안된다. 
>
> 그러나 유틸리티 라이브러리에 대한 타입, 특히 범용 모듈은 경우에 따라 제네릭을 많이 사용할 수도 있다.
>
> 제네릭을 이해하면 이러한 유틸리티 타입을 효과적으로 사용할 수 있다. 


<br/>

### **10.8.1 제네릭 황금률** 

- 함수에 타입 매개변수가 필요한지 여부를 판단할 수 있는 간단하고 빠른 방법은 타입 매개변수가 최소 두 번 이상 사용되었는지 확인하는 것

- 제네릭은 타입 간의 관계를 설명하므로 제네릭 타입 매개변수가 한 곳에만 나타나면 여러 타입 간의 관계를 정의 할 수 없다.

- 각 함수 타입 매개변수는 매개변수에 사용되어야 하고, 그 다음 적어도 하나의 다른 매개변수 또는 함수의 반환 타입에서도 사용되어야 한다.

```ts
// input 매개변수를 선언하기 위해 Input 타입 매개변수를 정확히 한번 사용!
// logInput은 타입 매개변수로 더많은 매개변수를 반환하거나 선언하는 작업X
// 따라서 Input 타입 매개변수를 선언하는 것은 별로 쓸모가 없다.
function logInput<Input extends string>(input: Input) {
}

//이렇게 다시 작성할 수 있다.
function logInput2(input: string) {
	console.log("Hi", input);
}
```

<br/>

### **10.8.2 제네릭 명명 규칙**

- 타입 매개 변수에 대한 표준 명명 규칙은 기본적으로 첫번째 타입 인수로 `T`, 후속 타입 매개변수 존재 시 `U`,`V`등을 사용하는 것

- 타입 인수가 어떻게 사용되어야 하는지 맥락과 관련된 정보가 알려진 경우 명명 규칙은 경우에 따라 용어의 첫 글자를 사용하는 것으로 확장됨.

- 상태 관리 라이브러리에서는 제네릭 상태를 `S`로, 데이터 구조의 키와 값은 `K`와 `V`로 나타냄

제네릭의 의도가 단일 문자 T에서 명확하지 않은 경우, 사용되는 용도를 가리키는 설명적인 제네릭 타입 이름을 사용하는 것이 가장 좋다

```ts
function<Label, Value>(label: Label, value: Value) {}
```

구조체가 여러 개의 타입 매개변수를 갖거나 단일 타입 인수의 목적이 명확하지 않을 때마다 단일 문자 약어 대신 가독성을 위해 완전히 작성된 이름을 사용하는 것이 좋다. 

<br/>

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}