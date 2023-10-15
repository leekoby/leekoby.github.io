---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 17. 
author: admin
date: 2023-10-16 00:00:00 +900
lastmod: 2023-10-16 00:00:00 +900
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

# **🌈 17. 타임라인 조율하기**

이번 장에서 살펴볼 내용

- 타임라인을 조율하기 위한 동시성 기본형을 만들어 본다.
- 시간에 관한 중요한 관점인 순서와 반복을 함수형 개발자들이 어떻게 다루는지

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

새로 버그가 생긴 장바구니에 다섯 번째 원칙을 적용해보자.

<br/>

---

<br/>

## **💻 버그가 있다!**

제품을 하나만 추가해도 가끔 잘못된 합계가 표시됨

- 장바구니에 제품 하나만 추가해도, 또한 여러 개의 제품을 빠르게 추가해도 발생한다.

- 하지만 재현할 수 없다.

먼저 제품 하나를 추가했을 때의 버그를 수정해보자.

<br/>

---

<br/>

## **💻 코드가 어떻게 바뀌었을까**

최적화하기 전에는 모든 코드가 잘 동작했음.

하지만, 이제 가끔 실패함

뭐가 바뀐걸까?

🔻 최적화하기 전(동작✅)

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  update_total_queue(cart);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    shipping_ajax(cart, function(shipping){
      total += shipping;
      callback(total);
    })
  })// 닫는 괄호의 위치가 바뀌었음
}

function calc_cart_worker(cart, done){
  calc_cart_total(cart, function(total){
    update_total_dom(total);
    done(total);
  })
}

let update_total_queue = DroppingQueue(1, calc_cart_worker);
```

🔻 최적화한 후(동작❌)

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  update_total_queue(cart);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    })
  shipping_ajax(cart, function(shipping){
    total += shipping;
    callback(total);
  })// 닫는 괄호의 위치가 바뀌었음
}

function calc_cart_worker(cart, done){
  calc_cart_total(cart, function(total){
    update_total_dom(total);
    done(total);
  })
}

let update_total_queue = DroppingQueue(1, calc_cart_worker);
```

닫는 괄호의 위치가 바뀌었다

왜 이런 일이 벌어지는걸까?

<br/>

---

<br/>

## **💻 액션을 확인하기 : 단계 1**

> <strong  style="color:#3366ff">다이어그램을 그리기 위한 세 단계</strong> 
> 
> <strong  style="color:#ff6600">액션을 확인하기</strong>
> 
> <strong  style="color:#ff6600">각 액션을 그리기</strong>
> 
> <strong  style="color:#ff6600">단순화하기</strong>
{:.prompt-info }


```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
                //액션
  update_total_queue(cart);
}  //액션            //액션

function calc_cart_total(cart, callback){
  let total = 0;
      // 액션
  cost_ajax(cart, function(cost){
    //액션
    total += cost;
    //액션
    })
  shipping_ajax(cart, function(shipping){
     total += shipping;
     //액션
     callback(total);
              //액션
  })
}

function calc_cart_worker(cart, done){
  calc_cart_total(cart, function(total){
    update_total_dom(total);
    //액션
    done(total);
  })
}

let update_total_queue = DroppingQueue(1, calc_cart_worker);
```

안전하게 공유되는지?

어떤 가정을 하지 않고 타임라인 다이어그램을 그리는 것이 중요

<br/>

---

<br/>

## **💻 모든 액션을 그리기 : 단계 2**

첫 번째 단계를 진행하면서 코드에 모든 액션을 확인했다.

일단 전에 했던 가정들은 모두 무시할 것!

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  update_total_queue(cart);
} 

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    })
  shipping_ajax(cart, function(shipping){
     total += shipping;
     callback(total);
  })
}

function calc_cart_worker(cart, done){
  calc_cart_total(cart, function(total){
    update_total_dom(total);
    done(total);
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

이를 통해 다이어 그램을 그려보면

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5f74c2aa-55cd-4859-b7c8-d660c0c742fc)

<br/>

---

<br/>

## **💻 다이어그램 단순화하기**

### **🍳 자바스크립트 스레드 모델에서 단순화하기 위한 단계**

1. 하나의 타임라인에 있는 모든 액션을 하나로 통합

2. 타임라인이 끝나느 곳에서 새로운 타임라인이 하나만 생긴다면 통합

첫 번째 단계

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/539abb05-1f5e-47a6-aa73-aee0e887e56d)

- 실행순서를 명확하게 하기 위해 점선을 단계가 끝나는 지점으로 이동

두 번째 단계

- 타임라인이 끝나는 곳에 새로운 타임라인이 생긴다면 하나로 합치는 단계

- 새로 생기는 타임라인이 하나만 있어야 적용할 수 있음

- 큐 타임라인과 두 ajax 콜백 타임라인은 합칠 수 없음

  - 큐 타임라인이 끝나면서 두 개의 타임라인이 생기기 때문

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/135719b5-0776-4709-89dd-89d8f602d584)

- 이제 타임라인에 있는 단계가 어떤 자원을 공유하는지 알 수 있음

- 공유자원은 total 변수뿐

  - total은 지역변수지만 모든 타임라인에서 접근하고 있다.

<br/>

---

<br/>

## **💻 실행 가능한 순서 분석하기**

앞서 타임라인 다이어그램을 완성하고 타임라인이 공유하는 자원은 total 변수뿐이라는 것을 확인했다.

두 콜백 타임라인의 실행 가능한 순서를 알아보자.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9fa4f43b-3829-444a-a094-87134cb08381)

- 두 콜백이 기대하지 않은 수너로 실행될 수 있음

- 요청 순서는 올바르지만 실행 순서가 바뀔 수 있음

왜 잘못된 코드가 더 빠르게 실행되는거지?

<br/>

---

<br/>

## **💻 왜 지금 타임라인이 더 빠를까?**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/3c7a6a94-7f25-42e2-abce-45a33a1ae179)

cost_ajax() 응답은 3초가 걸리고 shipping_ajax()는 4초가 걸린다고 가정해보자.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/39316c67-d088-4474-9688-7b60424acbe3)

- 왼쪽 타임 라인은 두 응답을 **순서대로** 기다려야 한다. 따라서 걸리는 시간을 더해야 한다.

- 오른쪽 타임라인은 두 응답을 **병렬**로 기다린다. 따라서 둘 중 더 긴 시간이 걸린다.

- 그러므로 오른쪽 타임라인이 더 빨리 끝난다.

실패하지 않고 병렬로 응답을 기다려 실행 속도를 개선할 수 있을까?

-> 동시성 기본형!

<br/>

---

<br/>

## **💻 모든 병렬 콜백 기다리기**

동시에 도착하는 ajax 응답을 모두 기다렸다가 DOM을 업데이트하자.

원하는 결과는 다음과 같다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/66e5f373-cf50-436d-95b1-d78e07a654af)

두 콜백은 서로 끝나기 를 기다린다.

다이어그램에서 점선이 이러한 것을 나타내며 `컷`이라고 해보자. 

컷은 앞서 사용했던 점선처럼 순서를 보장해주는 역할

- 컷은 앞에서 본 점선과 다르게 여러 타임라인의 끝에 맞춰 그림

- 타임라인에 컷이 있다면 컷 위에 있는 단계는 컷 아래가 실행되기 전에 모두 종료되어야 함

컷의 장점

- 타임라인을 앞부분과 두시부분으로 구분

- 컷을 기준으로 앞과 뒤의 타임라인을 따로 분석할 수 있음

- 컷의 앞부분과 뒷부분의 액션들은 서로 섞이지 않음

- 실행 가능한 순서를 줄여주기 때문에 애플리케이션의 복잡성을 줄여줌

<br/>

---

<br/>

## **💻 타임라인을 나누기 위한 동시성 기본형**

- 여러 타임라인이 다른 시간에 종료되도 서로 기다릴 수 있는 간단하고 재사용할 수 있는 기본형이 필요

- 여러 타임라인이 실행되는 순서를 신경쓰지 않아도 되고 타임라인이 모두 끝나는 것도 쉽게 처리할 수 있기 때문

- 경쟁 조건을 막을 수 있다.

> <strong  style="color:#3366ff">경쟁조건</strong> 
> 
> <strong  style="color:#ff6600">어떤 동작이 먼저 끝나는 타임라인에 의존할 때 발생</strong>
{:.prompt-info }

- 멀스레드를 지원하는 언어에서는 원자적 업데이트 기능을 사용

- 자바스크립트는 단일 스레드이므로 가능한 동기적으로 접근하는 간단한 변수로 동시성 기본형을 구현할 수 있음

```js
          //기다릴 타임라인 수 
function Cut(num, callback){ // 모든 것이 끝났을 때 실행할 콜백
  var num_finished = 0; //카운터 0으로 초기화
  return function(){
          // 리턴되는 함수는 타임라인이 끝났을 때 호출
          num_finished += 1; // 함수를 호출할 때마다 카운터 증가
          if(num_finished === num){
            callback(); // 마지막 타임라인이 끝났을 때 호출
          }
  }
}
```

<br/>

---

<br/>

## **💻 코드에 Cut() 적용하기**

장바구니에 제품을 추가하는 코드에 적용해보자

1. Cut() 을 보관할 범위
2. Cut() 에 어떤 콜백을 넣을지

🔻 Cut() 을 적용한 코드

```js
  function calc_cart_total(cart, callback){
    var total = 0;
    let done = Cut(2, function(){
      callback(total);
    })
  }

  cost_ajax(cart, function(cost){
    total += cost;
    done();
  })

  shipping_ajax(cart, function(shipping){
    total += shipping;
    done();
  })
```

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8cfbb5c6-0623-47d6-9bf4-b5cf8fad4520)

<br/>

---

<br/>

## **💻 불확실한 순서 분석하기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/609fd35d-f717-439b-9724-7c788ca6ad2a)

<br/>

---

<br/>

## **💻 병렬 실행 분석**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8a32344b-1257-4798-82db-1c4384c4372e)

<br/>

---

<br/>

## **💻 여러 번 클릭하는 경우 분석**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e629a4af-c980-40a7-8689-f2ba3328e9fa)

처음 보는 표현이 생겼는데, 실제로 타임라인은 두 개지만 Cut()을 사용했기 때문에 하나로 합쳐짐

- 이런 표현은 다이어그램이 유연하다는 것을 보여줌

- 세부적인 것이 많은 복잡한 상황을 분석하기 위해 이런 표현을 사용하면 좋음

> <strong  style="color:#3366ff">가능한 실행 순서의 개수 공식</strong> 
> 
> ![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/df2dc8a6-021c-4277-90a7-244fbd9f61e9)
{:.prompt-info }


<br/>

---

<br/>

## **💻 암묵적 시간 모델 VS 명시적 시간 모델**

자바스크립트의 시간 모델은 간단함

1. 순차적 구문은 순서대로 실행된다.

2. 두 타임라인에 있는 단계는 왼쪽 먼저 실행되거나, 오른쪽 먼저 실행될 수 있다.

3. 비동기 이벤트는 새로운 타임라인에서 실행된다.

4. 액션은 호출될 때마다 실행된다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5e357a31-8e43-459e-b705-d6549a5a43cd)

<br/>

---

<br/>

## **💻 요약 : 타임라인 사용하기**

1. 타임라인 수를 줄인다.

2. 타임라인 길이를 줄인다.

3. 공유 자원을 없앤다.

4. 동시성 기본형으로 자원을 공유한다.

5. 동시성 기본형으로 조율한다.

<br/>

---

<br/>

## **💻 요점 정리**

- 함수형 개발자는 언어가 제공하는 암묵적 시간 모델 대신 새로운 시간 모델을 만들어 사용한다. 

  - 새로운 모델은 해결하려고 하는 문제를 푸는 데 도움이 된다.

- 명시적 시간 모델은 종종 일급 값으로 만든다.

  - 일급 값으로 만든 시간 모델은 프로그래밍 언어를 사용해서 시간을 다룰 수 있다.

- 타임라인을 조율하기 위해 동시성 기본형을 만들 수 있다.

  - 간으한 순서를 제한해 항상 올바른 결과가 나올 수 있도록 보장

- 타임라인을 나누는 것도 타임라인을 조율하는 방법 중 하나.

  - 컷은 모든 타임라인의 작업이 끝날 때까지 기다렸다가 새로운 타임라인을 시작할 수 있다.

<br/>

---

<br/>


## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


