---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 10.
author: admin
date: 2023-10-08 09:00:00 +900
lastmod: 2023-10-08 09:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [FUNCTIONAL PROGRAMMING, 쏙쏙 들어오는 함수형 코딩]  # 대문자로 작성
tags: [functional programming, 쏙쏙 들어오는 함수형 코딩] # 소문자로 작성
---

![쏙쏙 들어오는 함수형 코딩](https://github.com/leekoby/leekoby.github.io/assets/118284808/7e31456f-1763-4786-bef8-b3ae98921901){: width="500" height="500" }

> 해당 포스트는 `쏙쏙 들어오는 함수형 코딩`을 학습하며 필요한 내용을 정리한 포스트입니다.<br/>
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 10. 일급 함수 1**

이번 장에서 살펴볼 내용

- 왜 일급 값이 좋은지

- 문법을 일급 함수로 만드는 방법

- 고차 함수로 문법을 감싸는 방법

- 일급 함수와 고차 함수를 사용한 리팩터링 두 개를 살펴보자.

## **💻 요약**

### **🍳 코드의 냄새: 함수 이름에 있는 암묵적 인자**

이 코드의 냄새는 일급 값으로 바꾸면 표현력이 더 좋아진다.

함수 본문에서 사용하는 어떤 값이 함수 이름에 나타난다면 **함수 이름에 있는 암묵적 인자**는 코드의 냄새가 된다.

➡ 리팩터링으로 해결할 수 있다.

특징

1. 거의 똑같이 구현된 함수가 있다.

2. 함수 이름이 구현에 있는 다른 부분을 가리킨다.

<br/>

### **🍳 리팩터링: 암묵적 인자를 드러내기**

함수 이름에 있는 암묵적 인자를 어떻게 명시적인 함수 인자로 바꿀 수 있을까?

**암묵적 인자를 드러내기** 리팩터링은 암묵적 인자가 일급 값이 되도록 함수에 인자를 추가한다. 

이렇게 하면 잠재적 중복을 없애고 코드의 목적을 더 잘 표현할 수 있다.

단계

1. 함수 이름에 있는 암묵적 인자를 확인한다.

2. 명시적인 인자를 추가한다.

3. 함수 본문에 하드 코딩된 값을 새로운 인자로 바꾼다.

4. 함수를 호출하는 곳을 고친다.

<br/>

### **🍳 리팩터링: 함수 본문을 콜백으로 바꾸기**

언어 문법 중 어떤 문법은 일급이 아니다. 

**함수 본문을 콜백으로 바꾸기** 리팩터링으로 함수 본문에 어떤 부분(비슷한 함수에 있는 서로 다른 부분)을 콜백으로 바꾼다. 

이렇게 하면 일급 함수로 어떤 함수에 동작을 잘 전달할 수 있다. 

이 방법은 원래 있던 코드를 고차 함수로 만드는 강력한 방법이다.

단계

1. 함수 본문에서 바꿀 부분의 앞부분과 뒷부분을 확인한다.

2. 리팩터링 할 코드를 함수로 빼낸다.

3. 빼낸 함수의 인자로 넘길 부분을 또 다른 함수로 빼낸다.

<br/>

---

<br/>

## **💻 코드의 냄새: 함수 이름에 있는 암묵적 인자**

🔻 예시 

```js
function setPriceByName(cart, name, price) {
            //문자열이 함수 이름에 그대로 들어있고 비슷한 구조를 가지고 있다.
            // 이 함수들은 인자의 문자열만 다르다.
  let item = cart(name);
  let newItem = objectSet(item, 'price', price);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setQuantityByName(cart, name, quant) {
  let item = cart(name);
  let newItem = objectSet(item, 'quantity', quant);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setShippingByName(cart, name, ship) {
  let item = cart(name);
  let newItem = objectSet(item, 'shipping', ship);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}

function setTaxByName(cart, name, tax) {
  let item = cart(name);
  let newItem = objectSet(item, 'tax', tax);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}
```

여기서 발생하는 코드냄새는 분명하게 알 수 있는 중복 문제다. 함수가 거의 똑같이 생겼다.

다른 하나는 함수들의 차이점. 즉, 필드를 결정하는 문자열이 함수 이름에 있따는 것이다. 

함수 이름에 있는 일부가 인자처럼 동작하는 것 같다.

> <strong  style="color:#3366ff">함수 이름에 있는 암묵적 인자 냄새</strong>
>
> 함수 구현이 거의 똑같으며, 함수의 이름만이 구현의 차이를 보인다.
>
> 함수 이름에서 서로 다른 부분이 암묵적 인자다.
>
> 즉, 인자나 이름만 다르고 중복되는 코드는 문제를 가져올 수 있다.
{:.prompt-info }

<br/>

---

<br/>

## **💻 리팩터링: 암묵적 인자를 드러내기**

### **🍳 암묵적 인자를 드러내기 리팩터링**

암묵적 인자를 명시적 인자로 바꾸는 것. 

드러낸다라는 의미가 암묵적인 것을 명시적으로 바꾼다는 의미

단계

1) 함수 이름에 있는 암묵적 인자를 확인

2) 명시적 인자 추가

3) 함수 본문에 하드 코딩된 값을 새로운 인자로 변경

4) 함수를 부르는 곳을 고침

🔻 리팩터링 전

```js
function setPriceByName(cart, name, price) {
  // -> price 는 암묵적 인자
  let item = cart(name);
  let newItem = objectSet(item, 'price', price);
                            // -> price 는 암묵적 인자
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}

// 호출 하던 곳
setPriceByName(cart, 'shoe', 13);
setQuantityByName(cart, 'shoe', 3);
setShippingByName(cart, 'shoe', 0);
setTaxByName(cart, 'shoe', 2.34);
```

🔻 리팩터링 후

```js
function setFieldByName(cart, name, field, value) {
                        // field, value로 명시적인 인자를 추가한다.
  let item = cart(name);
  let newItem = objectSet(item, field, value);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}

// 새로운 인자 사용
setFieldByName(cart, 'shoe', 'price', 13);
setFieldByName(cart, 'shoe', 'quantity', 3);
setFieldByName(cart, 'shoe', 'shipping', 0);
setFieldByName(cart, 'shoe', 'tax', 2.24);
```

리팩터링으로 필드명을 일급 값으로 만들었다. 

리팩터링 전에는 필드명이 함수 이름에 암묵적으로 있었다.

암묵적인 이름은 인자로 넘길 수 있는 값이 되었고 값은 변수나 배열에 담을 수 있다. 그래서 `일급`이라고 부른다.

일급값은 언어 전체에서 `어디서나 쓸 수 있다.`

<br/>

---

<br/>

## **💻 일급인 것과 일급이 아닌 것을 구별하기**

### **🍳 일급이 아닌 것과 일급인 것이 섞여있다.**

일급인 것과 일급이 아닌 것을 구별하고 일급으로 바꾸는 방법을 아는 것이 중요하다.

<br/>

### **🍳 자바스크립트에서 일급이 아닌 것**

1. 수식 연산자

2. 반복문

3. 조건문

4. try/catch 블록

<br/>

### **🍳 일급으로 할 수 있는 것**

1. 변수에 할당

2. 함수의 인자로 넘기기

3. 함수의 리턴값으로 받기

4. 배열이나 객체에 담기

<br/>

---

<br/>

## **💻 필드명을 문자열로 사용하면 버그가 생기진 않을까?**

문자열에 오타가 있으면 어떻게 될까?

이를 해결하기 위한 방법으론 컴파일 타임에 검사하는 것과 런타임에 검사하는 것

컴파일 타임에 검사하는 방법은 정적 타입 시스템에서 사용하는 방법 - typescript

런타임 검사는 컴파일 타임에 동작하지 않는다. 함수를 실행할 때마다 동작

🔻 런타임 검사 방법

```js
let validItemFields = ['price', 'quantity', 'shipping', 'tax']

function setFieldByName(cart, name, field, value) {
  if(!validItemFields.includes(field)){
    throw `유효하지 않은 field명 : ${field}`
  }
  let item = cart(name);
  let newItem = objectSet(item, 'field', value);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}

function objectSet(object, key, value){
  let copy = Object.assign({},object);
  copy[key] = value;
  return copy;
}
```

<br/>

---

<br/>

## **💻 일급 필드를 사용하면 API를 바꾸기 더 어려울까?**

필드명은 계속 유지해야 한다. 하지만 구현이 외부에 노출된 것은 아님.

만약 내부에서 정의한 필드명이 바뀌더라도 사용하는 사람들이 원래 필드명을 그대로 사용할 수 있게 하고, 내부에서 수정해줄 수 있다.

예를 들어 `quantity` 필드명이 `number` 라는 이름으로 바뀌었다고 해보면

```js
let validItemFields = ['price', 'quantity', 'shipping', 'tax']
let translations = {'quantity' : 'number'}

function setFieldByName(cart, name, field, value) {
  if(!validItemFields.includes(field)){
    throw `유효하지 않은 field명 : ${field}`
  }

  if(translations.hasOwnProperty(field)){
    field = translations[field]
  } // 원래 필드명을 새로운 필드명으로 단순히 변경해줌

  let item = cart(name);
  let newItem = objectSet(item, 'field', value);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}
```

<br/>

---

<br/>

## **💻 객체와 배열을 너무 많이 쓰게 된다.**

장바구니와 제품처럼 일반적인 엔티티는 객체와 배열처럼 일반적인 데이터 구조를 사용해야 한다.

데이터를 데이터 그대로 사용하는 것의 중요한 장점은 여러 가지 방법으로 해석할 수 있다는 것이고, 이것이 데이터 지향이라고 하는 중요한 원칙이다.

데이터 지향은 이벤트와 엔티티에 대한 사실을 표현하기 위해 일반 데이터 구조를 사용하는 프로그래밍 형식

<br/>

---

<br/>

## **💻 어떤 문법이든 일급 함수로 바꿀 수 있다**

수식 연산자는 변수에 할당할 수 없으나 함수를 만들 수 있다.

```js
function plus(a, b) {
  return a + b;
}
```

반복문을 직접 만들면 오류가 많이 생기지 않을까?

➡ 그럼 반목문을 일급으로 만들면 될 것 같다. 
➡ 다른 말로 고차 함수(higher-order function)를 만든다고 한다.

일급이며 고차며 이게 무슨말이죠?

➡ 일급은 인자로 전달할 수 있다.
➡ 고차라는 말은 함수가 다른 함수를 인자로 받을 수 있다.
➡ 일급 함수가 없다면 고차 함수를 만들 수 없다.

복문을 다시 만들지 않도록 할 수 있을까?

➡ 본문을 `콜백`으로 바꾸는 리팩터링을 하면 된다.

<br/>

---

<br/>

## **💻 반복문 예제: 먹고 치우기**

배열을 순회하는 일반적인 반복문을 보자. 

첫 번째 반복문은 음식을 준비하고 먹는 일을 하고

두 번째 반복문은 지저분한 식기를 설거지한다.

```js
//준비하고 먹기
for(let i = 0; i < foods.length; i++){
  let food = foods[i];
  cook(food);
  eat(food);
}

//설거지 하기 
for(let i = 0; i < dishes.length; i++){
  let dish = dishes[i];
  wash(dish);
  dry(dish);
  putAway(dish);
}
```

코드가 비슷하지만 두 반복문이 하는 일이 다르다. 

최대한 문법적으로 비슷한 부분을 찾아 하나로 만들어보자.

먼저 코드를 함수로 만들어보자

```js
//준비하고 먹기
function cookAndEatFoods(){
  for(let i = 0; i < foods.length; i++){
    let food = foods[i];
    cook(food);
    eat(food);
  }
}

cookAndEatFoods();

//설거지 하기 

function cleanDishes(){
  for(let i = 0; i < dishes.length; i++){
    let dish = dishes[i];
  wash(dish);
  dry(dish);
  putAway(dish);
  }
}

cleanDishes();
```

지역변수의 이름이 매우 구체적이므로, 좀 더 일반적인 이름으로 바꿔보면 

```js
//준비하고 먹기
...
    let food = foods[i];
...

//설거지 하기 
...
  let item = dishes[i];
...
```

함수에 이름에서도 암묵적 인자 냄새가 난다.

배열이름 또한 마찬가지.

암묵적 인자 드러내기 리팩터링을 해보면

```js
//준비하고 먹기
function cookAndEatArray(array){
  // 일반적인 이름의 함수명
  // 명시적인 배열 인자
  for(let i = 0; i < array.length; i++){
    let item = array[i];
    cook(item);
    eat(item);
  }
}

cookAndEatFoods(foods);
//인자 전달

//설거지 하기 

function cleanArray(array){
  // 일반적인 이름의 함수명
  // 명시적인 배열 인자
  for(let i = 0; i < array.length; i++){
    let item = array[i];
  wash(item);
  dry(item);
  putAway(item);
  }
}

cleanDishes(dishes);
//인자 전달
```

반복문 안에 있는 본문을 분리하는 일만 남았다.

본문 부분만 다르고 나머지는 동일한 상태.

본문을 분리에 함수로 빼내보면

```js
//준비하고 먹기
function cookAndEatArray(array){
  for(let i = 0; i < array.length; i++){
    let item = array[i];
    cookAndEat(item); // 빼낸 함수 호출
  }
}

function cookAndEat(food){
    cook(food);
    eat(food);
}

cookAndEatFoods(foods);

//설거지 하기 

function cleanArray(array){
  for(let i = 0; i < array.length; i++){
    let item = array[i];
    clean(item);// 빼낸 함수 호출
  }
}

function clean(dish){
  wash(dish);
  dry(dish);
  putAway(dish);
}

cleanDishes(dishes);
```

아직 함수 이름에서 암묵적 인자 냄새가 난다.


```js
//준비하고 먹기
function operateOnArray(array, func){
  for(let i = 0; i < array.length; i++){
    let item = array[i];
    func(item); 
  }
}

function cookAndEat(food){
    cook(food);
    eat(food);
}

function clean(dish){
  wash(dish);
  dry(dish);
  putAway(dish);
}

operateOnArray(foods, cookAndEat);
operateOnArray(dishes, clean);
```

원래 코드와 같아졌다. 다른 부분은 모두 인자로 빼냈다. 

> <strong  style="color:#3366ff">리팩터링 단계</strong> 
> 
><strong  style="color:#ff6600">1. 함수 이름에 있는 암묵적 인자를 확인한다.</strong>
> 
><strong  style="color:#ff6600">2. 명시적인 인자를 추가한다.</strong>
> 
><strong  style="color:#ff6600">3. 함수 본문에 하드 코딩된 값을 새로운 인자로 바꾼다.</strong>
> 
><strong  style="color:#ff6600">4. 함수를 호출하는 곳을 고친다.</strong>
{:.prompt-info }

<br/>

---

<br/>

## **💻 리팩터링: 함수 본문을 콜백으로 바꾸기**

1) 본문과 본문의 앞부분과 뒷부분을 구분한다.
2) 전체를 함수로 빼낸다.
3) 본문 부분을 빼낸 함수의 인자로 전달한 함수로 바꾼다.

많은 부분에서 사용되는 try/catch 문 콜백으로 바꿔보기 

```js
try{
  saveUserData(user);
} catch (error) {
  logToSnapErrors(error);
}

try{
  fetchProduct(ProductId);
} catch (error) {
  logToSnapErrors(error);
}
...
```

🔻 함수로 빼낸 코드

```js
function withLogging(){
  try{
    saveUserData(user);
  } catch (error) {
    logToSnapErrors(error);
  }
}

withLogging(); // 함수를 만들고 호출
```

🔻 콜백으로 빼내기

```js
function withLogging(func){
  try{
    func() // 인자로 전달받은 함수 호출
  } catch (error) {
    logToSnapErrors(error);
  }
}

withLogging(function(){ // 본문 전달
    saveUserData(user); // 한줄짜리 익명 함수
});
```

> <strong  style="color:#3366ff">함수 본문을 콜백으로 바꾸기 단계</strong>
>  
> <strong  style="color:#ff6600">1. 본문과 본문의 앞부분과 뒷부분을 구분한다.</strong>
>  
> <strong  style="color:#ff6600">2. 전체를 함수로 빼낸다.</strong>
>  
> <strong  style="color:#ff6600">3. 본문 부분을 빼낸 함수의 인자로 전달한 함수로 바꾼다.</strong>
{:.prompt-info }

<br/>

---

<br/>

## **💻 이건 무슨 문법인가?**

### **🍳 전역으로 정의하기**

함수를 전역적으로 정의하고 이름 붙일 수 있다.

가장 많이 쓰는 방법

```js
function saveCurrentUserDate(){ // 전역으로 함수 정의
  saveUserData(user);
}

withLoggin(saveCurrentUserDate); // 함수에 다른 함수 전달
```

<br/>

### **🍳 지역적으로 정의하기**

지역 범위 안에서 정의하고 이름을 붙일 수 있다.

이름을 가지고 있지만, 범위 밖에서는 사용할 수 없다.

지역적으로 쓰고 싶지만 이름이 필요할 때 유용

```js
function someFunction(){
  let saveCurrentUserData = function (){ 
    // 지역 범위에서만 쓸 수 있는 이름을 붙임
    saveUserData(user);
  };
withLoggin(saveCurrentUserDate); // 함수에 다른 함수 전달
}
```

<br/>

### **🍳 인라인으로 정의하기**

함수를 사용하는 곳에서 바로 정의할 수 있다. 

함수를 변수 같은 곳에 넣지 않기 때문에 이름이 없다.

`익명함수`라고 부른다. 문맥에서 한 번만 쓰는 짧은 함수에 쓰면 좋다.

```js
withLoggin(function(){saveUserData(user);}); 
          // 이름이 없음       //쓰는 곳에서 바로 함수를 정의
```

> <strong  style="color:#3366ff">인라인 함수(inline function)</strong> 
> 
> <strong  style="color:#ff6600">쓰는 곳에서 바로 정의하는 함수</strong>
> 
> 예를 들어, 인자 목록에서 바로 정의하는 함수가 인라인 함수
{:.prompt-info }

> <strong  style="color:#3366ff">익명 암수(anonymous function)</strong> 
> 
> <strong  style="color:#ff6600">이름이 없는 함수</strong>
> 
> <strong  style="color:#ff6600">익명 함수는 보통 필요한 곳에서 인라인(inline)으로 정의한다.</strong>
{:.prompt-info }

<br/>

---

<br/>

## **💻 왜 본문을 함수로 감싸서 넘기는거지**

```js

function withLoggin(func){
  try {
    func();
  } catch (error) {
    logToSnapError(error);
  }
}

withLoggin(function(){saveUserData(user);});  
          // 왜 함수를 감싸서 넘겼을까?
```

이 코드는 try/catch로 감쌀 수도 있고, 함수를 만들어 감쌀 수도 있다. 

그렇게 하는 이유는 함수가 바로 실행되면 안되기 때문이다.

감싼 함수를 호출하기 전까지 실행되지 않게 하기 위해서

<br/>

---

<br/>

## **💻 결론 및 요약**

- 일급 값은 변수에 저장할 수 있고 인자로 전달하거나 함수의 리턴값으로 사용할 수 있다. 일급 값은 코드로 다룰 수 있는 값이다.

- 언어에는 일급이 아닌 기능이 많다. 일급이 아닌 기능은 함수로 감싸 일급으로 만들 수 있다.

- 어떤 언어는 함수를 일급 값처럼 쓸 수 있는 일급 함수가 있다. 일급 함수는 어떤 단계 이상의 함수형 프로그래밍을 하는 데 필요하다.

- 고차 함수는 다른 함수에 인자로 넘기거나 리턴값으로 받을 수 있는 함수다. 고차 함수로 다양한 동작을 추상화할 수 있다.

- `함수 이름에 있는 암묵적 인자`는 함수의 이름으로 구분하는 코드의 냄새다. 이 냄새는 코드로 다룰 수 없는 함수 이름 대신 일급 값인 인자로 바꾸는 `암묵적 인자를 드러내기 리팩터링`을 적용해서 없앨 수 있다.

- 동작을 추상화하기 위해 `본문을 콜백으로 바꾸기 리팩터링`을 사용할 수 있다. 서로 다른 함수의 동작 차이를 일급 함수 인자로 만든다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


