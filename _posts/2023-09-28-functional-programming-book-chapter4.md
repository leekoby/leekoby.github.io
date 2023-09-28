---
layout: post
title: 4. 액션에서 계산 빼내기
author: admin
date: 2023-09-28 09:00:00 +900
lastmod: 2023-09-28 09:00:00 +900
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

# **🌈 4. 액션에서 계산 빼내기**

이번 장에서 살펴볼 내용

- 어떻게 함수로 정보가 들어가고 나오는지 
- 테스트하기 쉽고 재사용성이 좋은 코드를 만들기 위한 함수형 기술
- 액션에서 계산을 빼내는 방법

## **💻 MegaMart**

MegaMart는 온라인 쇼핑몰이다. 사용자가 쇼핑 중 장바구니에 담겨 있는 제품의 금액 합계를 볼 수 있는 기능이 중요한 기능 중 하나이다.

```js
let shopping_cart = []; // 장바구니 제품과 금액 합계를 담고 있는 전역변수
let shopping_cart_total = 0; // 장바구니 금액 합계를 담고 있는 전역변수

function add_item_to_cart(name, price) {
  shopping_cart.push({
    name: name,
    price: price,
  });
  calc_cart_total();
}

function calc_cart_total() {
  shopping_cart_total = 0;
  for(let i = 0; i < shopping_cart.length; i++) {
    shopping_cart_total += item.price;
  }
  set_cart_total_dom(); // 금액 합계를 반영하기 위해 DOM 업데이트
}
```

<br/>

---

<br/>

## **💻 무료 배송비 계산하기**

### **🍳 새로운 요구 사항**

장바구니에 담은 상품들의 합계가 20달러가 넘으면 제품의 구매 버튼 옆에 무료 배송 아이콘을 표시해 주려고 한다.

<br/>

### **🍳 절차적인 방법으로 구현하기**

```js
function update_shipping_icons() {

  // 페이지의 모든 구매 버튼을 가져와 반복문을 적용
  let buy_buttons = get_buy_buttons_dom(); 
  
  for(let i = 0; i < buy_buttons.length; i++) {
    let button = buy_bottons[i];
    let item = button.item;
    
    // 무료 배송이 가능한지
    if(item.price + shopping_cart_total >= 20)
      button.show_free_shipping_icon(); //아이콘 렌더링
    else
      button.hide_free_shipping_icon(); 
  }
}
```

```js
function calc_cart_total() {
  shopping_cart_total = 0;
  for(let i = 0; i < shopping_cart.length; i++) {
    shopping_cart_total += item.price;
  }
  set_cart_total_dom(); // 금액 합계를 반영하기 위해 DOM 업데이트
  update_shipping_icons(); // 아이콘을 업데이트하는 코드 추가
}
```

<br/>

### **🍳 세금 계산하기**

장바구니의 금액 합계가 바뀔 때마다 세금을 다시 계산해야 한다.

```js
function update_tax_dom() {
  set_tax_dom(shopping_cart_total + 0.10); // 세금 계산 후 DOM을 바로 업데이트
}
```

```js
function calc_cart_total() {
  shopping_cart_total = 0;
  for(var i = 0; i < shopping_cart.length; i++) {
    var item = shopping_cart[i];
    shopping_cart_total += item.price;
  }
  set_cart_total_dom(); // 금액 합계를 반영하기 위해 DOM 업데이트
  update_shipping_icons(); // 아이콘을 업데이트하는 코드 추가
  update_tax_dom(); // 페이지에 세금을 업데이트하기 위한 코드 추가
}
```

<br/>

---

<br/>

## **💻 테스트하기 쉽게 만들기**

### **🍳 지금 코드는 비즈니스 규칙을 테스트하기 어려움**

위 코드는 동작하긴 하지만, 정확하게 동작하고 있는지 테스트를 하고 싶어졌다. 그 중 비즈니스 규칙인 세금을 잘 계산하고 있는지를 테스트하고 싶은데 테스트하기가 꽤 어렵다.

- 브라우저 설정

- 페이지 로드

- 장바구니에 제품 담기 버튼 클릭

- DOM이 업데이트될 때까지 기다리기

- DOM에서 값 가져오기

- 가져온 문자열 값을 숫자로 바꾸기

- 예상하는 값과 비교하기

모든 코드가 바로 DOM으로 그려버리는 액션 함수로 이루어져 있어서 코드를 테스트하기 위해서는 브라우저에 들어가 이런 식으로 일일이 확인하는 방법밖에 없다.

<br/>

### **🍳 테스트 개선을 위한 제안**

- DOM 업데이트와 비즈니스 규칙은 분리되어야 한다.

- 전역변수가 없어야 한다.

<br/>

---

<br/>

## **💻  재사용하기 쉽게 만들기**

### **🍳 결제팀과 배송팀이 우리 코드를 사용하려고 한다.**

그런데 사용할 수 없었다.

- 장바구니 정보를 전역변수에서 읽어오고 있는데, 결제팀과 배송팀은 데이터베이스에서 장바구니 정보를 읽어온다.

- 결과를 보여주기 위해 DOM을 직접 바꾸고 있지만, 결제팀은 영수증을, 배송팀을 운송장을 출력해야 한다.

<br/>

### **🍳 제나의 코드 설명**

```js
function update_shipping_icons() {
  let buy_buttons = get_buy_buttons_dom();
  for(let i = 0; i < buy_buttons.length; i++) {
    let button = buy_bottons[i];
    let item = button.item;
    if(item.price + shopping_cart_total >= 20) // 타 팀에서 이규칙을 사용하려고함
      // 전역변수가 있어야 실행할 수 있음
      
      button.show_free_shipping_icon();  // 이 코드는 DOM이 있어야 사용가능 
    else
      button.hide_free_shipping_icon();
  }
} // 리턴 값이 없기 때문에 결과를 받을 방법이 없음
```

결제팀과 배송팀에서는 item.price + shopping_cart_total >= 20 라는 비즈니스 규칙을 사용하려고 한다. 

그런데 이 함수는 전역변수 shopping_cart_total 값이 있어야 실행할 수 있다.

<br/>

### **🍳 재사용성 개선을 위한 제안**

- 전역변수에 의존하지 않아야 한다.

- DOM을 사용할 수 있는 곳에서 실행된다고 가정하면 안 된다.

- 함수가 결괏값을 리턴해야 한다.


<br/>

---

<br/>

## **💻 액션과 계산, 데이터를 구분하기**

함수형 프로그래밍으로 위 문제를 해결하기 위해 액션, 계산, 데이터로 나누어 문제를 생각해보았다.

전역변수가 변경되므로 액션, DOM에서 엘리먼트들을 읽으므로 액션, DOM을 바꾸므로 액션. 

위 코드는 계산이나 데이터는 없고 모두 다 액션으로 이루어져 있다.

<br/>

---

<br/>

## **💻 함수에는 입력과 출력이 있다**

모든 함수에는 입력과 출력이 있다. 

입력은 함수가 계산을 하기 위한 외부 정보이고, 출력은 함수 밖으로 나오는 정보나 어떤 동작이다.

함수를 호출하는 이유는 결과가 필요하기 때문인데, 입력이 있어야 결과를 얻을 수 있다.

입력과 출력은 명시적이거나 암묵적일 수도 있다. 

이때 암묵적 입력과 암묵적 출력이 있으면 액션 함수가 되는 것이다. 

반대로 말하면 함수에서 암묵적 입력과 암묵적 출력을 없애면 계산 함수가 된다는 것이다.

```js 
let total = 0;
function add_to_total(amount){//인자는 입력
  console.log("old total: ", total) // 전역변수를 읽는 것은 입력
  // 콜솔에 찍히는 것은 출력
  total += amount;
  return total;
  // 리턴값은 출력
}
```

### **🍳 입력과 출력은 명시적이거나 암묵적일 수 있다.**

- 명시적 입력

  - 인자

- 암묵적 입력

  - 인자 외 다른 입력 (전역 변수를 읽는 것 등 ...)

- 명시적 출력

  - 리턴값

- 암묵적 출력

  - 리턴값 외 다른 출력 (콘솔에 찍히는 것, 전역변수를 바꾸는 것 등 ...)

<br/>

### **🍳 함수에 암묵적 입력과 출력이 있으면 액션이 된다.**

함수에서 암묵적 입력과 출력을 없애면 계산이 된다.

암묵적 입력은 함수의 인자, 암묵적 출력은 함수의 출력으로 바꾸면 된다.

<br/>

---

<br/>

## **💻 테스트와 재사용성은 입출력과 관련이 있다.**

테스트 담당 조지

- DOM 업데이트와 비즈니스 규칙은 분리 
- 전역변수 없어야 함 

개발팀 제나 

- 전역변수에 의존하지 않아야 함
- DOM을 사용할 수 있는 곳에서 실행된다고 가정하면 안 됨
- 함수가 결괏값을 리턴해야 함

### **🍳 DOM 업데이트와 비즈니스 규칙 분리하기**

DOM을 업데이트 한다는 것은 함수에서 어떤 정보가 나오는 것이기 때문에 출력이다. 

리턴값이 아니므로 암묵적 출력에 해당한다.

<br/>

### **🍳 DOM을 사용할 수 있는 곳에서 실행된다고 가정하지 않기**

위와 같으 이유

<br/>

### **🍳 전역변수에 의존하지 않기**

전역변수를 읽는 것은 암묵적 입력 / 바꾸는 것은 암묵적 출력

암묵적 입력은 인자로 / 암묵적 출력은 리턴값

<br/>

### **🍳 함수가 결괏값을 리턴하기**

암묵적 출력대신 명시적 출력을 사용

<br/>

---

<br/>

## **💻 액션에서 계산 빼내기**

입력값은 인자로 / 출력값은 리턴으로 

🔻 원래 코드

```js
function calc_cart_total() {
  shopping_cart_total = 0;
  // 계산에 해당되는 코드 시작
  for(let i = 0; i < shopping_cart.length; i++) {
    let item = shopping_cart[i];
    shopping_cart_total += item.price;
  }
  // 계산에 해당되는 코드 끝
  set_cart_total_dom(); 
  update_shipping_icons();
  update_tax_dom();
}
```

계산에 해당되는 코드를 따로 빼서 함수로 만들어보자!

🔻 바꾼 코드

```js
function calc_cart_total() {
  calc_total(); // 원래 있던 코드 분리
  set_cart_total_dom(); 
  update_shipping_icons();
  update_tax_dom();
}

function calc_total() {
 shopping_cart_total = 0; // 전역 변수
  // shopping_cart 전역 변수
  for(let i = 0; i < shopping_cart.length; i++) {
    let item = shopping_cart[i];
    shopping_cart_total += item.price;
    // 전역변수 변경
  }
}
```

암묵적인 입력으로는 전역변숫값을 읽어오고, 암묵적인 출력으로는 전역변숫값을 바꾸고 있다.

따라서 이 함수는 명시적인 입력과 명시적인 출력이 없기 때문에 아직도 액션 함수이다.

전역변수 대신 지역변수를 사용하여 지역변숫값을 리턴하도록 수정해보면

🔻 현재 코드

```js
function calc_cart_total() {
  calc_total(); // 원래 있던 코드 분리
  set_cart_total_dom(); 
  update_shipping_icons();
  update_tax_dom();
}

function calc_total() {
 shopping_cart_total = 0; // 전역 변수
  // shopping_cart 전역 변수
  for(let i = 0; i < shopping_cart.length; i++) {
    let item = shopping_cart[i];
    shopping_cart_total += item.price;
    // 전역변수 변경
  }
}
```

🔻 바꾼 코드

```js
function calc_cart_total() {
  shopping_cart_total = calc_total(); // 계산한 값 전역변수에 할당
  set_cart_total_dom(); 
  update_shipping_icons();
  update_tax_dom();
}

function calc_total(cart) {
  let total = 0; // 지역변수
  for(let i = 0; i < cart.length; i++) {
    let item = cart[i];
    total += item.price;
  }
  retrun total; // 지역변수 리턴
}
```

암묵적 출력을 없앴으니 암묵적 입력을 함수 인자로 바꾸기 

🔻 암묵적 입력을 없앤 코드

```js
function calc_cart_total() {
  shopping_cart_total = calc_total(shopping_cart); 
  // shopping_cart를 인자로 넣은 함수의 계산 결과를 전역변수에 할당

  set_cart_total_dom(); 
  update_shipping_icons();
  update_tax_dom();
}

function calc_total(cart) { 
  // 전역변수 대신 인자를 만들어 사용
  var total = 0; 
  for(var i = 0; i < shopping_cart.length; i++) {
    var item = shopping_cart[i];
    total += item.price;
  }
  retrun total;
}
```

이런 방식으로 다른 액션 함수에서 계산을 빼내어 새로운 함수로 만들 수 있다.

<br/>

---

<br/>

## **💻 액션에서 또 다른 계산 빼내기**


장바꾸니를 바꾸는 코드에서 계산을 빼내보자

🔻 원래 코드 

```js
function add_item__to_cart(name, price){
  shopping_cart.push({
    name,
    price
  });

  calc_cart_total();
}
```

🔻 바꾼 코드

```js
function add_item_to_cart(name, price){
 add_item(name, price);
 calc_cart_total(); 
}

function add_item(name, price){
  shopping_cart.push({
    name,
    price
  })
}
```

암묵적 입력과 출력 찾아 바꾸기

```js
function add_item(name, price){
  shopping_cart.push({//push() 함수로 배열을 바꾸고 있다.
  // 전역변수인 shopping_cart를 읽고 있다.
    name,
    price
  })
}
```

🔻 현재 코드

```js
function add_item_to_cart(name, price){
 add_item(name, price);
 calc_cart_total(); 
}

function add_item(name, price){
  shopping_cart.push({
    name,
    price
  })
}
```

🔻 바꾼 코드

```js
function add_item_to_cart(name, price){
 add_item(shopping_cart, name, price);
 // 전역변수를 인자로 넘긴다.
 calc_cart_total(); 
}

function add_item(cart, name, price){
  //인자를 추가한다.
  cart.push({
    // 전역변수 대신 인자를 사용한다.
    name,
    price
  })
}
```

압묵적인 입력을 인자로 바궈 명시적인 입력이 되었다. 

`push()` 함수로 전역배열을 변경하는 암묵적 출력이 남아있다. 

🔻 현재 코드

```js
function add_item_to_cart(name, price){
 add_item(shopping_cart, name, price);
 // 전역변수를 인자로 넘긴다.
 calc_cart_total(); 
}

function add_item(cart, name, price){
  //인자를 추가한다.
  cart.push({
    // 전역변수 대신 인자를 사용한다.
    name,
    price
  })
}
```

🔻 바꾼 코드

```js
function add_item_to_cart(name, price){
 shopping_cart = add_item(shopping_cart, name, price);
 calc_cart_total(); 
}

function add_item(cart, name, price){
  let new_cart = cart.slice(); 
  // 본사본을 만들어 지역변수에 할당
  new_cart.push({ 
    // 본사본을 변경
    name,
    price
  })
  return new_cart 
  // 복사본을 리턴 
}
```

복사본을 만들고 복사본에 제품을 추가해서 리턴했다. 

리턴값을 받아 전역변수에 할당했다. 

암묵적 출력이 리턴값으로 바뀌었다. 

<br/>

---

<br/>

## **💻 요약**

- 액션은 암묵적인 입력 또는 암묵적인 출력을 가지고 있다.

- 계산은 암묵적인 입력 또는 암묵적인 출력이 없어야 한다.

- 전역 변수, 데이터에 값을 추가하여 배열을 변경하는 등의 코드는 암묵적 입력 또는 암묵적 출력이 된다. 
  
  - 데이터는 복사하여 변경하고 반환하는 방식으로 명시적 입력과 명시적 출력으로 변경할 수 있다.

- 암묵적 입력은 인자로 바꿀 수 있다.

- 암묵적 출력은 리턴값으로 바꿀 수 있다.

- 함수형 원칙을 적용하면 액션은 줄어들고 계산은 늘어난다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


