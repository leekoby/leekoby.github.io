---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 13. 
author: admin
date: 2023-10-09 09:00:00 +900
lastmod: 2023-10-09 09:00:00 +900
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

# **🌈 13. 함수형 도구 체이닝**

이번 장에서 살펴볼 내용

- 복합적인 쿼리로 데이터를 조회하기 위해 함수형 도구를 조합하는 방법
- 복잡한 반복문을 함수형 도구 체인으로 바구는 방법을 이해
- 데이터 변환 파이프라인을 만들어 작업을 수행하는 방법 

## **💻 계속 일하고 있는 고객 커뮤니케이션팀**

### **🍳 조금 더 복잡한 요구 사항**

우수 고객이 가장 많은 비용을 쓸 것으로 생각된다. 그래서 각각의 우수 고객(3개 이상 구매)의 구매 중 가장 비싼 구매를 알려주세요.

➡ 단계들을 조합해 하나의 쿼리로 만들면 된다. 이렇게 여러 단계를 하나로 조합하는 것을 체이닝(chainning)이라고 한다.

우수 고객들의 가장 비싼 구매를 구해야 한다.

1. 우수 고객(3개 이상 구매)을 찾는다(filter).
2. 우수 고객을 가장 비싼 구매로 바꾼다(map).

함수 정의 부터 만들어 보면

```js
function biggestPurchasesBestCustomers(customers){
  
}
```

우수 고객을 필터링 해보자 

```js
function biggestPurchasesBestCustomers(customers){
  let bestCustomers = filter(customers, function(customers){
    return customer.purchases.length >= 3;
  })
}
```

다음으로 각 고객의 가장 비싼 구매를 가져와 배열에 담아 보자

```js
function biggestPurchasesBestCustomers(customers){
  let bestCustomers = filter(customers, function(customers){
    return customer.purchases.length >= 3;
  })

  let biggestPurchases = map(bestCustomers, function (customer){
    return ...?????? // 무엇을 리턴해야할까
  })
}
```

가장 큰 수를 찾는 방법을 reduce()로 해보자.

```js
function biggestPurchasesBestCustomers(customers){
  let bestCustomers = filter(customers, function(customers){
    return customer.purchases.length >= 3;
  })

  let biggestPurchases = map(bestCustomers, function (customer){
    return reduce(customer.purchases, {total:0}, function (biggestSoFar, puchases){
      if(biggestSoFar.total > purchase.total)
        return biggestSoFar
      else 
        return purchase;
    });
  });
  return biggestPurchases;
}
```

잘 동작하는 코드가 됐다. 

하지만 콜백이 여러 개 중첩되어 함수가 너무 커져버렸다.

코드를 깨끗하게 개선해보자.

🔻 원래 코드

```js
  reduce(customer.purchases, {total:0}, function (biggestSoFar, purchases){
      if(biggestSoFar.total > purchase.total)
        return biggestSoFar
      else 
        return purchase;
    });
```

🔻 콜백으로 분리 

```js
maxKey(customer.purchases, {total:0}, function (purchases){
  return purchase.total;
})

function maxKey(array, init, f){
  return reduce(array, init, function(biggestSoFar, element){
    if(f(biggestSoFar) > f(element)){
      return biggestSoFar;
    } else {
      return element;
    }
  })
}
```

배열에서 가장 큰 값을 찾는 함수를 만들었다. 원래 코드에 적용해보자.


```js
function maxKey(array, init, f){
  return reduce(array, init, function(biggestSoFar, element){
    if(f(biggestSoFar) > f(element)){
      return biggestSoFar;
    } else {
      return element;
    }
  })
}

function biggestPurchasesBestCustomers(customers){
  let bestCustomers = filter(customers, function(customers){
    return customer.purchases.length >= 3;
  })

  let biggestPurchases = map(bestCustomers, function (customer){
    return maxKey(customer.purchases, {total:0}, function (purchases){
     return purchase.total;
     })
  });

  return biggestPurchases;
}
```

코드가 간결해졌다. `mayKey()`로 코드가 의미하는 것을 명확하게 표현했다.

`reduce()`는 일반적이기 때문에 낮은 수준의 함수다.

지금 만든 코드도 간결하지만, 더 명확하게 만들 수 있다.

중첩된 리턴 구문이 있는 콜백이 있다. 그래서 코드가 어떤 일을 하는지 알기 어렵다.

이를 해결하기 위한 방법은 두 가지가 있다.

<br/>

---

<br/>

## **💻 체인을 명확하게 만들기 1: 단계에 이름 붙이기**

체인을 명확하게 만드는 첫 번째 방법은 각 단계에 이름을 붙이는 것이다.

```js
// 1단계
function biggestPurchasesBestCustomers(customers){ 
  let bestCustomers = filter(customers, function(customers){
    return customer.purchases.length >= 3;
  })

//2단계
  let biggestPurchases = map(bestCustomers, function (customer){
    return maxKey(customer.purchases, {total:0}, function (purchases){
     return purchase.total;
     })
  });

  return biggestPurchases;
}
```

다음과 같이 각 단계의 고차 함수를 뺴내 이름을 붙일 수 있다.

```js
function biggestPurchasesBestCustomers(customers){ 
  let bestCustomers = selectBestCustomers(customers); // 1단계
  let biggestPurchases = getBiggestPurchases(baseCustomers); // 2단계
  return biggestPurchases
}

function selectBestCustomers(customers) {
  return filter(customers, function(customer){
    return customer.purchases.length >= 3;
  })
}

function getBiggestPurchases(customer){
  return map(customers, getBiggestPurchase);
}

function getBiggestPurchase(customer){
  return maxKey(customer.purchases, {total : 0}, function(purchase){
    return purchase.total;
  })
}
```

각 단계에 이름을 붙이면 훨씬 명확해진다. 그리고 각 단계에 숨어 있던 두 함수의 구현도 알아보기 쉽다. 

콜백 함수는 여전히 인라인으로 사용되고 있는데, 인라인으로 정의된 콜백 함수는 재사용할 수 없다.

이를 해결하기 위해서 더 작은 함수로 쪼개기 위해 두 번째 방법을 보자.

<br/>

---

<br/>

## **💻 체인을 명확하게 만들기 2: 콜백에 이름 붙이기**

체인을 명확하게 하는 두 번째 방법은 콜백에 이름을 붙이는 방법이다.

단계에 이름을 붙이기 전으로 코드를 되돌려보자

```js
// 1단계
function biggestPurchasesBestCustomers(customers){ 
  let bestCustomers = filter(customers, function(customers){
    return customer.purchases.length >= 3;
  })

//2단계
  let biggestPurchases = map(bestCustomers, function (customer){
    return maxKey(customer.purchases, {total:0}, function (purchases){
     return purchase.total;
     })
  });

  return biggestPurchases;
}
```

이번에는 콜백을 빼내 이름을 붙여보자

```js
function biggestPurchasesBestCustomers(customers){ 
  let bestCustomers = filter(customers, isGoodCustomer); // 1단계
  let biggestPurchases = map(baseCustomers, getBiggestPurchase); // 2단계
  return biggestPurchases
}

function isGoodCustomer(customers) {
  return customer.purchases.length >= 3;
}

function getBiggestPurchase(customer){
  return maxKey(customer.purchases, {total : 0}, getPurchaseTotal);
}

function getPurchaseTotal(purchase){
    return purchase.total;
  }
```

콜백을 빼내고 이름을 붙여 재사용할 수 있는 함수로 만들었다.

호출 그래프의 아래쪽에 위치하므로 재사용하기 좋은 코드라는 것을 알 수 있다.

더 직관적으로 재사용하기 좋은 코드처럼 생겼다.

<br/>

---

<br/>

## **💻 체인을 명확하게 만들기 3: 두 방법을 비교**

### **🍳 방법 1: 단계에 이름 붙이기**

```js
function biggestPurchasesBestCustomers(customers){ 
  let bestCustomers = selectBestCustomers(customers); // 1단계
  let biggestPurchases = getBiggestPurchases(baseCustomers); // 2단계
  return biggestPurchases
}

function selectBestCustomers(customers) {
  return filter(customers, function(customer){
    return customer.purchases.length >= 3;
  })
}

function getBiggestPurchases(customer){
  return map(customers, getBiggestPurchase);
}

function getBiggestPurchase(customer){
  return maxKey(customer.purchases, {total : 0}, function(purchase){
    return purchase.total;
  })
}
```

<br/>

### **🍳 방법 2: 콜백에 이름 붙이기**

```js
function biggestPurchasesBestCustomers(customers){ 
  let bestCustomers = filter(customers, isGoodCustomer); // 1단계
  let biggestPurchases = map(baseCustomers, getBiggestPurchase); // 2단계
  return biggestPurchases
}

function isGoodCustomer(customers) {
  return customer.purchases.length >= 3;
}

function getBiggestPurchase(customer){
  return maxKey(customer.purchases, {total : 0}, getPurchaseTotal);
}

function getPurchaseTotal(purchase){
    return purchase.total;
  }
```

<br/>

일반적으로 두 번재 방법이 더 명확하다.

고차 함수를 그대로 쓰는 것보다는 이름을 붙여서 사용하는게 재사용하기 더 좋다.

인라인 대신 이름을 붙여 콜백을 사용하면 중첩되는 것도 막을 수 있다.

<br/>

---

<br/>

## **💻 반복문을 함수형 도구로 리팩터링 하기**

### **🍳 전략 1: 이해하고 다시 만들기**

<br/>

### **🍳 전략 2: 단서를 찾아 리팩터링**

반복문을 하나씩 선택한 다음 함수형 도구 체이닝으로 바꾸자.

```js
let answer = [];
let window = 5;

for(let i = 0 ; i < array.length ; i ++){
  let sum = 0;
  let count = 0;
  for(let w = 0; w< window; w++){
    let idx = i + w;
    if(idx < array.length){
      sum += array[idx];
      count += 1;
    }    
    answer.push(sum/count);
  }
}
```

코드를 전부 이해하지 않더라도 작게 쪼갤 수 있다.

<br/>

---

<br/>

## **💻 팁 1: 데이터 만들기**

```js
let answer = [];
let window = 5;

for(let i = 0 ; i < array.length ; i ++){
  let sum = 0;
  let count = 0;

  let subarray = array.slice(i, i + window); //하위 배열 생성

  for(let w = 0; w < subarray.length; w++){
      sum += subarray[w];
      count += 1;
    }    
    answer.push(sum/count);
}
```

<br/>

---

<br/>

## **💻 팁 2: 한 번에 전체 배열을 조작하기**

하위 배열을 만들었기 때문에 일부 배열이 아닌 배열 전체를 반복할 수 있다.

```js
let answer = [];
let window = 5;

for(let i = 0 ; i < array.length ; i ++){
  let subarray = array.slice(i, i + window); //하위 배열 생성
  answer.push(average(subarray));
}

// 이전에 만든 함수들
function average(numbers){
  return reduce(numbers, 0, plus) / numbers.length
}

function plus(a,b){
  return a+b;
}
```

<br/>

---

<br/>

## **💻 팁 3: 작은 단계로 나누기**

배열의 항목이 아니라 인덱스를 가지고 반복해야하는 문제가 있다.

```js
let indices = [];

for(let i = 0 ; i < array.length ; i++){
  indices.push(i)
}
```

새로운 단계가 생겼다. 이제 인데스 배열에 map()을 적용해 반복문을 바꿔보자.


```js
let indices = [];

for(let i = 0 ; i < array.length ; i++){
  indices.push(i)
}

let window = 5;

let answer = map(indices, function(i){
  let subarray = array.slice(i, i + window); 
  return average(subarray)
})
```

map() 콜백 안에서 두 가지 일을 하고 있다. 하위 배열을 만들고 평균을 계산하는 일.

이 코드를 두 단계로 나눠보면 더 명확해질 것이다.

```js
let indices = [];

for(let i = 0 ; i < array.length ; i++){
  indices.push(i)
}

let window = 5;

let windows = map(indices, function(i){ // 1단계, 하위 배열만들기
  return  array.slice(i, i + window); 
})

let answer = map(window, average) // 2단계, 평균 계산
```

마지막으로 인덱스 배열을 만드는 코드를 빼내 유용한 함수로 정의 하는 것.

```js
function range(start, end){
  let ret = [];
  for(let i = start ; i < end ; i++){
    ret.push(i)
  }
  return ret;
}

let window = 5;

let indices = range(0, array.length); 
let windows = map(indices, function(i){ // 1단계, 하위 배열만들기
  return  array.slice(i, i + window); 
})
let answer = map(window, average) // 2단계, 평균 계산
```

<br/>

---

<br/>

## **💻 절차적 코드와 함수형 코드 비교**

처음에는 반복문이 중첩되고 인덱스를 계산하며 지역변수를 바꾸는 코드였다.

각 단계로 나눠 명확하게 만들었다.

완성된 코드는 글로도 그대로 바꿔 쓸 수 있다.

<br/>

---

<br/>

## **💻 체이닝 팁 요약**

### **🍳 데이터 만들기**

함수형 도구는 배열 전체를 다룰 때 잘 동작한다.

배열 일부에 대해 동작하는 반복문이 있다면 배열 일부를 새로운 배열로 나눌 수 있다. 그리고 함수형 도구를 사용하면 작업을 줄일 수 있다.

<br/>

### **🍳 배열 전체 다루기**

어떻게 하면 반복문을 대신해 전체 배열을 한 번에 처리할 수 있을지 생각해보자.

map()은 모든 항목을 변환하고 filter()는 항목을 없애거나 유지한다.

reduce()는 항목을 하나로 합친다. 

<br/>

### **🍳 작은 단계로 나누기**

한 번에 너무 많은 일을 한다고 생각된다면 직관에 반하지만 두 개 이상의 단계로 나눠보자.

단계를 더 만들면 이해하기 쉬워진다.

작은 단계는 더 단순하기 때문이다.

<br/>

---

<br/>

## **💻 체이닝 디버깅을 위한 팁**

고차 함수를 사용하는 것은 매우 추상적이기 때문에 문제가 생겼을 때 이해하기 어려울 수도 있다. 

### **🍳 구체적인 것을 유지하기**

각 단계에서 어떤 것을 하고 있는지 알기 쉽게 이름을 잘 지어야 한다.

의미를 기억하기 쉽게 이름을 붙이자.

<br/>

### **🍳 출력해보기**

중간에 어떤 데이터가 생기는지 잊어버리는 경우가 있다. 그런 경우 각 단계에 print 구문을 넣어보자. 

예상한 대로 동작하는지 확인할 수 있는 좋은 방법이다.

<br/>

### **🍳 타입을 따라가 보기**

함수형 도구는 정확한 타입이 있다.

자바스크립트처럼 타입이 없는 언어를 사용해도 함수형 도구는 타입이 있다.

다만 컴파일 타임에 타입을 검사하지 않을 뿐...

<br/>

---

<br/>

## **💻 요점 정리**

- 함수형 도구는 여러 단계의 체인으로 조합할 수 있다.
  - 함수형 도구를 체인으로 조합하면 복잡한 계산을 작고 명확한 단계로 표현할 수 있다.

- 함수형 도구를 체인으로 조합하는 것은 SQL 같은 쿼리 언어로 볼 수 있다.
  - 함수형 도구 체인으로 배열을 다루는 복잡한 쿼리를 표현할 수 있다.

- 종종 체인의 다음 단계를 위해 새로운 데이터를 만들거나 기존 데이터를 인자로 사용해야하는 일이 있다.
  - 최대한 암묵적인 정보를 명시적으로 표현하는 방법을 찾아야 한다.

- 함수형 도구는 더 많이 있다. 
  - 코드를 리팩터링 하면서 새로운 함수형 도구를 찾거나 다른 언어에서 영감을 받을 수 있다.

- 자바처럼 전통적으로 함수형 언어가 아닌 언어들도 나름의 방법으로 함수형 도구를 지원하고 있다. 
  - 언어에 맞는 방법을 찾아 함수형 도구를 사용하자.


<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


