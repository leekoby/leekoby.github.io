---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 16. 
author: admin
date: 2023-10-15 09:00:00 +900
lastmod: 2023-10-15 09:00:00 +900
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

# **🌈 16. 타임라인 사이에 자원 공유하기**

이번 장에서 살펴볼 내용

- 자원을 공유해서 생기는 버그를 찾는 방법
- 안전하게 자원을 공유할 수 있는 자원 공유 기본형을 만드는 방법

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

## **💻 DOM이 업데이트되는 순서를 보장해야 한다.**

특정 순서로 DOM이 업데이트 되어야 문제가 없다.

- 타임라인 만으로 두 타임라인의 순서를 보장할 방법은 없다. 

- 업데이트의 순서를 제한해야한다.


클릭한 순서대로 DOM이 업데이트돼야 한다. 

- DOM 업데이트는 아무도 통제할 수가 없다.

- DOM 업데이트는 네트워크 요청이 완료되면 실행

- 네트워크 요청이 완료되는 시점은 통제할 수 없는 많은 변수에 의존 

항상 클릭 순서대로 업데이터되도록 DOM 업데이트를 조율할 방법이 필요하다.

현실에서 어떤 일을 순서대로 진행되게 하는 방법의 하나는 줄을 서는 것

큐(queue)는 들어온 순서대로 나오는 데이터 구조다.

- 사용자 클릭을 큐에 넣으면 넣은 순서대로 꺼낼 수 있다. 

- 여러 타임라인에 있는 액션 순서를 조율하기 위해 많이 사용

- 큐는 공유자원이지만 안전하게 공유

  - 순서대로 작업을 꺼내 쓸 수 있기 때문

- 큐에 있는 모든 작업 라인은 타임라인에서 처리되기 때문에 순서가 관리됨

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/72eb5c88-c91e-4553-a224-2c5fe4c3f0bf)

<br/>

---

<br/>

## **💻 자바스크립트에서 큐 만들기**

### **🍳 자바스크립트에서 큐 자료구조가 없기 때문에 만들어야 한다.**

큐는 자료구조지만 타임라인 조율에 사용한다면 <strong  style="color:#ff6600">동시성 기본형</strong>이라고 부른다.

동시성 기본형은 자원을 안전하게 공유할 수 있는 재사용 가능한 코드를 말한다.

- 기본적으로 제공하는 언어도 있음

- 자바스크립트는 없어서 직접 구현

큐에서 처리할 일들 나눠보자.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5ba1d0ab-68cb-4daf-bcec-66b680e35f6a)

클릭 핸들러 다이어그램을 통해 큐에서 할 일과 핸들러에서 할일을 찾아보자

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/dcb58915-479f-4cf4-bdab-6e1e4120c3e4)

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  calc_cart_total(cart, update_total_dom);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    shipping_ajax(cart, function(shipping){
      total += shipping;
      callback(total);
    })
  })
}
```

가능한 많은 작업을 클릭 핸들러에서 하는 것이 좋다.

- 클릭 핸들러에는 다른 작업이 끼어들 수 없어서 순서가 섞일 염려가 없다.

<br/>

### **🍳 큐에서 처리할 작업을 큐에 넣기**

- 현재 모든 코드는 타임라인 하나에 있다.
- 큐에서 처리할 작업을 다른 타임라인으로 옮겨야 함

먼저 큐에서 처리할 작업을 큐에 넣는 액션 하나로 바꾸는 작업을 해보자

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/548447b9-25ca-4089-8315-68bf1c019abc)

🔻 현재 코드
```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  calc_cart_total(cart, update_total_dom);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    shipping_ajax(cart, function(shipping){
      total += shipping;
      callback(total);
    })
  })
}
```

🔻 새로운 코드

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
  })
}

let queue_items=[];
function update_total_queue(cart){
  queue_items.push(cart);
}
```

지금까지의 큐는 단순하다. 그냥 배열 끝에 항목을 추가하는 간단한 코드

<br/>

### **🍳 큐에 있는 첫 번째 항목 실행하기**

큐 끝에 항목을 넣었기 때문에 작업을 실행할 수 있다. (가장 앞에 있는 순서를 꺼내 작업을 시작)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/908ef661-1646-4d94-9713-8f664cd29c79)

🔻 현재 코드

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
  })
}

let queue_items=[];

function update_total_queue(cart){
  queue_items.push(cart);
}
```

🔻 새로운 코드

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  update_total_queue(cart, update_total_dom);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    shipping_ajax(cart, function(shipping){
      total += shipping;
      callback(total);
    })
  })
}

let queue_items=[];

function runNext(){
  let cart = queue_item.shift();
  calc_cart_total(cart, update_total_dom)
}

function update_total_queue(cart){
  queue_items.push(cart);
  setTimeout(runNext, 0);// 이벤트 루프에 작업을 추가
}
```

<br/>

### **🍳 두 번째 타임라인이 첫 번째 타임라인과 동시에 실행되는 것을 막기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/dceae668-293e-464c-a5da-8347891cedd4)

🔻 새로운 코드

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  update_total_queue(cart, update_total_dom);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    shipping_ajax(cart, function(shipping){
      total += shipping;
      callback(total);
    })
  })
}

let queue_items=[];
let working = false;

function runNext(){
  if(working) return;
  working = true
  let cart = queue_item.shift();
  calc_cart_total(cart, update_total_dom)
}

function update_total_queue(cart){
  queue_items.push(cart);
  setTimeout(runNext, 0);// 이벤트 루프에 작업을 추가
}
```

두 타임라인이 동시에 실행되는 것은 막았으나 추가된 작업이 항상 하나만 실행된다. 

현재 작업이 끝났을 때 다음 작업을 실행하려면?

<br/>

### **🍳 다음 작업을 시작할 수 있도록 calc_cart_total() 콜백 함수 고치기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b17b5b38-e78e-4bc9-be64-041adcab8e8c)


🔻 새로운 코드

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  update_total_queue(cart, update_total_dom);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    shipping_ajax(cart, function(shipping){
      total += shipping;
      callback(total);
    })
  })
}

let queue_items=[];
let working = false;

function runNext(){
  if(working) return;
  working = true
  let cart = queue_item.shift();
  calc_cart_total(cart, function(total){
    update_total_dom(total); 
    working = false; 
    // 작업 완료를 표시하고 다음 작업 시작
    runNext();
  })
}

function update_total_queue(cart){
  queue_items.push(cart);
  setTimeout(runNext, 0);
}
```

비동기로 작업을 이어서 할 수 있는 구조로 바꿈

하지만 배열이 비었을 때 멈추지 않는 문제가 있음

<br/>

### **🍳 항목이 없을 때 멈추게 하기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/d354d60c-1075-4985-81d5-adda9c49beb9)


🔻 새로운 코드

```js
function add_item_to_cart(item){
  cart = add_item(cart, item);
  update_total_queue(cart, update_total_dom);
}

function calc_cart_total(cart, callback){
  let total = 0;
  cost_ajax(cart, function(cost){
    total += cost;
    shipping_ajax(cart, function(shipping){
      total += shipping;
      callback(total);
    })
  })
}

let queue_items=[];
let working = false;

function runNext(){
  if(working) return;
  if(queue_items.length === 0)return;
  working = true
  let cart = queue_item.shift();
  calc_cart_total(cart, function(total){
    update_total_dom(total);
    working = false;
    runNext();
  })
}

function update_total_queue(cart){
  queue_items.push(cart);
  setTimeout(runNext, 0);
}
```

이제 잘 동작하게 됨

많이 빠르게 클리갷도 순서대로 처리할 수 있음

<br/>


### **🍳 변수와 함수를 함수 범위로 넣기**

지금 코드는 두 개의 전역변수를 변경하고 있음

Queue()라는 함수에 전역변수와 사용하는 함수를 넣어 다른 곳에서 접근할 수 없도록 만들어보자.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9266df8c-5cd1-4603-b537-0498660e9e94)

🔻 새로운 코드

```js
function Queue(){ // 모든 코드를 Queue에 넣음
  // 전역변수가 지역변수로 
  let queue_items=[];
  let working = false;
  
  function runNext(){
    if(working) return;
    if(queue_items.length === 0) return;
    working = true
    let cart = queue_item.shift();
    calc_cart_total(cart, function(total){
      update_total_dom(total);
      working = false;
      runNext();
   });
  }
  // 항목을 넣을 수 있는 함수를 리턴
  return function(cart){
    queue_items.push(cart);
    setTimeout(runNext, 0);
  }
}

// 리턴된 함수를 원래 함수처럼 사용
let update_total_queue = Queue();
```

<br/>

---

<br/>

## **💻 원칙 : 공유하는 방법을 현실에서 착안하기**

컴퓨터는 공유하는 방법을 모르기 때문에 직접 프로그래밍 해줘야 공유할 수 있음

<br/>

---

<br/>

## **💻 큐를 재사용할 수 있도록 만들기**

### **🍳 done() 함수 빼내기**

함수 본문을 콜백으로 바꾸기 리팩터링

🔻 새로운 코드

```js
function Queue(){
  let queue_items=[];
  let working = false;
  
  function runNext(){
    if(working) return;
    if(queue_items.length === 0) return;
    working = true
    let cart = queue_item.shift();
    
    function worker(cart, done){ // done은 콜백 함수 이름
      calc_cart_total(cart, function(total){
        update_total_dom(total);
        done(total);
      });
    }
    
    worker(cart, function (){
        working = false;
        runNext();
      })
  }
  
  return function(cart){
    queue_items.push(cart);
    setTimeout(runNext, 0);
  }
}

let update_total_queue = Queue();
```

done() 콜백으로 큐 타임라인 작업을 이어서 할 수 있다.

<br/>

### **🍳 워커 행동을 바꿀 수 있도록 밖으로 분리**

함수를 인자로 빼는 리팩터링으로 특정한 행동을 하는 코드를 없애고, 큐가 생성될 때 원하는 행동을 전달할 수 있다.

🔻 새로운 코드

```js
function Queue(worker){
  let queue_items=[];
  let working = false;
  
  function runNext(){
    if(working) return;
    if(queue_items.length === 0) return;
    working = true
    let cart = queue_item.shift();
   
    worker(cart, function (){
        working = false;
        runNext();
      })
  }
  
  return function(cart){
    queue_items.push(cart);
    setTimeout(runNext, 0);
  }
}

 function calc_cart_worker(cart, done){ 
      calc_cart_total(cart, function(total){
        update_total_dom(total);
        done(total);
      });
}

let update_total_queue = Queue(calc_cart_worker);
```

일반적인 큐를 만들었다.

Queue()에 있는 기능은 모두 일반적인 기능이다.

원하는 기능을 넘길 수 있다.

<br/>


### **🍳 작업이 끝났을 때 실행하는 콜백 받기**

🔻 새로운 코드

```js
function Queue(worker){
  let queue_items=[];
  let working = false;
  
  function runNext(){
    if(working) return;
    if(queue_items.length === 0) return;
    working = true
    let item = queue_item.shift();
   
    worker(item.data, function (){
        working = false;
        runNext();
      })
  }
  
  return function(data, callback){
    queue_items.push({
      data: data,
      callback : callback || function (){}
    });
    setTimeout(runNext, 0);
  }
}

 function calc_cart_worker(cart, done){ 
      calc_cart_total(cart, function(total){
        update_total_dom(total);
        done(total);
      });
}

let update_total_queue = Queue(calc_cart_worker);
```

콜백의 기본값을 설정하기 위해 `||` 사용

두 번째 인자를 전달하지 않으면 `undefined`가 될 수 있다.

항상 실행되야하므로 `undefined`인 경우 아무것도 하지 않는 함수로 설정

<br/>

### **🍳 작업이 완료되었을 때 콜백 부르기**

🔻 새로운 코드

```js
function Queue(worker){
  let queue_items=[];
  // 일반적인 함수이기 때문에 가능한 일반적인 이름
  let working = false;
  
  function runNext(){
    if(working) return;
    if(queue_items.length === 0) return;
    working = true
    let item = queue_item.shift();
   
    worker(item.data, function (val){
        working = false;
        setTimeout(item.callback, 0, val);//콜백인자 전달
        runNext();
      })
  }
  
  return function(data, callback){
    queue_items.push({
      data: data,
      callback : callback || function (){}
    });
    setTimeout(runNext, 0);
  }
}

 function calc_cart_worker(cart, done){ 
      calc_cart_total(cart, function(total){
        update_total_dom(total);
        done(total);
      });
}

let update_total_queue = Queue(calc_cart_worker);
```

일반적인 함수이기 때문에 일반적인 함수명과 변수명을 사용

변수명은 구체화 단계에 따라 하는 일을 표현

<br/>

### **🍳 Queue()는 액션에 새로운 능력을 줄 수 있는 함수**

Queue()는 함수를 인자로 받아 또 다른 함수를 리턴하는 함수

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/17f25b8f-28d3-4d3c-9a27-a4ee88fcee15)

어떤 함수를 새로운 타임라인에서 실행하고 한 번에 한 타임라인만 실행할 수 있도록 만들어주는 고차 함수

다음과 같은 타임라인을

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c8166efb-27e7-450f-bffd-3191962be49c)

아래와 같은 타임라인으로 바꿔준다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1d80d611-2248-41fb-bf45-1d79b0ba36c1)

- Queue() 는 액션에 **순서 보장**을 주는 도구로 볼 수 있다.

- Queue() 라는 말 대신 `linearize()` 라고 할 수도 있다.

- Queue() 는 **동시성 기본형**

  - 여러 타임라인을 올바르게 동작하도록 만드는 재사용 가능한 코드

<br/>

---

<br/>

## **💻 지금까지 만든 타임라인 분석**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/dffcdf22-883b-4173-8403-a5a0d9fb028f)

지금까지 만든 타임라인 다이어그램. 공유하는 리소스는 아이콘으로 강조

장바구니 전역변수, 큐, DOM 을 공유하고 있음

먼저 장바구니 전역변수부터 확인해보면 

- 클릭 타임라인에서 모두 사용하고 있음.

- 제품을 추가하기 위해 클릭하면 장바구니 전역변수에 세 번 접근

- 하지만 모두 같은 박스에서 동기적으로 실행

실행되는 단계를 확인해 보면

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f1c2c1e1-fef4-4762-ab8b-fc0fa453447e)

장바구니 전역변수는 공유할 때 문제가 없는 것 같다.

다음으로 DOM을 보면

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/47a09d7b-55fc-43e8-8306-ad9b53034142)

큐를 사용해 DOM 업데이트가 같은 타임라인에서 하도록 만들었기 때문에 순서에 문제가 생기지 않는다.

- 같은 타임라인에 있기 때문에 가능한 순서를 따질 필요가 없다.(하나의 타임라인에 있는 액션은 항상 순서대로 실행)

마지막으로 큐는 모든 타임라인에서 서로 다른 단계에서 공유하고 있는 것은 문제가 있는거 같다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5df2b290-b429-429b-a949-690a34f74e12)

```js
// 큐에 추가하기
queue_items.push({
  data : data,
  callback : callback
});

// 큐에서 꺼내기 
queue.items.shift();
```

- 다이어 그램을 보면 큐에 추가하는 두 단계 중 하나는 항상 먼저 실행

- 큐에서 꺼내는 단계 두 개 중 하나는 항상 마지막에 실행(점선으로 알 수 있음) 

- 남은 두 단계는 순서가 섞일 수 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e5506a8b-0123-4663-a56e-b61451dbf137)

- 액션의 순서가 바뀌는 것은 막을 수 없다.

  - 하지만 두 순서 모두 올바른 결과가 나옴

- 동시성 기본형인 큐가 이런 것을 보장해 준다.

<br/>

---

<br/>

## **💻 원칙 : 문제가 있을 것 같으면 타임라인 다이어그램을 살펴보자**

- 타임라인 다이어그램의 가장 큰 장점은 타이밍 문제를 ㅁ여확히 보여주는 것

- 공유된 자원이 잘 못된 순서로 사용되는지 알 수 있음

- 타이밍에 관한 버그는 재현하기 매우 힘들기 때문에 타임라인 다이어그램이 필요

- 타임라인 다이어그램은 모든 실행가능한 순서를 포함해 소프트웨어가 어떻게 동작하는지 이해하는 데 쓸 수 있는 유연한 도구

<br/>

---

<br/>

## **💻 큐를 건너뛰도록 만들기**

### **🍳 올바른 순서로 동작하는 것 같지만 너무 느려요!**

현재 큐를 구현한 방식에 따르면

- 워커는 각각의 작업이 끝나야 다음으로 진행 👉 이러한 방식은 매우 느림

- 정말 빠르게 여러번 클릭할 경우 마지막 합계만 표시하면 됨.

  - 하지만 큐에 있는 작업이 한 번에 하나씩만 처리됨.

  - AJAX 요청이 있어서 최종 결과가 업데이트될 때까지 몇 초가 걸릴 수 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6a7bc759-c55f-4395-a96f-2601dbbefb27)

코드를 개선할 필요가 있음

- 큐에 있는 마지막 업데이트만 필요 👉 다른 업데이트는 다음 업데이트가 끝나자마자 덮어써짐

- 덮어쓸 항목을 큐에서 빼면 어떻게 될까? 

새로운 작업이 들어오면 건너뛸 수 있도록 드로핑(dropping) 큐를 만들어보자.

🔻 드로핑 큐

```js
function DroppingQueue(max, worker){
  //max는 보관할 수 있는 최대 큐 크기
  let queue_items=[];
  let working = false;
  
  function runNext(){
    if(working) return;
    if(queue_items.length === 0) return;
    working = true
    let item = queue_item.shift();
   
    worker(item.data, function (val){
        working = false;
        setTimeout(item.callback, 0, val);
        runNext();
      })
  }
  
  return function(data, callback){
    queue_items.push({
      data: data,
      callback : callback || function (){}
    });
    while(queue_item.length > max){
      // 큐에 추가한 후에 항목이 max를 넘으면 모두 버림
      update_total_shift();
    }
    setTimeout(runNext, 0);
  }
}

 function calc_cart_worker(cart, done){ 
      calc_cart_total(cart, function(total){
        update_total_dom(total);
        done(total);
      });
}

let update_total_queue = DroppingQueue(1, calc_cart_worker);
                                      // 한 개 이상은 모두 버림
```

- `max` 값을 `1`로 설정했기 때문에 아무리 빨리 항목을 추가해도 큐 항목이 한개 이상 늘어나지 않음

- 사용자는 계속 기다릴 필요 없이 서버에 응답을 최대 두 번만 기다리면 됨

<br/>

---

<br/>

## **💻 요점 정리**

- 타이밍 문제는 재현하기 어렵고, 테스트로 확인하지 못할 수 있다.

  - 타임라인 다이어그램을 그려 분석하고 타이밍 문제를 확인해 보자

- 자원 공유 문제가 있을 때 현실에서 해결 방법을 찾아 보자.

  - 사람들은 항상 무언가를 문제없이 공유한다. 사람을 통해 배우자

- 재사용 가능한 도구를 만들면 자원 공유에 도움이 된다.

  - 자원 공유를 위한 도구를 동시성 기본형이라고 부른다

  - 동시성 기본형을 사용하면 코드가 더 깨끗하고 단순해 진다.

- 동시성 기본형은 액션을 고차함수로 받는다.

  - 이 고차 함수는 액션에 슈퍼 파워를 준다.

- 동시성 기본형은 스스로 만들기 어렵지 않다.

  - 작은 단계부터 시작해 리팩터링 하면서 스스로 만들 수 있다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


