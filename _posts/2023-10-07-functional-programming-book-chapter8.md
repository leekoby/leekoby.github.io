---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 8. 
author: admin
date: 2023-10-07 15:00:00 +900
lastmod: 2023-10-07 15:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, 쏙쏙 들어오는 함수형 코딩]  # 대문자로 작성
tags: [functional programming, 쏙쏙 들어오는 함수형 코딩] # 소문자로 작성
---

![쏙쏙 들어오는 함수형 코딩](https://github.com/leekoby/leekoby.github.io/assets/118284808/7e31456f-1763-4786-bef8-b3ae98921901){: width="500" height="500" }

> 해당 포스트는 `쏙쏙 들어오는 함수형 코딩`을 학습하며 필요한 내용을 정리한 포스트입니다.<br/>
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 8. 계층형 설계 1**

이번 장에서 살펴볼 내용

-  소프트웨어 설계에 대한 실용적 정의

-  계층형 설계의 이해와 어떤 도움이 되는지

-  클린 코드를 만들기 위한 함수 추출하는 방법

-  계층 분리로 소프트웨어를 설계하면 왜 더 나은 생각을 할 수 있는지

## **💻 소프트웨어 설계란 무엇인가?**

설계를 잘하면 소프트웨어 개발 과정 전체에 도움이 된다.

아이디어를 코드로 구현하고 테스트하고 유지보수하기 쉽다.

이러한 내용이 소프트웨어 설계에 관한 실용적 정의에 가깝다.

> **<span style="color:#3366ff">소프트웨어 설계 (software design)</span>**
> 
> **<span style="color:#ff6600">코드를 만들고, 테스트하고, 유지보수하기 쉬운 프로그래밍 방법을 선택하기 위해 미적 감각을 사용하는 것.</span>**
{:.prompt-info }

<br/>

---

<br/>

## **💻 계층형 설계란 무엇인가?**

계층형 설계는 소프트웨어를 계층으로 구성하는 기술

각 계층에 있는 함수는 바로 아래 계층 함수를 이용해 정의한다.

실제 감각을 키우면 소프트웨어를 고치고, 테스트하고, 재사용하기 쉬운 코드를 만들기 위한 계층 구조가 무엇인지 알 수 있다.

각 계층(layer)을 정확히 구분하기는 어렵다. 

계층을 잘 구분하려면 구분하기 위한 다양한 변수를 찾고 찾은 것을 가지고 어떻게 해야 하는지 알아야 한다.

<br/>

---

<br/>

## **💻 설계 감각 키우기**

### **🍳 계층형 설계 감각을 키우기 위해 다양한 입력**

|함수 본문|계층 구조|함수 시그니처|
|:-|:-|:-|
|- 길이|- 화살표 길이|- 함수명|
|- 복잡성|- 응집도|- 인자 이름|
| - 구체화 단계	| - 구체화 단계	| - 인자값|
| - 함수 호출		| | - 리턴값|
| - 프로그래밍 언어의 기능 사용		|||

<br/>

### **🍳 계층형 설계 감각을 키우기 위한 출력**

|조직화|구현|변경|
|:-|:-|:-|
|- 새로운 함수를 어디에 놓을지 결정 <br/> - 함수를 다른 곳으로 이동 |- 구현 바꾸기 <br/> - 함수 추출하기 <br/> - 데이터 구조 바꾸기 |- 새 코드를 작성할 곳 선택하기 <br/> - 적절한 수준의 구체화 단계 결정하기|

<br/>

---

<br/>

## **💻 계층형 설계 패턴**


### **🍳 패턴 1. 직접 구현**

직접 구현된 함수를 읽을 때, 함수 시그니처가 나타내고 있는 문제를 함수 본문에서 적절한 구체화 수준에서 해결해야 한다.

<br/>

### **🍳 패턴 2. 추상화 벽**

계층형 설계를 할 때 어떤 계층에서는 세부 구현을 감추고 인터페이스를 제공한다.

<br/>

### **🍳 패턴 3. 작은 인터페이스**

비즈니스 개념을 나타내는 중요한 인터페이스는 작고 강력한 동작으로 구성하는 것이 좋다.

<br/>

### **🍳 패턴 4. 편리한 계층**

계층을 잘 나누고 각 계층이 하는 일을 잘 추상화 해야 한다.

<br/>

---

<br/>

## **💻 패턴 1. 직접 구현**

아래는 넥타이 하나를 사면 무료로 넥타이 클립을 하나 주는 코드이다.

```js
function freeTieClip(cart) {
  let hasTie = false
  let hasTieClip = false;
  for(let i = 0; i < cart.length; i++) {
    let item = cart[i];
    if(item.name === "tie") // 넥타이 확인
      hasTie = true;
    if(item.name === "tie clip") // 클립 확인
      hasTieClip = true;
  }
  if(hasTie && !hasTieClip) {
    let tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip); // 넥타이 클립 추가
  }
  return cart;
}
```

한 함수에서 아주 많은 일들을 하고 있다. 

이 코드는 첫번째 계층형 설계 패턴인 직접 구현을 따르지 않고 있다. 

게다가 마케팅과 관련된 함수인데 배열인 장바구니를 순회하는 등 너무 구체적인 내용을 담고 있다.

<br/>

---

<br/>

## **💻 장바구니 개선**

🔻 제품 추가하기

```js
function add_item(cart, item) {
  return add_element_last(cart, item);
}
```

🔻 제품 삭제하기

```js
function remove_item_by_name(cart, name) {
  let idx = null;
  for(let i = 0; i < cart.length; i++) {
    if(cart[i].name === name)
      idx = i;
  }
  if(idx !== null)
    return removeItems(cart, idx ,1);
  return cart;
}
```

장바구니에 제품이 있는지 확인하기
- 아직 구현하지 않음

🔻 합계 계산하기

```js
function calc_total(cart) {
  let total = 0;
  for(let i = 0; i < cart.length; i++) {
    let item = cart[i];
    total += item.price;
  }
  return total;
}

```

장바구니 비우기
- 아직 구현하지 않음

🔻 제품이름으로 가격 설정하기

```js
function setPriceByName(cart, name, price) {
  let cartCopy = cart.slice();
  for(let i = 0; i < cartCopy.length; i++) {
    if(cartCopy[i].name === name)
      cartCopy[i] = setPrice(cartCopy[i], price);
  }
  return cartCopy;
}
```

🔻 세금 계산하기

```js

function cartTax(cart) {
  return calc_tax(calc_total(cart));
}
```

🔻 무료 배송이 되는지 확인하기

```js
function gets_free_shipping(cart) {
  return calc_total(cart) >= 20;
}
```

🔻 장바구니에 제품이 있는지 확인하기 구현하기

```js
function freeTieClip(cart) {
  let hasTie = false
  let hasTieClip = false;
  for(let i = 0; i < cart.length; i++) {
    let item = cart[i];
    if(item.name === "tie")
      hasTie = true;
    if(item.name === "tie clip")
      hasTieClip = true;
  }
  if(hasTie && !hasTieClip) {
    let tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip);
  }
  return cart;
}
```

방금 구현한 freeTieClip() 함수를 보니 직접 구현 패턴을 적용할 수 있을 것 같다.

장바구니 안에 제품이 있는지 확인하는 함수가 있다면 계층의 저수준인 for문을 사용하지 않아도 된다.

```js
function isInCart(cart, name) {
  for(let i = 0; i < cart.length; i++) {
    if(cart[i].name === name)
      return true;
  }
  return false;
}
```

위처럼 함수에서의 for문 빼내어 함수를 만들고 아래처럼 직접 구현 패턴을 적용할 수 있다.

```js
function freeTieClip(cart) {
  let hasTie = isInCart(cart, "tie");
  let hasTieClip = isInCart(cart, "tie clip");
  if(hasTie && !hasTieClip) {
    let tieClip = make_item("tie clip", 0);
    return add_item(cart, tieClip);
  }
  return cart;
}
```

<br/>

---

<br/>

## **💻 호출 그래프**

지금까지 작성한 함수들을 계층을 나누어 생각해볼 수 있다.


- 장바구니 비즈니스 규칙

- 일반적인 비즈니스 규칙

- 장바구니 기본 동작

- 제품에 대한 기본 동작

- 카피-온-라이트 동작

- 자바스크립트 언어 기능

함수 그래프를 그려 함수 호출을 시각화해보면 아래와 같다.

![호출 그래프 이미지](https://github.com/leekoby/leekoby.github.io/assets/118284808/fafd1ab1-a52c-4af3-be5e-cce576681cdd)

같은 계층에 있는 함수는 같은 목적을 가졌고, 함수가 호출하는 함수를 화살표로 가리켰다.

위 그래프는 그렇지 않지만, 직접 구현 패턴을 사용하면 모든 화살표가 같은 길이를 가져야 한다.

이 그래프처럼 화살표가 다양한 계층을 넘나드는 것은 어떤 것은 너무 많이 구체화 했고, 어떤 것은 덜 구체화했다는 증거이다.

직접 구현 패턴이 지향하는 것은 상위 계층에 있는 함수가 바로 아래 계층의 동작에 의존하게 만드는 것이다. 쉽게 말하면 모두 같은 길이의 화살표를 가져야 한다.

일반적인 방법으로는 함수에서 코드를 추출하여 추출한 코드를 가진 함수를 추가하여 화살표 길이를 짧게 만드는 것이다.

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/33c7bbfc-4aa4-43de-90ec-5fb2d18e6a2f)

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/a1d59c19-7273-4d27-8d03-f0c0e385ae55)

<br/>

---

<br/>

## **💻 3단계 줌 레벨**

계층형 설계는 3단계의 줌 레벨로 볼 수 있다.

1. 계층 사이에 상호 관계
2. 특정 계층의 구현
3. 특정 함수의 구현

### **🍳 1. 전역 줌 레벨**

전역 줌 레벨로 그래프 전체 중 필요한 부분을 살펴볼 수 있다. 

전역 줌 레벨이 기본 줌 레벨이다. 

계층 사이에 상호 관계를 포함해서 모든 문제 영역을 살펴볼 수 있다.

<br/>

### **🍳 2. 계층 줌 레벨**

계층 줌 레벨은 한 계층과 연결된 바로 아래 계층을 볼 수 있는 줌 레벨이다. 

계층 줌 레벨로 계층이 어떻게 구현되어 있는지 알 수 있다.

<br/>

### **🍳 3. 함수 줌 레벨**

함수 줌 레벨로 함수 하나와 바로 아래 연결된 함수들을 볼 수 있다. 

함수 줌 레벨로 함수 구현의 문제를 찾을 수 있다.

<br/>

다양한 줌 레벨로 설계 문제를 찾거나 고칠 수 있다.

<br/>

---

<br/>

## **💻 직접 구현 패턴 리뷰**

### **🍳 1. 직접 구현한 코드는 한 단계의 구체화 수준에 관한 문제만 해결한다.**

직접 구현하면 코드를 읽기 위해 알아야 하는 구체화 단계의 범위를 줄일 수 있다.

<br/>

### **🍳 2. 호출 그래프는 구체화 단계에 대한 풍부한 단서를 보여준다.**

호출 그래프는 함수가 서로 어떻게 연결되어 있는지 보여주므로, 함수 시그니처 본문, 호출 그래프와 같은 다양한 단서를 가지고 직접 코드 패턴을 적용할 수 있다.

<br/>

### **🍳 3. 함수를 추출하면 더 일반적인 함수로 만들 수 있다.**

함수에 직접 구현 패턴을 적용하는 방법의 하나는 함수가 더 구체적인 내용을 다루지 않도록 함수를 일반적인 함수로 빼내는 것이다. 

일반적인 함수는 보통 구체적인 내용을 하나만 다루기 때문에 테스트하기 쉽다. 

명확한 코드와 알맞은 이름을 가진 함수는 더 읽기 쉽다.

<br/>

### **🍳 4. 일반적인 함수가 많을수록 재사용하기 좋다.**

구현을 명확하게 하기 위해 일반적인 함수를 빼내는 것으로 구체적인 함수보다 더 많은 곳에서 쓸 수 있다. 또한 언제나 재사용이 가능하다.

<br/>

### **🍳 5. 복잡성을 감추지 않는다.**

계층형 설계에서 모든 계층은 바로 아래 계층에 존재해야 한다. 

복잡한 코드를 같은 계층으로 옮기면 안된다. 

더 낮은 구체화 수준을 가진 일반적인 함수를 만들어 직접 구현 패턴을 적용해야 한다.

<br/>

---

<br/>

## **💻 요약**


- 계층형 설계는 코드를 추상화 계층으로 구성한다. 계층을 볼 때 다른 계층에 구체적인 내용은 몰라도 된다.

- 문제 해결을 위한 함수를 구현할 때 어떤 구체화 단계로 쓸지 결정하는 것이 중요하다. 그래야 함수가 어떤 계층에 속할지 알 수 있다.

- 함수가 어떤 계층에 속할지 알려주는 요소는 많이 있다. 함수 이름 본문, 호출 그래프 등이 그러한 요소다.

- 함수 이름은 의도를 알려준다. 비슷한 목적의 이름을 가진 함수를 함께 묶을 수 있다.

- 함수 본문은 중요한 세부 사항을 알려준다. 함수 본문은 함수가 어떤 계층 구조에 있어야 하는지 알려준다.

- 호출 그래프로 구현이 직접적이지 않다는 것을 알 수 있다. 함수를 호출하는 화살표가 다양한 길이를 가지고 있다면 직접 구현되어 있지 않다는 신호다.

- 직접 구현 패턴은 함수를 명확하게 구현해 계층을 구성할 수 있도록 알려준다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


