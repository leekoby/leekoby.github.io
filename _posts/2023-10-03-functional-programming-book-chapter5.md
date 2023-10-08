---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 5.
author: admin
date: 2023-10-03 09:00:00 +900
lastmod: 2023-10-03 09:00:00 +900
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

# **🌈 5. 더 좋은 액션 만들기**

이번 장에서 살펴볼 내용

- 암묵적 입력과 출력을 제거해서 재사용하기 좋은 코드를 만드는 방법
- 복잡하게 엉킨 코드를 풀어 더 좋은 구조를 만드는 법 

## **💻 비즈니스 요구 사항과 설계를 맞추기**

### **🍳 요구 사항에 맞춰 더 나은 추상화 단계 선택하기**

비즈니스 요구 사항은 장바구니에 담긴 제품을 주문할 때 무료 배송인지 확인하는 것이다.

```js
function gets_free_shipping(total, item_price){
  return item_price + total >= 20;
}
```

코드를 보면 비즈니스 요구사항과 맞지 않고 맞는 것처럼 보이게 하는 함수인 것을 알 수 있다.

합계 금액과 제품 가격을 더해서 무료배송 금액과 비교하는 것이 아닌 주문 결과가 무료 배송인지 확인하는 코드를 작성해야 비즈니스 요구사항과 맞는 함수이다.

```js
function calc_total(cart){
  let total = 0;
  for(let i = 0; i < cart.length; i++) {
    let item = cart[i];
    total = item.price + total; // 코드 중복
  }
  return total;
}
```

게다가 `item.price + total` , 장바구니 합계를 계산하는 코드가 중복이다.

중복이 항상 나쁘다고 할 수는 없지만, 코드에서 나는 냄새다. 코드의 냄새 (code smell) 는 나중에 문제가 될 수 있다.

그래서 함수를 비즈니스 요구사항에 맞게 고쳐주려 한다.


`get_free_shipping(total, item_price)` 함수를 `get_free_shipping(cart)` 로 바꾼다.

<br/>

---

<br/>

## **💻 비지니스 요구사항과 함수 맞추기**

### **🍳 함수의 동작을 바꿨기 때문에 엄밀히 말하면 리팩터링이라고 할 수 없다.**

🔻 원래 코드

```js
function gets_free_shipping(total, item_price) {
  return item_price + total >= 20;
}
```

🔻 새 시그니처를 적용한 코드

```js
function gets_free_shipping(cart) {
  return calc_total(cart) >= 20;
}
```

바꾼 함수는 합계와 제품 가격 대신 장바구니 테이터를 사용

장바구니는 전자상거래에서 많이 사용하는 엔터티 타입이기 때문에 비즈니스 요구사항과 잘 맞는다.

함수 시그니처가 바뀌었기 때문에 사용하는 부분도 수정 

🔻 원래 코드

```js
function update_shipping_icons(){
  let buttons = get_buy_buttons_dom();
  for(let i = 0; i < buttons.length; i++){
    let button = buttons[i];
    let item = button.item;

    if(gets_free_shipping(shopping_cart_total, item_price))
      button.show_free_shipping_icon();
    else
      button.hide_free_shipping_icon();
  }
}
```

🔻 새 시그니처를 적용한 코드

```js
function update_shipping_icons(){
  let buttons = get_buy_buttons_dom();
  for(let i = 0; i < buttons.length; i++){
    let button = buttons[i];
    let item = button.item;
    
    let new_cart = add_item(shopping_cart, item.name, item.price);

    if(gets_free_shipping(new_cart))
      button.show_free_shipping_icon();
    else
      button.hide_free_shipping_icon();
  }
}
```

<br/>

---

<br/>

## **💻 원칙 : 암묵적 입력과 출력은 적을수록 좋다**

인자가 아닌 모든 입력은 암묵적 입력이고, 리턴값이 아닌 모든 출력은 암묵적 출력이다.

어떤 함수에 암묵적 입력과 암묵적 출력이 있다면 다른 컴포넌트와 강하게 연결된 컴포넌트라고 할 수 있다.

다른 곳에서 사용할 수 없기 때문에 모듈이 아니다. 

이런 함수의 동작은 연결된 부분의 동작에 의존

또 아무때나 실행할 수 없기 때문에 테스트하기에도 어렵다.

계산은 암묵적 입력과 출력이 없기 때문에 테스트 하기 쉽다.

모든 암묵적 ㅣㅇㅂ력과 출력을 못해서 액션을 계산으로 바꾸지 못해도 암묵적 입출력만 줄여도 테스트하기 쉽고 재사용하기 좋다.

<br/>

---

<br/>

## **💻 암묵적 입출력 줄이기**

먼저 암묵적 입력을 명시적 입력인 인자로 바궈보자.

🔻 원래 코드

```js
function update_shipping_icons() {
  let buttons = get_buy_buttons_dom();
  for(let i = 0; i < buttons.length; i++){
    let button = buttons[i];
    let item = button.item;
    let new_cart = add_item(shopping_cart, item.name, item.price); 
    // shopping_cart 전역변수를 읽고 있다.
    if(gets_free_shipping(new_cart))
      button.show_free_shipping_icon();
    else
      button.hide_free_shipping_icon();
  }
}
```

🔻 명시적 인자로 바꾼 코드

```js
function update_shipping_icons(cart) { // 전역변수 대신 인자 추가
  let buttons = get_buy_buttons_dom();
  for(let i = 0; i < buttons.length; i++){
    let button = buttons[i];
    let item = button.item;
    let new_cart = add_item(cart, item.name, item.price);
    // cart 인자 추가
    if(gets_free_shipping(new_cart))
      button.show_free_shipping_icon();
    else
      button.hide_free_shipping_icon();
  }
}
```

코드에서 전역변수인 `shopping_cart` 를 사용하면서 암묵적 입력과 출력을 이용하고 있다.

`shopping_cart` 라는 전역변수 대신 인자를 넣어주어 암묵적 입력을 제거한다.

함수 시그니처가 달라졌기 때문에 호출하는 곳도 바꿔준다.

```js
// update_shipping_icons();
update_shipping_icons(shopping_cart);
```
이제는 함수 안에서 전역 변수인 shopping_cart 를 사용하는 게 아니라 인자로 전달할 수 있다.

<br/>

---

<br/>

## **💻 원칙 : 설계는 엉켜있는 코드를 푸는 것이다**

함수를 사용하면 관심사를 자연스럽게 분리할 수 있다. 

함수는 인자로 넘기는 값과 그 값을 사용하는 방법을 분리한다. 또 가끔은 분리된 것을 합치고 싶을 수도 있다. 

하지만 분리한 것은 언제든 쉽게 조합할 수 있기 때문에 잘 분리하면 분리할 수록 좋다. 

잘 분리가 된다면, 

- 재사용하기 쉽다

  - 함수는 작으면 작을수록 재사용하기 쉽다.
  - 하는 일도 적고 쓸 때 가정을 많이 하지 않아도 된다.

- 유지보수하기 쉽다.

  - 작은 함수는 쉽게 이해할 수 있고 유지보수하기 쉽다.
  - 코드가 작기 떄문에 올바른지 아닌지 명화하게 알 수 있다.

-  테스트하기 쉬워진다.

  - 한 가지 일만 하기 때문에 한가지만 테스트하면 된다.

함수에 특별한 문제가 없어도 꺼낼 것이 있다면 분리하는 것이 좋다.

<br/>

---

<br/>

## **💻 add_item()을 분리해 더 좋은 설계 만들기**

add_item() 이라는 함수가 있다. 장바구니에 제품을 추가하는 간단한 일을 하는 함수이다. 그런데 정말 간단한걸까?

```js
function add_item(cart, name, price) {
  let new_cart = cart.slice(); // 1. 배열 복사
  new_cart.push({ // 2. 복사본에 item 추가
    name: name, // 3. item 객체 만들기
    price: price,
  });
  return new_cart; // 4. 복사본 리턴
}
```

어떤 일을 하는지 나누어보면 이렇게 나눌 수 있다.

🔻 item 객체를 만드는 함수

```js
function make_cart_item(name, price) {
  return {
    name : name,
    price : price,
  };
}
```

🔻 배열을 복사해서 복사본 반환하는 함수

```js
function add_item(cart, item) {
  let new_cart = cart.slice();
  new_cart.push(item);
  return new_cart;
}
```

🔻 함수 호출

```js
add_item(shopping_cart, make_cart_item("shoes", 3.45))
```

item 구조만 알고 있는 함수 (make_cart_item)와 cart 구조만 알고 있는 함수 (add_item)로 나눠서 수정

이렇게 분리하면 cart와 item을 독립적으로 확장할 수 있다.

1번, 3번, 4번은 값을 바꿀 때 복사하는 카피-온-라이트 를 구현한 부분이기 때문에 함께 두면서 분리하는 것이 좋다.

<br/>

---

<br/>

## **💻 요약**

암묵적 입력과 암묵적 출력은 인자와 리턴값으로 바꿔 없애주기

설계는 엉켜있는 것을 푸는 것이다. 풀려있는 것은 언제든 합칠 수 있다.

엉켜있는 것을 풀어 각 함수가 하나의 일만 하도록 하면, 개념을 중심으로 쉽게 구성 가능

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


