---
layout: post
title: 러닝타입스크립트 8장 - 클래스
author: admin
date: 2023-03-19 00:00:00 +900
lastmod: 2023-03-19 00:00:00 +900
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

# **🌈 8. 클래스**

## **📖 8.1 클래스 메서드**

타입스크립트는 독립함수<sup>stand-alone function</sup>를 이해하는 것과 동일한 방식으로 메서드를 이해

매개변수 타입에 타입이나 기본값을 지정하지 않을 경우 

  - `any` 타입을 기본으로 가짐

허용 가능한 수의 인수가 필요 

대부분의 반환 타입 유추 가능 (재귀함수 제외)

```ts
class Greeter {
	greet(name: string) {
		console.log(`${name}!, Hi`);
	}
}

new Greeter().greet("leekoby"); // ✅
new Greeter().greet(); 
// ❌ Error : Expected 1 arguments, but got 0.

```

클래스 생성자는 매개변수와 관련하여 클래스 메서드처럼 취급

타입스크립트는 메서드 호출 시 올바른 타입의 인수가 올바른 수로 제공되는지 확인하기 위해 타입 검사 수행

```ts
class Greeted {
	constructor(message: string) {
		console.log(`${message}!`);
	}
}

new Greeted("Hello"); // ✅
new Greeted();
 // ❌ Error : Expected 1 arguments, but got 0.
```

<br/>

---

<br/>

## **📖 8.2 클래스 속성**

타입스크립트에서 클래스의 속성을 읽거나 쓰려면 클래스에 <strong style="color:#3366ff">명시적으로 선언</strong>해야 한다.

클래스 속성은 인터페이스와 동일한 구문을 사용해 선언한다.

클래스 속성 이름 뒤에는 선택적으로 `Type Annotaion`이 붙는다

존재하지 않는 멤버에 접근하려고 시도하면 타입 오류 발생

```ts
class FieldTrip {
	destination: string;

	constructor(destination: string) {
		this.destination = destination; 
    // ✅ string으로 명시적 Annotation

    this.goal = destination;
     //❌Error:  Property 'goal' does not exist on type 'FieldTrip'.
	}
}

const trip = new FieldTrip("Korea")

trip.destination ; //✅

trip.goal;
// ❌Error : Property 'goal' does not exist on type 'FieldTrip'.
```

<br/>

### **8.2.1 함수 속성**

자바스크립트에는 두 가지 방법으로 클래스의 멤버를 호출 가능한 함수로 선언할 수 있다. 

- `메서드` : 클래스의 프로토타입으로 함수를 할당하는 방식 

  - `맴버 () {}`과 같이 멤버 이름 뒤에 괄호를 붙이는 메서드 접근 방식 
  
  - 모든 클래스는 동일한 함수 정의한다.
  
  - 메서드는 인스턴스끼리 공유하는 함수 
  
  
- `속성` : 클래스의 속성으로 할당하는 방식 

  - 값이 함수인 속성을 선언하는 방식 
  
  - 클래스의 인스턴스당 새로운 함수 생성한다.
  
  - 각 인스턴스가 갖는 독립적인 함수 
  
> why ❓
>
> this로 각자의 맴버에 접근할 수 있는데 굳이 속성으로 선언해야하는 이유는 무엇일까 

```ts
class Car {
  // 메서드로 선언한 방식
  run() { }
  drive: () => void = () => { }
  // 속성으로 선언한 방식 
  move = () => { };
}

const car1 = new Car();
const car2 = new Car();

console.log(car1.run === car2.run); // true
console.log(car1.move === car2.move); // false
console.log(car1.drive === car2.drive) // false

```

함수 속성에는 클래스 메서드와 독립 함수의 동일한 구문을 사용해 매개변수와 반환 타입을 지정할 수 있다.

함수 속성은 클래스 멤버로 할당된 값, 그 값은 함수

```ts
class WithPropertyParameters {
	takesParameters = (input: boolean) => input ? "Yes" : "No" ;
}

const test = new WithPropertyParameters();
test.takesParameters(true); // ✅
test.takesParameters(11); 
// ❌ Error : Argument of type 'number' is
//            not assignable to parameter of type 'boolean'.
```

<br/>

### **8.2.2 초기화 검사**

`strictNullChecks`가 켜져있는 상태라면 초기화 하지 않은 경우 타입에러를 나타낸다.

```ts
class WithValue {
	immediate = 0;
	later: number; // ✅ constructor에서 할당
	maybeUndefined: number | undefined; // ✅ ndefined가 되는 것이 허용
	unused: number; 
	// ❌ Error : Property 'unused' has no initializer and is
	//            not definitely assigned in the constructor.
	
	constructor() {
		this.later = 1;
	}
}
```

만약 `strictNullChecks`를 켜두지 않았다면 타입 체커에서는 통과하지만 런타임에 문제가 되는 동작을 허용할 수 있다.

따라서 `strictNullChecks`는 켜두는 것이 좋다.


#### **확실하게 할당된 속성**

- 엄격한 초기화 검사가 유용한 경우가 대부분

- 클래스 생성자 다음에 클래스 속성을 의도적으로 할당하지 않는 경우가 있을 수도 있다.

- 초기화 검사를 하고 싶지 않은 경우 `Not-null assertion(!)`를 추가해 검사를 비활성화한다.

- 이 경우 사용되기 전 undefined가 된다.

- `Not-null assertion(!)`을 추가하고 속성에 대한 타입 안정성을 줄이는 대신 클래스를 리팩터링해서 `Not-null assertion(!)`이 더 이상 필요하지 않도록 하자.

```ts
class Car {
  name!: string;
}
```

<br/>

### **8.2.3 선택적 속성**

선언된 속성 이름 뒤 `?`를 추가해 속성을 옵션으로 선언

선택적 속성은 `| undefined`를 포함하는 유니언 타입과 거의 동일하게 작동

```ts
class Car {
  brand?: string;
  // constructor () {}
}
const car = new Car();
// Name: string | undefined
const { brand: Brand } = car;
```

<br/>


### **8.2.4 읽기 전용 속성**

 `readonly` 키워드를 추가해 읽기 전용으로 선언할 수 있다.

`readonly` (1)선언된 위치 또는 (2)생성자에서 초기값만 할당 가능

`readonly`는 자바스크립트로 컴파일할 때 삭제된다.

```ts
class Quote {
	// (1)
	// 
	readonly text: string;
	
	// (2)
	constructor(text: string) {
		this.text = text;
	}

	emphasize() {
		this.text += "!!!";
		// ❌ Error : Cannot assign to 'text' because it is a read-only property.
	}
}
```

```ts
class Car {
  // (1)
  readonly brand: string = "HD";

  constructor () {
    // (2)
    this.brand = "Avan";
  }
}

const car = new Car();
```

원시 타입의 초깃값을 갖는 `readonly` 로 선언된 속성은 `Type Inference`가 좀 더 구체적으로 된다. 

```ts
class Car {
  name1 = "HD";
  readonly name2 = "HD";
  readonly name3: string = "HD";

  constructor() {
    // ✅
    this.name1 = "Avan";

    // ❌ Error: Type '"Avan"' is not assignable to type '"HD"'.
    this.name2 = "Avan";

    // ✅
    this.name3 = "Avan";
  }
}

// person.name1: string
// person.name2: "HD"
// person.name3: string
const car = new Car();
```

<br/>

---

<br/>

## **📖 8.3 타입으로서의 클래스**

타입 시스템에서 클래스는 클래스 선언이 런타임 값(클래스 자체), 타입 애너테이션에서 사용할 수 있는 타입을 모두 생성한다.

해당 클래스의 인스턴스가 아니더라도 모든 멤버와 메서드를 갖고 있다면 할당할 수 있다.

- 타입스크립트의 구조적 타이핑으로 선언되는 방식이 아니라 

- 객체의 형태만을 고려하기 때문

```ts
class Car {
	car = "Avt";

	constructor() { }

	run() { }
}

// 클래스를 타입으로 사용
const par: Car = {
	// 인스턴스가 아니더라도 "Person"에 정의된 모든 멤버와 메서드를 갖고 있는 객체라면 할당 가능
	// 형태가 동일하기 때문
	car: "",
	run() { },
	drive() { }
	/* 
	❌ Error : Car 클래스에 할당할 수 있는 값만 할당 가능 
	Type '{ car: string; run(): void; drive(): void; }' is not assignable to type 'Car'.
	Object literal may only specify known properties, and 'drive' does not exist in type 'Car'.
	 */
};
```

<br/>

---

<br/>

## **📖 8.4 클래스와 인터페이스**

`implements`를 사용하면 해당 클래스가 해당 인터페이스를 준수한다고 선언할 수 있다.

타입 검사기에 의해 모든 불일치에 대해서 타입 오류 발생 

```ts
interface Learner {
	name: string;
	study(hours: number): void;
}
class Student implements Learner {
	// ❌ Error : 
	// Class 'Student' incorrectly implements interface 'Learner'.
	// Property 'name' is missing in type 'Student' but required in type 'Learner'.

	// name: string; // 없을 경우 

	constructor(name: string) {
		// this.name = name;
	}

	study(hours: number) {
		for (let i = 0; i < hours; i += 1) {
			console.log("studying");
		}
	}
}
```

```ts
interface Learner {
	name: string;
	study(hours: number): void;
}
class Student implements Learner {
	name: string; 

	constructor(name: string) {
		this.name = name;
	}

	study(hours: number) {
		for (let i = 0; i < hours; i += 1) {
			console.log("studying");
		}
	}
}

class Slacker implements Learner {
	/*
	❌ Error : 
	Class 'slacker' incorrectly implements interface 'Learner'.
    Type 'slacker' is missing the following properties from type 'Learner': name, study
	*/
}
```

<br/>

### **8.4.1 다중 인터페이스 구현**

`implements` 뒤에 `,`를 이용해서 개수 제한 없이 인터페이스를 등록할 수 있다.

해당 클래스는 등록된 모든 인터페이스를 준수해야한다.

```ts
interface Graded {
	grades: number[];
}

interface Reporter {
	report: () => string;
}
class ReprotCard implements Graded, Reporter {
	grades: number[];

	constructor(grades: number[]) {
		this.grades = grades;
	}

	report() {
		return this.grades.join(",");
	}
}
class Empty implements Graded, Reporter { }
/*
❌ Error :
		Class 'Empty' incorrectly implements interface 'Graded'.
  		Property 'grades' is missing in type 'Empty' but required in type 'Graded'.
		
		Class 'Empty' incorrectly implements interface 'Reporter'.
  		Property 'report' is missing in type 'Empty' but required in type 'Reporter'.
 */
```

만약 인터페이스끼리 중복된 부분이 있고 두 인터페이스가 매우 다른 객체 형태를 표현하는 경우 해당 클래스는 정의할 수 없다.

```ts
interface Graded {
	grades: number[];
}

interface Reporter {
	grades: () => number[]
	report: () => string;
}

class ReprotCard implements Graded, Reporter {
	grades: number[];
	/*
	❌ Error :
	Property 'grades' in type 'ReprotCard' is not assignable
	to the same property in base type 'Reporter'.
	Type 'number[]' is not assignable to type '() => number[]'.
	 */

	constructor() {
	}

	report() {
		return this.grades.join(",");
	}
}
```

<br/>

---

<br/>

## **📖 8.5 클래스 확장**

다른 클래스를 확정하거나 하위 클래스를 만드는 자바스크립트 개념에 타입 검사를 추가한다.

```ts
class Teacher {
	teach() {
		console.log("Teaching");
	}
}
class StudentTeacher extends Teacher {
	learn() {
		console.log("Learning");
	}
}
const teacher = new StudentTeacher();
teacher.teach(); // ✅ 기본 클래스에 정의됨
teacher.learn(); // ✅ 하위 클래스에 정의됨

teacher.speak(); 
// ❌ Error: Property 'speak' does not exist on type 'StudentTeacher'.
```

```ts
class Teacher {
	constructor(name: string) {
		this.name = name
	}
	name: string;

	teach() {
		console.log("Teaching...");
	}
}

class StudentTeacher extends Teacher {
	age:number;

	constructor(name:string, age :number){
		super(name);
		this.age = age
	}
	learn() {
		console.log("Learning...");
	}
}

const teacher = new StudentTeacher('PSE', 25);
teacher.teach(); // ✅ 기본 클래스에 정의됨
teacher.learn(); // ✅ 하위 클래스에 정의됨
```

<br/>

### **8.5.1 할당 가능성 확장**

하위 클래스에서 기본 클래스의 멤버나 메서드를 사용할 수 있다.

구조적 타이핑에 따라 기본 클래스의 타입이 하위 클래스의 모든 맴버가 동일하게 존재하는 경우 기본 클래스의 인스턴스를 하위 클래스 대신 사용할 수 있다. 


```ts
class Lesson {
	subject: string;
	constructor(subject: string) {
		this.subject = subject;
	}
}

class OnlineLesson extends Lesson {
	url: string;
	constructor(subject: string, url: string) {
		super(subject);
		this.url = url;
	}
}

let lesson: Lesson;
lesson = new Lesson("coding"); // ✅
lesson = new OnlineLesson("coding", "google.com"); // ✅
lesson.url; 
// ❌ Error : Property 'url' does not exist on type 'Lesson'.

let online: OnlineLesson;
online = new Lesson("coding") 
// ❌ Error : Property 'url' is missing in type 'Lesson' but required in type 'OnlineLesson'.
```

<br/>

### **8.5.2 재정의된 생성자**

하위 클래스는 자체 생성자를 정의할 필요가 없다 

- 자체 생성자가 없는 하위 클래스는 기본 클래스의 생성자를 사용함 

```ts
class Car {
  car: string;
  constructor(car: string) {
    this.car = car;
  }
}
class Brand extends Car {
  brand: string;
  constructor(name: string, brand: string) {
    super(name);
    this.brand = brand;
  }
}
```

<br/>

### **8.5.3 재정의된 메서드 ( Overriding )**

하위 클래스는 기본 클래스의 메서드를 재정의 할 수 있다. 

새 메서드의 타입도 기본 메서드 대신 사용할 수 있어야 한다. 

```ts
class Car {
	name: string;

	constructor(name: string) {
		this.name = name;
	}

	state(message: string) {
		console.log(message)
	}
}

class MotorCycle extends Car {
	age: number;
	wheel?: number
	constructor(name: string, age: number, wheel?: number) {
		super(name);
		this.age = age;
		this.wheel = wheel
	}
	// Overrides
	state(name: string, wheel?: number) {
		console.log(name, wheel)
	}
}
let motor = new MotorCycle('DL', 10, 2)
motor.state('CT100', 2);
```

<br/>

### **8.5.4 재정의된 속성**

재정의된 메서드와 마찬가지로 기본 클래스에서도 사용할 수 있어야 하기 때문에 구조적으로 일치해야 한다.

더 구체적이거나 기본 클래스 속성 타입에서 확장되는 타입으로 만든다. 

```ts
class NumericGrade {
  value = 0;
}

class VagueGrade extends NumericGrade {
  value = Math.random() > 0.5 ? 1 : '...';
  /* ❌ Error :
  Property 'value' in type 'VagueGrade' is 
  not assignable to the same property in base type 'NumericGrade'.
    Type 'string | number' is not assignable to type 'number'.
      Type 'string' is not assignable to type 'number'.
   */
}

const intstance: NumericGrade = new VagueGrade();
// 예상한 타입 : number
// 실제 타입 : number | string
intstance.value
```

<br/>

---

<br/>

## **📖 8.6 추상 클래스** 

일부 메서드에 대한 구현을 하위 클래스가 해당 메서드를 구현하고 싶을 때 유용하다. 

`absctract` 키워드를 추가하여 메서드를 추상화한다.

추상화 메서드는 기본 클래스에서 메서드 본문을 제공하는 것을 건너뛰고 대신 인터페이스와 동일한 방식으로 선언된다. 

```ts
abstract class Developer {
  abstract coding(): void; // 'abstract'가 붙으면 상속 받은 클래스에서 무조건 구현해야 함
  drink(): void {
    console.log('drink sth');
  }
}

class FrontEndDeveloper extends Developer {
  coding(): void {
    // Developer 클래스를 상속 받은 클래스에서 무조건 정의해야 하는 메서드
    console.log('develop web');
  }
  design(): void {
    console.log('design web');
  }
}
const dev = new Developer(); // error: cannot create an instance of an abstract class
const koby = new FrontEndDeveloper();
koby.coding(); // develop web
koby.drink(); // drink sth
koby.design(); // design web
```

<br/>

---

<br/>

## **📖 8.7 멤버 접근성**

타입스크립에는 접근 제한자가 존재한다.

자바스크립트에도 `#`이라는 `private`한 접근 제한자가 존재하지만 `private`와 `#`은 비슷한 역할을 하지만 미묘한 차이가 존재한다. 

- `#`은 컴파일 후에도 사라지지 않는 자바스크립트 고유의 문법 

- `private`은 컴파일 후에 사라지는 타입스크립트 고유의 문법

접근 제한자

- `public` : 어디서나 누구나 접근 가능

- `protected` : 클래스 내부 또는 하위 클래스에서만 접근 가능

- `private` : 클래스 내부에서만 접근 가능

```ts
class Base {
  isPublicImplicit = 0;
  public isPublicExplicit = 1;
  protected isProtected = 2
  private isPrivate = 3;
  #truePrivate = 4;
}
class SubClass extends Base{
  exaple(){
    this.isPublicImplicit; // ✅
    this.isPublicExplicit; // ✅
    this.isProtected; // ✅

    this.isPrivate;
    // ❌ Error : Property 'isPrivate' is private and only accessible within class 'Base'.

    this.#truePrivate;
    // ❌ Error : Property '#truePrivate' is not accessible outside 
    //            class 'Base' because it has a private identifier.
  }
}

new SubClass().isPublicExplicit; // ✅
new SubClass().isPublicImplicit; // ✅

new SubClass().isProtected;

// ❌ Error : Property 'isProtected' is protected 
//            and only accessible within class 'Base' and its subclasses.

new SubClass().isPrivate;
// ❌ Error : Property 'isPrivate' is private and only accessible within class 'Base'.
```


<br/>

### **정적 필드 제한자**

- 자바스크립트는 `static` 키워드를 사용해 클래스 자체에 멤버를 선언 

- 타입스크립트는 `static` 키워드를 단독으로 사용하거나 `readonly`와 접근성 키워드를 함께 사용할 수 있다. 

- 함께 사용할 경우 접근성키워드 > `static` > `readonly` 키워드가 온다. 

- `static` 클래스 필드에 대해 `readonly`와 접근성 제한자를 사용하면 해당 클래스 외부에서 접근되거나 수정되는 것을 제한하는데 유용 

```ts
class Question {
  protected static readonly answer: 'bash';
  protected static readonly prompt = "favorite programming language?";

  guess(getAnswer: (prompt: string) => string) {
    const answer = getAnswer(Question.prompt);
   
    // ✅
    if (answer === Question.answer) {
      console.log('You got it!')
    } else {
      console.log('Try again...')
    }
  }
}

Question.answer;
// ❌ Error : 
// Property 'answer' is protected and only 
// accessible within class 'Question' and its subclasses.
```

<br/>

---

<br/>

## **📚 레퍼런스**

> Goldberg, et al. 러닝 타입스크립트 / 조시 골드버그 지음 ; 고승원 옮김, 2023.
{:.prompt-info}
