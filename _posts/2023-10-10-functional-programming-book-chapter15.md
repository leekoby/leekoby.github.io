---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 15. 
author: admin
date: 2023-10-10 09:00:00 +900
lastmod: 2023-10-10 09:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, 쏙쏙 들어오는 함수형 코딩]  # 대문자로 작성
tags: [functional programming, 쏙쏙 들어오는 함수형 코딩] # 소문자로 작성
---

![쏙쏙 들어오는 함수형 코딩](https://github.com/leekoby/leekoby.github.io/assets/118284808/7e31456f-1763-4786-bef8-b3ae98921901){: width="500" height="500" }

> 해당 포스트는 `쏙쏙 들어오는 함수형 코딩`을 학습하며 필요한 내용을 정리한 포스트입니다.
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 15. 타임라인 격리하기**

이번 장에서 살펴볼 내용

- 코드를 타임라인 다이어그램으로 그리는 방법

- 버그를 찾기 위해 타임라인 다이어그램 보는 법

- 타임라인끼리 공유하는 자원을 줄여 코드 설계를 개선하는 방법

## **😫 버그가 있다!**

장바구니에서 합계 금액이 잘못 표시된다는 문의가 접수되고 있다!

고객이 장바구니에 제품을 추가하면 합계 금액이 `X`원이라고 표시되지만, 실제 결제를 진행하면 `Y`원이 결제되고 있다

이 문제를 어떻게 디버깅할 수 있을까?

천천히 클릭했을 때는 정상적으로 작동

빠르게 여러번 클릭하면 점점 오차가 발생한다.

### **🍳 코드를 보면서 버그 이해하기**

```js
        // 사용자가 장바구니에 추가 버튼을 클릭할 때 실행되는 함수
function add_item_to_cart(name, price, quantity){
  cart = add_item(cart, name, price, quantity);
  // 장바구니에 전역변수를 읽고 쓴다.
  calc_cart_total();
}

function calc_cart_total(){
  total = 0;
  // 제품 API로 AJAX 요청
  cost_ajax(cart, function(cost){ //요청이 완료될 때 실행되는 콜백
    total += cost; 
    // 판매 API로 AJAX 요청
    shipping_ajax(cart, function(shipping){ //판매 API 응답이 오면 실행되는 콜백
      total += shipping;
      update_total_dom(total); // 합계를 DOM에 출력
    })
  })
}
```

유스케이스 다이어그램으로 그려보면 다음과 같다.

다른 API 두개와 차례로 통신하고 있는 것을 볼 수 있다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e764cde9-8b73-43de-b881-60e19e13d95d)

<br/>

---

<br/>

## **💻 타임라인 다이어그램은 시간에 따라 어떤 일이 일어나는지 보여준다.**

두 번 빠르게 클릭했을 때 어떤 일이 일어나는지 보여주는 타임라인 다이어 그램이다.

타임라인은 액션을 순서대로 나열한 것 

타임라인 다이어그램은 시간에 따른 액션 순서를 시간적으로 표시한 것

<br/>

---

<br/>

## **💻 두 가지 타임라인 다이어그램 기본 규칙**

타임라인 다이어그램으로 알 수 있는 두 가지 중요한 사실

1) 순서대로 실행되는 액션

2) 동시에 나란히 실행되는 액션

기본 규칙 두 가지를 보자.

### **두 액션이 순서대로 나타나면 같은 타임라인에 넣는다.**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/10badc4d-377b-4730-b15b-86d181794e62)

타임라인에는 액션만 그린다.

<br/>

### **두 액션이 동시에 실행되거나 순서를 예상할 수 없다면 분리된 타임라인에 넣는다.**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/cebbacc5-9234-4003-945a-9540f0d6a1ef)

액션이 서로 다른 스레드나 프로세스, 기계, 비동기 콜백에서 실행되면 서로 다른 타임라인에 표시한다.

이 경우는 액션 두개가 서로 다른 비동기 콜백에서 실행된다.

두 액션의 실행 시점이 무작위이기 때문에 어떤 액션이 먼저 실행될지 알 수 없다.

<br/>

### **🍳 요약**

1) 액션은 순서대로 실행되거나 동시에 실행된다.

2) 순서대로 실행되는 액션은 같은 타임라인에서 하나가 끝나면 다른 하나가 실행된다.

3) 동시에 실행되는 액션은 여러 타임라인에서 나란히 실행된다.

이 규칙을 적용해서 코드를 타임라인 다이어그램으로 그리면 시간이 지남에 따라 코드가 어떻게 실행되는지 이해하는 데 도움이 된다.

<br/>

---

<br/>

## **💻 자세히 보면 놓칠 수 있는 액션 순서에 관한 두 가지 사실**

모든 액션을 확인하고 어떤 순서로 실행되는지 이해하는 것은 중요

### **`++`와 `+=`는 사실 세 단계다.**

이 연산자로 짧고 쉽게 쓰기를 할 수 있다.

이 연산자에는 세 단계로 연산을 한다는 사실이 숨어있다.

🔻 예시
```js
total ++ // 단일 연산자지만 세 단계로 실행된다.

let temp = total; // 읽기(액션)
temp = temp + 1; // 더하기(계산)
total = temp; // 쓰기 (액션)
```

`total++`이나 `total+=3`은 읽기와 쓰기, 두 개의 액션으로 다이어그램에 표시

두 번째 단계는 계산이므로 다이어그램에 표시하지 않음

<br/>

### **인자는 함수를 부르기 전에 실행한다.**

만약 함수에 인자를 전달하면서 실행한다면 인자는 함수에 전달되기 전에 실행된다.

이 부분도 타임라인 다이어그램을 그릴 때 순서대로 표현되어야 한다.

🔻 예시 코드 - 전역변수를 읽어(액션) 값을 로깅하는(액션) 코드

```js
console.log(total) 

//실행순서
let temp = total;
console.log(temp);
```

모든 액션을 다이어그램에 올바른 순서로 표시해야 하므로 주의해야 한다.

<br/>

---

<br/>

## **💻 add-to-cart 타임라인 그리기 : 단계 1**

add-to-cart 코드의 다이어그램을 그려보자. 모두 세 단계로 그릴 수 있다.

1) 액션을 확인한다.

2) 순서대로 실행되거나 동시에 실행되는 액션을 그린다.

3) 플랫폼에 특화된 지식을 사용해 다이어그램을 단순하게 만든다.

### **1. 액션을 확인한다.**

액션을 확인하고, 계산은 다이어그램을 그릴 때 신경쓰지 않아도 된다.


```js
function add_item_to_cart(name, price, quantity){
  cart = add_item(cart, name, price, quantity);
  //읽기         //읽기 
calc_cart_total();
}

function calc_cart_total(){
  total = 0;//쓰기
  cost_ajax(cart, function(cost){
  //부르기   //읽기  
    total += cost;
    //쓰기 
    shipping_ajax(cart, function(shipping){
    //부르기     //읽기
      total += shipping;
      // 읽기 쓰기 
      update_total_dom(total); 
      //부르기        //읽기
    })
  })
}
```

액션 
1) cart 읽기

2) cart 쓰기

3) total = 0 쓰기

4) cart 읽기

5) cost_ajax() 부르기

6) total 읽기

7) total 쓰기

8) cart 읽기

9) shipping_ajax() 부르기

10) total 읽기

11) total 쓰기

12) total 읽기

13) update_total_dom() 부르기

이 짧은 코드에 13개의 액션이 있다. 그리고 비동기 콜백이 두 개 있는 것도 주의

<br/>

---

<br/>

## **💻 비동기 호출은 새로운 타임라인으로 그린다.**

비동기 콜백은 새로운 타임라인에 표시해야 한다. 먼저 비동기 콜백이 어떻게 동작하는지 아는 것이 중요

🔻 예시 - 사용자와 문서를 저장하는 과정에서 로딩 상태를 보여주는 코드

```js
saveUserAjax(user, function(){
  //서버에 사용자를 저장(ajax)
  setUserLoadingDOM(false);// 사용자 로딩 표시를 감춤
});

setUserLoadingDOM(true); // 사용자 로딩 표시 보여줌
saveDocumentAjax(document, function(){
  setDocLoadingDOM(false); // 문서 로딩 표시를 감춤
});
setDocLoadingDOM(true) //문서 로딩 표시 보여줌
```

이 코드는 각 라인이 코드의 순서와 다르게 실행될 수 있는 코드다.

### **액션 확인해보기**

user와 document는 지역변수라고 가정하면 읽는 것은 액션이 아니다.

액션

1) savaUserAjax()

2) setUserLoadingDOM(false)

3) setUserLoadingDOM(true)

4) setDocumentAjax()

5) setDocLoadingDOM(false)

6) setDocLoadingDOM(true)

### **액션 그려보기**

완성되면 다음과 같은 모습이 된다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9f177f70-e91a-43b1-9285-0fe0d6b31454)

<br/>

---

<br/>

## **💻 서로 다른 언어, 서로 다른 스레드 모델**

자바스크립트는 단일 스레드, 비동기 모델을 사용한다.

하지만 모든 언어가 그런 것은 아니다.

다른 언어에서 사용하는 스레드 모델에 대해 알아보자.

### **단일 스레드, 동기**

기본적으로 멀티스레드를 지원하지 않는 언어도 있다. 

- 예) PHP

모든 것이 순서대로 실행되고 입출력을 사용하면 끝날 때까지 기다려야 한다.

시스템이 단순하다는 장점

<br/>

### **단일 스레드, 비동기**

대표적으로 자바스크립트

입출력 작업을 하려면 비동기 모델을 사용해야 한다.

입출력의 결과는 콜백으로 받을 수 있지만, 언제 끝날지 알 수없기 때문에 다른 타임라인에 표시

<br/>

### **멀티스레드**

자바, 파이썬, 루비, C, C# 과 같은 많은 언어들이 지원

실행 순서를 보장하지 않기 때문에 프로그래밍하기 매우 어렵다.

새로운 스레드가 생기면 새로운 타임라인을 그려야 한다.

<br/>

### **메세지 패싱(message-passing) 프로세스**

엘릭서, 얼랭 같은 언어

서로 다른 프로세스를 동시에 실행할 수 있는 스레드 모델을 지원

프로세스는 서로 메모리를 공유하지 않고 메세지로 통신

서로 다른 타임라인에 있느 액션은 순서가 섞이지만, 메모리를 공유하지 않기 때문에 가능한 실행 순서가 많아도 문제가 되지 않음

<br/>

---

<br/>

## **💻 타임라인 다이어그램으로 순서대로 실행되는 코드에도 두 가지 종류가 있다는 것을 알 수 있다.**

순서대로 실행되지만 순서가 섞일 수 있는 코드와 그렇지 않은 코드 모두 타임라인 다이어그램으로 표현할 수 있다.

### **🍳 순서가 섞일 수 있는 코드**

두 액션 사이에 시간은 얼마나 걸릴지 알 수 없다.

액션은 박스로 표시하고 액션사이에 걸리는 시간은 선으로 표시

<br/>

### **🍳 순서가 섞이지 않는 코드**

액션을 같은 상자 안에 그림

<br/>

---

<br/>

## **💻 타임라인 다이어그램으로 동시에 실행되는 코드는 순서를 예측할 수 없다는 것을 알 수 있다.**

순차적으로 실행되는 코드뿐만 아니라 동시에 실행되는 코드를 표현할 수 있다.

동시에 실행되는 코드는 실행 순서를 확신할 수 없다.

동시에 실행되는 코드는 타임라인 다이어그램에 나란히 표현한다.

나란히 표현되지만 항상 정확히 동시에 실행된다는 의미는 아님.

<br/>

---

<br/>

## **💻 좋은 타임라인의 원칙**

1) 타임라인은 적을수록 이해하기 쉽다.
  - 타임라인이 하나인 시스템이 가장 이해하기 쉽다.
  - 하지만 요즘 시스템에는 여러 타임라인이 필요하다.
  - 멀티스레드, 비동기 콜백, 클라이언트-서버 간 통신 등에 새로운 타임라인이 필요

2) 타임라인은 짧을수록 이해하기 쉽다.
  - 타임라인의 단계를 줄이면 실행 가능한 순서도 많이 줄일 수 있다.

3) 공유하는 자원이 적을수록 이해하기 쉽다.
  - 서로 다른 타임라인에 있는 두 액션이 서로 자원을 공유하지 않는 다면 실행 순서에 신경 쓸 필요가 없다.
  - 서로 자원을 공유하는 액션을 주의 깊게 살펴보자.

4) 자원을 공유한다면 서로 조율해야 한다.
  - 공유 자원을 안전하게 공유할 수 있어야 한다.
    - 올바른 순서대로 자원을 쓰고 돌려준다는 말
   - 타임라인 간 조율은 올바른 결과를 주지 않는 실행 순서를 없애는 것

5) 시간을 일급으로 다룬다.
  - 타임라인 다루는 재사용 가능한 객체를 만들면 타이밍 문제를 쉽게 만들 수 있다.

<br/>

---

<br/>

## **💻 자바스크립트의 단일 스레드**

자바스크립트의 스레드 모델은 타임라인이 자원을 공유하면서 생기는 문제를 줄여줌

하나의 메인 스레드만 있어서 대부분의 액션을 하나의 박스로 표현할 수 있음

자바스크립트에서 스레드를 사용한다면 같은 스레드를 계속 사용하는 것. 

두 액션이 동시에 실행될 일이 없음

하지만 비동기 콜백을 함께 사용한다면 문제가 생길 수 있다.

비동기 호출은 미래에 알 수 없는 시점에 런타임에 의해 실행

<br/>

---

<br/>

## **💻 자바스크립트의 비동기 큐**

브라우저에서 동작하는 자바스크립트 엔진은 `작업 큐(job queue)`라고 하는 큐를 가지고 있다.

작업 큐는 `이벤트 루프(event loop)`에 의해 처리

이벤트 루프는 큐에서 작업 하나를 꺼내 실행하고 완료되면 다음 작업을 꺼내 실행하는 것을 무한 반복한다.

이벤트 루프는 하나의 스레드에서 처리하기 때문에 두 개의 작업이 동시에 진행될 수 없다. 

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9d001052-121a-49ea-82c4-10c62d366651)

<br/>

### **🤔 작업이란 무엇인가요?**

작업 큐에 있는 작업은 이벤트 데이터와 이벤트를 처리할 콜백으로 구성되어 있다.

이벤트 루프는 이벤트 데이터를 인자로 콜백을 부른다.

- 콜백은 이벤트 루프가 실행할 함수

이벤트 루프는 단순히 첫 번째 인자에 이벤트 데이터를 넣어 콜백 함수를 실행

<br/>

### **🤔 작업은 큐에 어떻게 들어가나요?**

이벤트가 발생하면 큐에 작업이 추가 

- 이벤트는 마우스 클릭이나 키보드 입력, AJAX 이벤트 같은 것

만약 콜백 함수가 있는 버튼에 이벤트가 발생하면 콜백 함수와 이벤트 데이터(클릭에 대한 정보)가 큐에 추가된다.

마우스 클릭처럼 어떤 이벤트도 예상할 수 없기 때문에 이벤트는 `예측 불가능한`시점에 작업 큐에 들어간다.

들어간 작업은 작업 큐에서 나중에 온전히 가져온다.

<br/>

### **🤔 작업이 없을 때 엔진은 무엇을 하나요?**

처리할 작업이 없을 때 이벤트 루프는 대기 상태로 들어가고 전원을 아낀다.

또는 가비지 컬렉션 같은 관리 작업을 수행 

이것은 브라우저 개발자에게 달려있는 부분

<br/>

---

<br/>

## **💻 AJAX와 이벤트 큐**

AJAX는 브라우저에 기반을 둔 웹 요청을 말한다.

Asynchronous JavaScript And XML의 약자다.

근데 조금 이상한데 XML을 사용하짐 않는데 용어는 그대로 남아있다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/0cba3d60-64b7-46cc-8197-f17be4de7456)

자바스크립트에서 AJAX 요ㅗ청을 만들면 네트워크 엔진이 AJAX 요청을 처리하기 위해 요청 큐에 넣는다.

요청 큐에 작업이 추가되어도 코드는 계속 실행된다. 

- 요청이 완료될 때까지 기다릴 필요가 없음

AJAX를 사용하는 부분은 `비동기`


### **🤔 요청에 대한 응답을 기다리지 않으면 응답을 어떻게 받나요?**

AJAX 요청을 만들 때 요청에 관련된 다양한 이벤트 콜백을 등록 할 수 있다.

콜백은 이벤트가 발생했을 때 실행되는 함수

<br/>

---

<br/>

## **💻 요약 : 타임라인 다이어그램 그리기**

### **🍳 액션을 확인하기**

모든 액션은 타임라인에 표시되어야 한다.

<br/>

### **🍳 액션을 그리기**

액션은 순서대로 실행되거나 동시에 실행될 수 있다.

#### **순서대로 실행되는 액션 : 하나가 끝나고 다음이 시작**

순서대로 실행되는 액션은 같은 타임라인에 표시

#### **동시에 실행되는 액션 : 동시에 실행되거나 왼쪽 먼저 또는 오른쪽 먼저 실행**

동시에 실행되거나 순서가 섞여서 실행되는 경우 분리된 타임라인으로 표시

- 비동기 콜백
- 멀티 스레드
- 멀티 프로세스
- 여러 장치

모든 액션을 그린 후 실행 순서를 제한하기 위해 점섬을ㄹ 사용할 수 있다.

<br/>

### **🍳 타임라인 단순화 하기**

언어의 특징을 사용해서 타임라인을 단순화할 수 있다.

- 순서가 섞이지 않는 두 액션은 하나의 박스로 합친다.
- 타임라인 끝에서 새로운 타임라인이 하나만 생긴다면 하나로 합친다.
- 순서에 제약이 있는 경우 점선을 추가한다.

<br/>

### **🍳 타임라인 읽기**

일반적으로 서로 다른 타임라인에 있는 액션은 세 가지 순서로 실행될 수 있다.

동시에 실행되거나 왼쪽이 먼저 실행되거나 오른쪽이 먼저 실행될 수 있다.

<br/>

---

<br/>

## **💻 요점 정리**

- 타임라인은 동시에 실행될 수 있는 순차적 액션을 말한다.
  - 코드가 순서대로 실행되는지 동시에 실행되는지 알 수 있다.

- 현대 소프트웨어는 여러 타임라인에서 실행된다. 
  - 서로 다른 컴퓨터나 스레드, 프로세스, 비동기 호출 같은 것이 있다면 새로운 타임라인을 추가한다.

- 서로 다른 타임라인에 있는 액션은 끼어들 수 있어서 여러 개의 실행가능한 순서가 생긴다.
  - 실행 가능한 순서가 많으면 코드가 항상 올바른 결과를 내는지 알기 어렵다

- 타임라인 다이어그램은 코드가 순서대로 실행되는지 동시에 실행되는지를 알려준다.
  - 타임라인 다이어그램으로 서로 영향을 주는 부분이 어떤 부분인지 알 수 있다.

- 언어에서 지우너한느 스레드 모델을 이해하는 것은 중요하다
  - 분산 시스템에서 어떤 부분이 순서대로 실행되고 어떤 부분이 동시에 실행되는지 이해하는 것이 중요하다.

- 자원을 공유하는 부분은 버그가 발생하기 쉽다.
  - 공유 자원을 확인하고 없애면 코드가 더 좋아진다.

- 자원을 공유하지 않는 타임라인은 독립적으로 이해하고 실행할 수 있다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


