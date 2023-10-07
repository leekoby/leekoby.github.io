---
layout: post
title: 6. 변경 가능한 데이터 구조를 가진 언어에서 불변성 유지하기
author: admin
date: 2023-10-07 00:00:00 +900
lastmod: 2023-10-07 00:00:00 +900
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

# **🌈 6. 변경 가능한 데이터 구조를 가진 언어에서 불변성 유지하기**

이번 장에서 살펴볼 내용

- 데이터가 바뀌지 않도록 하기 위해 카피-온-라이트를 적용
- 배열과 객체를 데이터에 쓸 수 있는 카피-온-라이트 동작을 만든다.
- 깊이 중첩된 데이터도 카피-온-라이트가 잘 동작하게 만든다.

## **💻 동작을 읽기, 쓰기 또는 둘 다로 분류하기**

동작을 읽기 또는 쓰기 또는 둘 다 하는 것으로 분류할 수 있다. 

장바구니 동작

- 제품 개수 가져오기 ➡ 읽기
- 제품 이름으로 제품 가져오기 ➡ 읽기
- 제품 추가하기 ➡ 쓰기
- 제품 이름으로 제품 빼기 ➡ 쓰기
- 제품 이름으로 제품 구매 수량 바꾸기 ➡ 쓰기

제품에 대한 동작

- 가격 설정하기 ➡ 쓰기
- 가격 가져오기 ➡ 읽기
- 이름 가져오기 ➡ 읽기

읽기 동작은 데이터를 바꾸지 않고 정보를 꺼내는 것이다.

쓰기 동작은 어떻게든 데이터를 바꾼다. 바뀌는 값은 어디서 사용될지 모르기 때문에 바뀌지 않도록 원칙이 필요하다.

쓰기 동작은 불변성 원칙에 따라 구현해야 한다. 여기서 불변성 원칙은 `카피-온-라이트(copy-on-write)` 라고 한다.

JavaScript에서는 기본적으로 변경 가능한 데이터 구조를 사용하기 때문에 불변성 원칙을 적용하려면 직접 구현해야 한다.

<br/>

---

<br/>

## **💻 카피-온-라이트 원칙 세 단계**

1. 복사본 만들기

2. 복사본 변경하기 (원하는 만큼)

3. 복사본 리턴하기

전역변수를 변경하는 동작을 모두 카피-온-라이트로 바꾸면 데이터가 더 이상 변경되지 않고 불변 데이터로 동작하게 될 것이다.

지난장에서 카피-온-라이트로 구현한 예제를 보자.

```js
function add_elment_last(array, elem) { // 배열을 변경하려고 한다.
  let new_array = array.slice(); // 1. 복사본 만들기
  new_array.push(elem); // 2. 복사본 바꾸기
  return new_array; // 3. 복사본 리턴하기
}
```

이 함수는 카피-온-라이트로 구현되었다.

- 배열을 복사했고 기존 배열을 변경하지 않았다.

- 복사본은 함수 범위에 있기 때문에 다른 코드에서 값을 바꾸기 위해 접근할 수 없다.

- 복사본을 변경하고 나서 함수를 리턴한다. 이후에는 값을 바꿀 수 없다.

데이터를 바꾸지 않고 정보를 리턴했기 때문에 읽기다. 

카피-온-라이트로 동작을 구현하면 쓰기를 읽기로 바꿀 수 있다.

<br/>

---

<br/>

## **💻 카피-온-라이트로 쓰기를 읽기로 바꾸기**

제품 이름으로 장바구니에서 제품을 빼는 함수가 있다.

```js
function remove_item_by_name(cart, name) {
  let idx = null;
  for(let i = 0; i < cart.length; i++){
    if(cart[i].name === name)
      idx = i;
  }
  if(idx !== null)
     cart.splice(idx, 1); // 장바구니 변경
}
```

이 함수는 splice() 메소드를 통해 장바구니를 변경한다.

만약 장바구니가 바뀌지 않았으면 하고, 장바구니를 변경 불가능한 데이터로 쓰려고하면 어떻게 바꿀 수 있을까

> **<span style="color:#3366ff">카피-온-라이트 규칙</span>**
> 
> **<span style="color:#ff6600">1. 복사본 만들기</span>**
> 
> **<span style="color:#ff6600">2. 복사본 변경하기(원하는 만큼)</span>**
> 
> **<span style="color:#ff6600">3. 복사본 리턴하기</span>**
{:.prompt-info }


### **🍳 1. 복사본 만들기**

```js
function remove_item_by_name(cart, name) {
  let new_cart = cart.slice()// 복사한 데이터를 지역변수에 할당 
  let idx = null; 
  for(let i = 0; i < cart.length; i++){
    if(cart[i].name === name)
      idx = i;
  }
  if(idx !== null)
     cart.splice(idx, 1); // 장바구니 변경
}
```

<br/>

### **🍳 2. 복사본 변경하기**

```js
function remove_item_by_name(cart, name) {
  let new_cart = cart.slice()// 복사한 데이터를 지역변수에 할당 
  let idx = null; 
  for(let i = 0; i < new_cart.length; i++){// 복사한 데이터 사용
    if(new_cart[i].name === name)// 복사한 데이터 사용
      idx = i;
  }
  if(idx !== null)
     new_cart.splice(idx, 1); // 복사한 데이터의 장바구니 변경
}
```

<br/>

### **🍳 3. 복사본 리턴하기**

```js
function remove_item_by_name(cart, name) {
  let new_cart = cart.slice()// 복사한 데이터를 지역변수에 할당 
  let idx = null; 
  for(let i = 0; i < new_cart.length; i++){// 복사한 데이터 사용
    if(new_cart[i].name === name)// 복사한 데이터 사용
      idx = i;
  }
  if(idx !== null)
    new_cart.splice(idx, 1); // 복사한 데이터의 장바구니 변경
  return new_cart; // 복사본 반환 
}
```

<br/>

### **🍳 전역 변수 변경**

```js
function delete_handler(name){
  remove_item_by_name(shopping_cart, name);
  ...
}
```
위의 코드가 현재의 코드다.

그러나 이 함수안에서 전역변수를 변경하고 있다는 것을 위 코드만 보고는 알 수가 없다.

카피-온-라이트를 적용하여 작성한 코드는 원본 데이터는 변경하지 않고, 원본 데이터를 복사한 데이터를 변경하고 있다.

```js
function delete_handler(name){
  shopping_cart = remove_item_by_name(shopping_cart, name);
  ...
}
```
그래서 위처럼 복사한 데이터를 반환하는 함수의 값을 변수에 할당해주어야 한다. 그리고 이 함수를 보면 전역변수를 바꾸고 있다는 것을 한 눈에 알 수 있다.

<br/>

---

<br/>

## **💻 앞에서 만든 카피-온-라이트 동작은 일반적입니다.**

### **🍳 일반화 해보기**

🔻 원래 코드 

```js
function removeItems ( array, idx, count) {
  array.splice(idx, count)
}
```

🔻 카피-온-라이트를 적용한 코드 

```js
function removeItems ( array, idx, count) {
  let copy = array.slice();
  copy.splice(idx, count);
  return copy;
}
```

고친 `removeItems()` 함수에 맞게 `remove_item_by_name()` 함수를 수정

🔻 원래 카피-온-라이트 버전 

```js
function remove_item_by_name(cart, name) {
  let new_cart = cart.slice(); // 복사한 데이터를 지역변수에 할당
  let idx = null;
  for(let i = 0; i < new_cart.length; i++){ // 복사한 데이터 사용
    if(new_cart[i].name === name) // 복사한 데이터 사용
      idx = i;
  }
  if(idx !== null)
     new_cart.splice(idx, 1); // 복사한 데이터의 장바구니 변경
  return new_cart; // 복사본 반환
}
```

🔻 splice()를 사용한 카피-온-라이트 버전

```js
function remove_item_by_name(cart, name) {
  let idx = null;
  for(let i = 0; i < cart.length; i++){ 
    if(cart[i].name === name)
      idx = i;
  }
  if(idx !== null)
    return removeitems(cart, idx, 1);
  return cart; 
}
```

재사용할 수 있도록 일반화하면 귀찮은 일이 많이 줄어든다.

더 이상 배열이나 객체를 복사하는 코드 패턴을 반복해서 쓰지 않아도 된다.

<br/>

---

<br/>

## **💻 쓰기를 하면서 읽기도 하는 동작은 어떻게 할까요?**

어떤 동작은 읽고 변경하는 일을 동시에 한다. 이런 동작은 값을 변경하고 리턴한다.

`.shift()` 메소드가 좋은 예인데 이런 동작을 카피-온-라이트로 어떻게 바꿀 수 있을까?

1. 읽기와 쓰기 함수로 각각 분리한다.

2. 함수에서 값을 두 개 리턴한다.

> **<span style="color:#3366ff">두 가지 접근 방법</span>**
> 
> **<span style="color:#ff6600">함수를 분리하기</span>**
> 
> **<span style="color:#ff6600">값을 두 개 리턴하기</span>**
{:.prompt-info }

<br/>

---

<br/>

## **💻 쓰면서 읽기도 하는 함수를 분리하기**

1. 쓰기에서 읽기를 분리
2. 쓰기에 카피-온-라이트를 적용해서 읽기로 바꾸기

### **🍳 읽기와 쓰기 동작으로 분리하기**

`.shift()` 메서드의 읽기는 단순히 값을 리턴하는 동작, `.shift` 메서드가 반환하는 값은 배열의 첫 번째 요소다.

따라서 배열의 첫 번째 요소를 반환하는 계산함수를 만든다.

```js
function first_element(array){
  return array[0]; 
  // 단순히 배열에 첫 번째 항목(비어있다면 undefined)을 리턴하는 함수 
  // 이 함수는 계산이다.
}
```

`first_element()` 함수는 배열을 바꾸지 않는 읽기 함수이기 때문에 카피-온-라이트를 적용할 필요가 없다.

쓰기 분리 과정은 다음과 같다.

```js
function drop_first(array){
  array.shift();
  //.shift()를 실행하고 결괏값은 무시
}
```

```js
function drop_first(array){
  var array_copy = array.slice();
  array_copy.shift();
  return array_copy;
}
```

읽기와 쓰기를 분리하는 접근 방법은 분리된 함수를 따로 쓸 수 있기 때문에 더 좋은 접근 방법

<br/>

---

<br/>

## **💻 값을 두 개 리턴하는 함수로 만들기**

### **🍳 동작을 감싸기**

새로운 함수로 감싸는 것.

여기서는 함수 리턴값을 무시하면 안된다.

```js
function shift(array){
  return array.shift();
}
```

<br/>

### **🍳 읽기 함수로 바꾸기**

인자를 복사한 후에 복사한 값의 첫 번째 항목을 지우고, 지운 첫 번째 항목과 변경된 배열을 함께 리턴하도록 수정한다.

```js
function shift(array) {
  let array_copy = array.slice();
  let first = array_copy.shift();
  return {
    first : first,
    array : array_copy
  }
}
```

<br/>

### **🍳 다른 방법**

위에서 나눈 함수를 합쳐 값을 두 개 리턴하도록 만들 수도 있다.

```js
function shift(array) {
  return {
    first : first_element(array),
    array : drop_first(array)
  }
}
```

<br/>

---

<br/>

## **💻 불변 데이터 구조를 읽는 것은 계산입니다.**

변경 가능한 데이터를 읽는 것은 `액션`

- 변경 가능한 값을 읽을 때마다 다른 값을 읽을 수도 있다.

쓰기는 데이터를 변경가능한 구조로 만든다

- `쓰기는 데이터를 바꾸기 때문에` 데이터를 변경 가능한 구조로 만든다.

어떤 데이터에 쓰기가 없다면 데이터는 변경 불가능한 데이터

- 쓰기를 모두 없앴다면 데이터는 생성 이후에 바뀌지 않는다.
- 따라서 `불변 데이터`

불변 데이터 구조를 읽는 것은 계산

- 어떤 `데이터를 불변형`으로 만들었다면 그 데이터에 `모든 읽기는 계산`

쓰기를 읽기로 바꾸면 코드에 계산이 많아진다. 

- 데이터 구조를 불`변형으로 만들수록` 코드에 더 많은 계산이 생기고` 액션은 줄어든다.`

<br/>

---

<br/>

## **💻 불변 데이터 구조는 충분히 빠르다.**

불변 데이터 구조는 변경 가능한 데이터 구조보다 메모리를 더 많이 쓰고 느리다. 

그러나 불변 데이터 구조를 사용하면서 대용량의 고성능 시스템을 구현하는 사례도 많다. 

이런 사례가 많다는 건 불변 데이터도 일반 앱이 쓰기 충분히 빠르다는 증거이다.

- 불변 데이터 쓰면 좋은 점

  - 언제든 최적화 가능하다.

  - 가비지 콜렉터는 매우 빠르다.

  - 생각보다 많이 복사하지 않는다.

  - 함수형 프로그래밍 언어에는 빠른 구현체가 있다.

<br/>

---

<br/>

## **💻 객체에 대한 카피-온-라이트**

지금까지는 데이터가 배열이라는 가정 아래 `.slice()` 로 데이터를 복사했다.

객체 데이터에 카피-온-라이트를 적용하는 방법도 배열과 같다.

- 복사본 만들기

- 복사본 변경하기

- 복사본 리턴하기

객체를 복사하려면 `Object.assign({}, object)` 를 사용하면 된다.

이를 이용하여 제품 가격을 설정하는 함수를 작성하면 아래와 같다.

```js
let obj = {a: 1, b: 2};
let obj_copy = Object.assign({}, obj)

// 이전 코드
function setPrice(item, new_price) {
	item.price = new_price;
}

// 카피-온-라이트
function setPrice(item, new_price) {
  	let item_copy = Object.assign({}, item);
	item_copy.price = new_price;
  	return item_copy;
}
```

<br/>

---

<br/>

## **💻 요약**

- 함수형 프로그래밍에서는 불변 데이터가 필요하다. 계산에서는 변경 가능한 데이터에 쓰기를 할 수 없다.

- 카피-온-라이트는 데이터를 불변형으로 유지할 수 있는 원칙이다. 복사본을 만들고 원본 대신 복사본을 변경하는 것을 말한다.

- 카피-온-라이트는 값을 변경하기 전 얕은 복사(shallow copy)를 하고 리턴을 한다. 이렇게 하면 통제 가능한 범위에서 불변성 구현이 가능하다.

- 보일러 플레이트 코드(여러 곳에서 비슷한 코드가 반복)를 줄이기 위해 기본적인 배열과 객체 동작에 대한 카피-온-라이트 버전을 만들어 두는 것이 좋다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


