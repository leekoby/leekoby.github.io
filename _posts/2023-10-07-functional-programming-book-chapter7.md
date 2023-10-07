---
layout: post
title: 7. 신뢰할 수 없는 코드를 쓰면서 불변성 지키기
author: admin
date: 2023-10-07 09:00:00 +900
lastmod: 2023-10-07 09:00:00 +900
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

# **🌈 7. 신뢰할 수 없는 코드를 쓰면서 불변성 지키기**

이번 장에서 살펴볼 내용

- 레거시 코드나 신뢰할 수 없는 코드로부터 내 코드를 보호하기 위한 방어적 복사 만들기

- 얕은 복사와 깊은 복사의 비교

- 카피-온-라이트와 방어적 복사를 언제 사용하면 좋은지

## **💻 레거시 코드와 불변성**

레거시 코드 : 오래전에 만든 것으로, 지금 당장 고칠 수 없어 그대로 사용해야하는 것을 말함.

레거시 코드를 추가하면 카피-온-라이트 원칙을 지킬 수 없다.

그러나 카피-온-라이트 원칙을 지키며 안전하게 함수를 사용할 수 있는 다른 원칙이 있다. 

이 원칙을 방어적 복사(defensive copy)라고 한다.

<br/>

---

<br/>

## **💻 우리가 만든 카피-온-라이트 코드는 신뢰할 수 없는 코드와 상호작용해야 한다.**

우리가 만든 모든 코드는 불변성이 지켜지는 `안전지대`에 있고, 이 곳에 있는 코드는 걱정없이 사용 가능하다.

그렇다면 안전지대 밖에 있는 코드를 우리가 만든 코드에 사용해야할 때, 안전하지 않은 함수의 입, 출력을 통해 안전지대에 있는 코드와 데이터를 주고받아야 한다.

이때, 문제가 발생하는데, 안전지대 밖으로 나가는 데이터는 바뀔 수가 있다. 

신뢰할 수 없는 코드가 데이터를 바꿀 수 있기 때문이다. 

마찬가지로 신뢰할 수 없는 코드에서 안전지대로 들어오는 데이터 역시 바뀔 수 있다. 

현 상황에서 카피-온-라이트로 문제를 해결할 수 없다.

여기서 데이터가 바뀌는 것을 완벽히 막아주는 원칙이 필요하며, 이를 방어적 복사 라고 한다.

<br/>

---

<br/>

## **💻 방어적 복사는 원본이 바뀌는 것을 막아준다.**

➡️ 신뢰할 수 없는 코드와 데이터를 주고 받는 문제를 푸는 방법은 복사본을 만드는 것이다.

데이터가 들어올 때

1. 신뢰할 수 없는 코드에 있는 변경 가능한 데이터(참조를 가지고 있음)가 안전지대로 들어온다.

2. 그 데이터를 안전지대에서 깊은 복사 한다.
   - 깊은 복사로 만든 복사본은 안전지대에 있다.

   - 신뢰할 수 없는 코드는 데이터가 바뀌어도 영향이 가지 않는다.


데이터가 나갈 때

1. 안전지대에 있는 변경 불가한 데이터를 깊은 복사를 통해 복사한다.

2. 깊은 복사를 한 데이터를 안전지대에서 내보낸다.
   
   - 원본은 안전지대에 있어 불변성을 유지한다.
   
   - 복사본이 변경되어도 원본에 영향을 미치지 않음.

<br/>

---

<br/>

## **💻 방어적 복사 구현하기**

바뀔 수도 있는 데이터가 신뢰할 수 없는 코드를 통해 안전지대로 들어오게 되면, 들어온 데이터로 깊은 복사본을 만들고 변경 가능한 원본은 버린다.

반대로 안전지대에서 나가는 데이터도 신뢰할 수 없는 코드를 통해 변경될 수 있기 때문에 나가는 데이터도 깊은 복사본을 만들어 내보내야 한다.

한마디로 들어오고 나가는 데이터의 복사본을 만드는 것이 방어적 복사가 동작하는 방식이다.

🔻 원래 코드

```js
function add_item_to_cart(name, price){
  let item = make_cart_item(name, price);
  shopping_cart = add_item(shopping_cart, item);
  let total = calc_total(shopping_cart);
  set_cart_total_dom(total);
  update_shipping_icons(shopping_cart);
  update_tax_dom(total);
  black_friday_promotion(shopping_cart); 
}
```

🔻 데이터를 전달하기 전에 복사

```js
function add_item_to_cart(name, price){
  let item = make_cart_item(name, price);
  shopping_cart = add_item(shopping_cart, item);
  let total = calc_total(shopping_cart);
  set_cart_total_dom(total);
  update_shipping_icons(shopping_cart);
  update_tax_dom(total);
  // 데이터를 전달하기 전에 깊은 복사
  let cart_copy = deepCopy(shopping_cart);
  black_friday_promotion(shopping_cart); 
}
```

🔻 데이터를 전달하기 전후에 복사

```js
function add_item_to_cart(name, price){
  let item = make_cart_item(name, price);
  shopping_cart = add_item(shopping_cart, item);
  let total = calc_total(shopping_cart);
  set_cart_total_dom(total);
  update_shipping_icons(shopping_cart);
  update_tax_dom(total);
  // 데이터를 전달하기 전에 깊은 복사
  let cart_copy = deepCopy(shopping_cart);
  black_friday_promotion(shopping_cart); 
  // 들어오는 데이터를 위해 깊은 복사
  shopping_cart = deepCopy(cart_copy);
}
```

<br/>

---

<br/>

## **💻 방어적 복사 규칙**

### **🍳 규칙1. 데이터가 안전한 코드에서 나갈 때 복사하기**

1. 불변성 데이터를 위한 깊은 복사본을 만든다.

2. 신뢰할 수 없는 코드로 복사본을 전달한다.

<br/>

### **🍳 규칙2. 안전한 코드로 데이터가 들어올 때 복사하기**

1. 변경될 수도 있는 데이터가 들어오면 바로 깊은 복사본을 만들어 안전한 코드로 전달한다.

2. 복사본을 안전한 코드에서 사용한다.


위 규칙을 따르면 불변성 원칙을 지키면서 신뢰할 수 없는 코드와 상호작용할 수 있다.

여기서, 방어적 복사와 관련된 코드를 감싸면 더 좋은 코드가 될 수 있다.

<br/>

---

<br/>

## **💻 신뢰할 수 없는 코드 감싸기**

방어적 복사 코드를 분리해 새로운 함수로 만들기

🔻 원래 코드 

```js
function add_item_to_cart(name, price) {
  let item = make_cart_item(name, price);
  shopping_cart = add_item(shopping_cart, item);
  let total = calc_total(shopping_cart);
  set_cart_total_dom(total);
  update_shipping_icons(shopping_cart);
  update_tax_dom(total);
  let cart_copy = deepCopy(shopping_cart);
  black_friday_promotion(cart_copy);
  shopping_cart = deepCopy(cart_copy);
}
```

🔻 안전하게 분리된 버전

```js
function add_item_to_cart(name, price) {
  let item = make_cart_item(name, price);
  shopping_cart = add_item(shopping_cart, item);
  let total = calc_total(shopping_cart);
  set_cart_total_dom(total);
  update_shipping_icons(shopping_cart);
  update_tax_dom(total);
  // 방어적 복사 함수 사용
  shopping_cart = black_friday_promotion_safe(shopping_cart);
}

// 방어적 복사와 관련된 코드
function black_friday_promotion_safe(cart) {
  let cart_copy = deepCopy(cart);
  black_friday_promotion(cart_copy);
  return deepCopy(cart_copy);
}
```

<br/>

---

<br/>

## **💻 카피-온-라이트와 방어적 복사 비교**

|카피-온-라이트|방어적 복사|
|:-|:-|
|통제할 수 있는 데이터를 바꿀 때 사용|신뢰할 수 없는 코드와 데이터를 주고받아야 할 때|
|안전지대 어디서나 쓸 수 있다.|안전지대의 경계에서 데이터가 오고 갈 때 사용|
|얕은 복사(상대적으로 적은 비용)|깊은 복사(상대적으로 많은 비용)|
|1. 바꿀 데이터의 얕은 복사를 만든다.<br/> 2. 복사본을 변경한다.<br/> 3. 복사본을 리턴한다.	|1. 안전지대로 들어오는 데이터에 깊은 복사를 만든다. <br/> 2. 안전지대로 나가는 데이터에 깊은 복사를 만든다.|

<br/>

---

<br/>

## **💻 깊은 복사는 얕은 복사보다 비싸다**

✔️ 깊은 복사는 원본과 어떤 데이터 구조도 공유하지 않는 것이 얕은 복사와의 차이점이다.

✔️ 중첩된 모든 객체나 배열을 복사한다.

✔️ 얕은 복사에서는 바뀌지 않는 값이라면 원본과 복사본이 데이터를 공유한다.

깊은 복사는 비용이 많이 들기 때문에 모든 곳에 쓰이지 않는다. 카피-온-라이트를 사용할 수 없는 곳에서만 사용한다.

<br/>

---

<br/>

## **💻 자바스크립트에서 깊은 복사를 구현하는 것은 어렵다.**

그렇다면 어떤 좋은 방법이 있을까?

바로, Lodash 라이브러리에 깊은 복사 함수를 사용하는 것이다.

```js
// Lodash
.cloneDeep()
```
해당 함수는 중첩된 데이터에 깊은 복사를 한다.

<br/>

---

<br/>

## **💻 카피-온-라이트와 방어적 복사 중에 어떤 원칙이 더 중요할까?**

데이터가 불변성을 가질 수 있는 안전지대를 만들기 위해서 카피-온-라이트 방식이 아닌 방어적 복사 방식을 사용할 수도 있지만, 깊은 복사는 중첩된 데이터 구조의 모든 것을 복사하기 때문에 바뀐 부분만 복사하는 얕은 복사, 카피-온-라이트에 비해 방어적 복사를 사용할 때 많은 비용이 든다.

상황에 따라 적절하게 두 원칙을 사용해야 한다. 

방어적 복사만으로 코드를 작성할 수 없고 또 카피-온-라이트만으로도 코드를 작성할 수 없다.

<br/>

---

<br/>

## **💻 요약**

불변성을 유지할 수 있는 더 강력한 원칙인 방어적 복사는 불변성을 스스로 구현할 수 있기 때문에 더 강력하다. 

그러나 더 많은 데이터를 복사해야하므로 비용이 많이 든다는 단점이 있다. 

그래서 카피-온-라이트와 함꼐 사용하여 언제든 적용할 수 있는 강력함과 얕은 복사로 인한 효율성에 대한 장점을 모두 얻을 수 있다.

- 방어적 복사는 불변성을 구현하는 원칙. 데이터가 들어오고 나갈 때 복사본을 만든다.

- 방어적 복사는 깊은 복사를 한다. 그러므로 카피-온-라이트보다 비용이 더 든다

- 카피-온-라이트와 다르게 방어적 복사는 불변성 원칙을 구현하지 않은 코드로부터 데이터를 보호해준다.

- 복사본이 많이 필요하지 않기 때문에 카피-온-라이트를 더 많이 사용한다.

- 방어적 복사는 신뢰할 수 없는 코드와 함께 사용할 때만 사용한다.

- 깊은 복사는 위에서 아래로 중첩된 데이터 전체를 복사한다.

- 얕은 복사는 필요한 부분만 최소한으로 복사한다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


