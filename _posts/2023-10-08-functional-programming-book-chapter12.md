---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 12. 
author: admin
date: 2023-10-08 15:00:00 +900
lastmod: 2023-10-08 15:00:00 +900
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

# **🌈 12. 함수형 반복**

이번 장에서 살펴볼 내용

- 함수형 도구 `map()`, `filter()`, `reduce()`에 대해 배운다.
- 배열에 대한 반복문을 함수형 도구로 바꾸는 방법
- 함수형 도구를 어떻게 구현하는지

## **💻 코드 냄새 하나와 리팩터링 두 개**

리팩터링으로 코드에 중복을 없애고 더 좋은 추상화를 만들었었다.

그 과정에서 일급 값과 고차 함수를 만들었다.

다시 정리해보면

### **🍳 코드의 냄새: 함수 이름에 있는 암묵적 인자.**

함수 본문에서 사용하는 어떤 값이 함수 이름에 나타난다면 `함수 이름에 있는 암묵적 인자`는 코드 냄새다.

특징

1. 거의 똑같이 구현된 함수가 있다.

2. 함수 이름이 구현에 있는 다른 부분을 가르킨다.

<br/>

### **🍳 리팩터링: 암묵적 인자를 드러내기**

함수 이름에 있는 암묵적 인자를 명시적인 함수 인자로 바꾸려면?

➡ 암묵적 인자 드러내기 리팩터링 

➡ 암묵적 인자가 일급 값이 되도록 함수에 인자를 추가 

단계 

1. 함수 이름에 있는 암묵적 인자를 확인

2. 명시적인 인자를 추가

3. 함수 본문에 하드 코등된 값을 새로운 인자로 바꾼다.

4. 함수를 호출하는 곳을 고친다.

<br/>

### **🍳 리팩터링: 함수 본문을 콜백으로 바꾸기**

함수 본문을 콜백으로 바꾸기 리팩터링으로 함수 본문에 어떤 부분(비슷한 함수에 있는 서로 다른 부분)을 콜백으로 바꾼다.

단계

1. 본문에서 바꿀 부분의 앞부분과 뒷부분을 확인

2. 리팩터링 할 코드를 함수로 빼낸다.

3. 빼낸 함수의 인자로 넘길 부분을 또 다른 함수로 빼낸다.

<br/>

---

<br/>

## **💻 기존 코드 forEach()로 바꿔보기**

🔻 3장에서 나왔던 코드

```js
function emailsForCustomers(customers, goods, bests){
  let emails = [];
  for(let i = 0; i < costomers.length ; i ++){
    let customer =customers[i];
    let email = emailForCustomer(customer, goods, bests)
    emails.push(email);
  }
  return email;
}
```

🔻 forEach()로 변경

```js
function emailsForCustomers(customers, goods, bests){
  let emails = [];
  forEach(customers, function(customer){
    let email = emailForCustomer(customer, goods, bests)
    emails.push(email);
  });
  return emails;
}
```

<br/>

---

<br/>

## **💻 예제를 통해 map()함수 도출하기**

🔻 팀에 할당된 코드 중 비슷한 코드들 

```js
function emailsForCustomers(customers, goods, bests){
  let emails = []; 
  forEach(customers, function(customer){//앞부분
    let email = emailForCustomer(customer, goods, bests) //본문
    emails.push(email);//뒷부분
  });
  return emails;
}

function biggestPurchasePerCustomer(customers){
  let purchases = [];
  forEach(customers, function(costomer){//앞부분
    let purchase = biggestPurchase(customer) //본문
    purchase.push(purchase);//뒷부분
  });
  return purchases;
}

function customerFullNames(customers){
  let fullNames = [];
  forEach(customers, function(customer){//앞부분
    let name = customer.firstName + ' ' + customer.lastName;//본문
    fullNames.push(name);//뒷부분
  });
  return fullNames
}

function customerCities(customers){
  let cities = [];
  forEach(customers, function(customer){//앞부분
    let city = customer.address.city;//본문
    cities.push(city);//뒷부분
  });
  return cities;
}
```

결과 배열에 넣을 값을 만드는 부분만 다르다.

함수 본문을 콜백으로 바꾸기 리팩터링으로 일반화할 수 있다.

🔻 콜백으로 바꾼 버전

```js
function emailsForCustomers(customers, goods, bests){
  return map(customers, function(customer){ // 본문을 콜백으로 전달
    return emailForCustomer(customer, goods, bests)
  })
}

function map(array, f){//콜백인자
  let newArray = [];
  forEach(array, function(element){
    newArray.push(f(element)); //여기서 콜백을 호출
  });
  return newArray 
}
```

<br/>

---

<br/>

## **💻 함수형 도구: map()**

```js
          // 배열과, 함수를 인자로 받는다.
function map(array, f){
  let newArray = []; // 빈 배열을 만든다.
  forEach(array, function(element){
    // 원래 배열 항목으로 새로운 항목을 만들기 위해 f()함수를 호출
    newArray.push(f(element)); 
    // 원래 배열 항목에 해당하는 새로운 항목을 추가
  });
  return newArray;
  // 새로운 배열을 리턴
}
```

`map()`은 `X`(어떤 값으 집합) 값이 있는 배열을 `Y`(또 다른 값의 집합) 값이 있는 배열로 변환한다고 볼 수 있다. 

`map()`은 값 하나를 바꾸는 함수를 배열 전체를 바꾸는 데 사용할 수 있다.

```js
function emailsForCustomers(customers, goods, bests){
            // 고객 배열을 map()에 전달
  return map(customers, function(customer){ 
                        //  고객을 받아 이메일을 리턴하는 함수를 map()에 전달
    return emailForCustomer(customer, goods, bests)
    // 고객에서 이메일을 계산한 결과를 리턴
  })
}
```

<br/>

---

<br/>

## **💻 함수를 전달하는 세 가지 방법**

### **🍳 전역으로 정의하기**

함수를 전역적으로 정의하고 이름 붙일 수 있다.

가장 많이 쓰는 방법

```js
function greet(name){ // 프로그램 한 곳에 이름을 붙여 함수를 정의
  return "Hello, " + name;
}

let friendGreetings = map(friendsNames, greet)
// 어디서나 이름으로 함수를 참조할 수 있다.
// 이렇게 map()에 전달할 수 있다.
```

<br/>

### **🍳 지역적으로 정의하기**

지역 범위 안에서 정의하고 이름을 붙일 수 있다.

이름을 가지고 있지만, 범위 밖에서는 사용할 수 없다.

지역적으로 쓰고 싶지만 이름이 필요할 때 유용

```js
function greetEverybody(friends){// 이 함수 범위 안에 있다.
  let greeting;
  if(language === 'english'){
    greeting = 'Hello, ';
  } else {
    greeting = 'Salut, ';
  }

  // 이 함수 안에서 이름을 붙여 함수를 정의
  let greet = function(name){
    return greeting + name;
  };
  // 같은 범위에 있다면 이름으로 함수를 참조할 수 있다.
  return map(friends, greet)
}
```

<br/>

### **🍳 인라인으로 정의하기**

함수를 사용하는 곳에서 바로 정의할 수 있다. 

함수를 변수 같은 곳에 넣지 않기 때문에 이름이 없다.

`익명함수`라고 부른다. 문맥에서 한 번만 쓰는 짧은 함수에 쓰면 좋다.

```js
let friendGreetings = map(friendsNames, function (name){
                                    // 함수를 사용하는 곳에서 함수를 정의
  return 'Hello, ' + name;
}); 
```

<br/>

---

<br/>

## **💻 예제: 모든 고객의 이메일 주소**

모든 고객의 이메일을 만들어야 한다고 가정해보자.

고객 데이터는 이미 배열로 가지고 있다.

- 가진 것 : 고객 배열

- 필요한 것 : 고객 이메일 주소 배열

- 함수 : 고객 하나를 받아 고객 이메일 주소를 리턴하는 함수 

```js
// 가지고 있는 고객 배열 전체에 map() 사용
map(customers, function(customer){ /// 고객의 이메일 주소를 만드는 함수를 넘김
  return customer.email; 
}) // 모든 고객의 이메일을 배열로 리턴
```

**🚨 주의**

map은 매우 유용한 함수지만 항상 조심해서 사용해야 한다.

리턴값인 배열에 들어 있는 항목을 확인하지 않기 때문.

위의 상황에서 고객 데이터에 이메일이 없어서 customer.email 값이 `null`이나 `undefined`라면 결과 배열안에 `null`이 들어갈 것이다.

배열 전체에 함수를 적용하기 때문에 문제가 더 커질 수 있다.

<br/>

---

<br/>

## **💻 예제를 통해 filter()함수 도출하기**

🔻 팀에 할당된 코드 중 비슷한 코드들 

```js
function selectBestCustomers(customers){
  let newArray = []; 
  forEach(customers, function(customer){//앞부분
  if(customer.purchases.length >= 3) //본문
    newArray.push(customer);//뒷부분
  });
  return newArray;
}

function selectCustomerAfter(customers, date){
  let newArray = []; 
  forEach(customers, function(customer){//앞부분
  if(customer.signupDate > date) //본문
    newArray.push(customer);//뒷부분
  });
  return newArray;
}

function selectCustomerBefore(customers, date){
  let newArray = []; 
  forEach(customers, function(customer){//앞부분
  if(customer.signupDate < date) //본문
    newArray.push(customer);//뒷부분
  });
  return newArray;
}

function singlePurchaseCustomers(customers){
  let newArray = []; 
  forEach(customers, function(customer){//앞부분
  if(customer.purchases.length === 1) //본문
    newArray.push(customer);//뒷부분
  });
  return newArray;
}
```

🔻 콜백 함수로 바꾼 버전 

```js
function selectBestCustomers(customers){
  return filter(customers, funtion(customer){
    return customer.purchase.length >= 3;
    // 표현식을 함수로 빼서 인자로 전달
  })
} 

function filter(array, f){
  let newArray = []; 
  forEach(array, function(element){
  if(f(element)) // 조건식으로 콜백을 호출
    newArray.push(element);
  });
  return newArray;
}
```

<br/>

---

<br/>

## **💻 함수형 도구 : filter()**

```js     
                // 배열과 함수를 받음
function filter(array, f){
  let newArray = []; // 빈 배열 생성
  forEach(array, function(element){
  if(f(element)) // f()를 호출해 항목을 결과 배열에 넣을지 확인
    newArray.push(element);// 조건에 맞으면 원래 항목을 결과 배열에 넣음
  });
  return newArray; // 결과 배열 리턴
}
```

filter()는 배열에서 일부 항목을 선택하는 함수로 볼 수 있다.

항목이 `x`인 배열에 filter()를 사용해도 결과는 여전히 항목이 `x`인 배열

<br/>

---

<br/>

## **💻 예제: 아무것도 구입하지 않은 고객**

모든 고객 중에 아무것도 구입하지 않은 고객을 배열로 만들려고 한다.

- 가진 것 : 고객 배열

- 필요한 것 : 아무것도 구입하지 않은 고객 배열

- 함수 : 고객 하나를 받아 아무것도 구입하지 않았다면 `true` 리턴

```js
      // 고객 배열        // 아무것도 구입하지 않은 고객을 결정하는 함수 전달
filter(customers, function(customer){
  return customer.purchases.length === 0; // filter()는 true를 리턴
});
```

**🚨 주의**

map()을 사용한 경우 결과 배열에 `null`이 있을 수도 있었다. 

filter() 함수를 사용하면 `null`을 쉽게 없앨 수 있다.

```js
let allEmails = map(customers, function(customer){
  return customer.email; // 고객 이메일이 null 이면 배열에 null이 들어감
})

let emailsWithoutNulls = filter(emailsWithNulls, function(email){
  return email !== null; // 올바른 이메일만 남겨두기 위해 null을 없앨 수 있다.
})
```

<br/>

---

<br/>

## **💻 예제를 통해 reduce() 도출하기**

🔻 팀에 할당된 코드 중 비슷한 코드들 

```js
function countAllPurchases(customers){
  let total = 0;
  forEach(customers, function(customer){//앞부분
    total = total + customer.purchases.length; 
    //본문(합치는 동작)
  }); 
  return total; //뒷부분
}

function concatenateArrays(arrays){
  let result = [];
  forEach(arrays, function(array){//앞부분
    result = result.concat(array)
    //본문(합치는 동작)
  }); 
  return result; //뒷부분
}

function customerPerCity(customers){
  let cities = {};
  forEach(customers, function(customer){//앞부분
    cities[customer.address.city] += 1;
    //본문(합치는 동작)
  }); 
  return cities; //뒷부분
}

function biggestPurchase(purchases){
  let biggest = {total : 0};
  forEach(purchases, function(purchase){//앞부분
    biggest = biggest.total > purchase.total? biggest : purchase;
    //본문(합치는 동작)
  }); 
  return total; //뒷부분
}
```

🔻 콜백으로 바꾼 버전

```js
function countAllPurchases(customers){
 return reduce(
            // 초기값 0 
  customers, 0, function (total, customer){
    return total + customer.purchase.length //콜백 함수
  }
 )
}

function reduce(array, init, f){
   let accum = init;
  forEach(array, function(element){//앞부분
    accum = f(accum,element)
    //본문(합치는 동작)
  }); 
  return accum; //뒷부분
}
```

<br/>

---

<br/>

## **💻 함수형 도구: reduce()**

```js
                // 배열과 초깃값, 누적 함수 전달
function reduce(array, init, f){
   let accum = init;// 누적된 값 초기화
  forEach(array, function(element){
    accum = f(accum,element) 
    // 누적 값을 계산하기 위해 현재 값과 배열 항목으로 f() 함수 호출
  }); 
  return accum; 
  // 누적된 값 리턴
}
```

reduce()는 배열을 순회하면서 값을 누적

값을 누적하는 것은 추상적인 개념

실제로는 여러 가지 형태가 될 수 있다. 

예를 들면 값을 더할 수도 있고, 해시 맵이나 문자열을 합치는 것이 될 수도 있다.

```js
function countAllPurchases(customers){
 return reduce(
 // 고객 배열 전달, 초기값 0 전달 
  customers, 0, function (total, customer){ 
                // reduce()에 전달하는 함수는 인자가 두 개 여야하고 
                // 리턴 값을 첫 번째 인자와 타입이 같아야 한다.
    return total + customer.purchase.length 
    // 지금까지 누적한 합계와 현재 고객이 구입한 제품의 개수를 더한 값을 리턴
  }
 )
}
```

<br/>

---

<br/>

## **💻 예제: 문자열 합치기**

어떤 문자열 배열이 있고 이 배열에 모든 문자열을 하나로 합치려고 한다.

- 가진 것 : 문자열 배열 

- 필요한 것 : 배열에 있는 모든 문자열을 하나로 합친 문자열

- 함수 : 누적된 문자열과 배열에 있는 현재 문자열을 받아서 함치는 함수 

```js
    // 문자열 배열을 합쳐서 누적 "" 초기값은 빈문자열
reduce(string, "", function (accum, string){ //콜백함수 전달
  return accum + string;
})
```

**🚨 주의**

reduce() 함수를 사용할 때 두 가지를 조심해야 한다.

1) 인자 순서 

2) 초깃값 결정하는 방법
  - 계산이 어떤 값에서 시작되는가?
  - 배열이 비어 있다면 어떤 값을 리턴할 것인가?

<br/>

---

<br/>

## **💻 reduce로 할 수 있는 것들**

reduce는 매우 강력하다. 

reduce()로 filter()나 map()도 만들 수 있다. 하지만 그 반대는 불가능

### **🍳 실행 취소 / 실행 복귀**

실행 취소와 복귀는 제대로 동작하게 만들기가 매우 어렵다.

리스트 형태의 사용자 입력에 reduce()를 적용한 것이 현재 상태라고 생각해보면, 실행 취소는 리스트 의 마지막 사용자 입력을 없애는 것이라고 할 수 있다.

<br/>

### **🍳 테스트할 때 사용자 입력을 다시 실행하기**

시스템의 처음 상태가 초깃값이고 사용자 입력이 순서대로 리스트에 있을 때 reduce()로 모든 값을 합쳐 현재 상태를 만들 수 있다.

<br/>

### **🍳 시간 여행 디버깅**

어떤 언어는 변경 사항을 어떤 시점으로 되돌릴 수 있다.

뭔가 잘못 동작하는 경우 특정 시점 상태의 값을 보관할 수 있다.

그리고 문제를 해결하고 새로운 코드로 다시 실행해볼 수 있다.

reduce()를 통해서 가능하다.

이런 느낌인 것으로 생각된다...

예를 들어, 어떤 프로그램이 작동 중인데, 특정 시점에서 버그가 발생한다. 이 때 reduce()를 사용하여 해당 시점의 상태 값을 보관하고 문제를 해결한 후에 다시 실행

```js
// 가정: 배열의 요소들을 제곱하는 코드에서 버그 발생

const numbers = [1, 2, 3, 4, 5];
let squaredNumbers = [];

for (let i = 0; i < numbers.length; i++) {
  // 버그: 제곱 연산이 아닌 덧셈 연산
  squaredNumbers.push(numbers[i] + numbers[i]);
  
  // 디버깅용 로그
  console.log(`현재 인덱스 ${i}, 현재 배열 ${squaredNumbers}`);
}

console.log(squaredNumbers); // 출력: 잘못된 결과

// 문제 해결을 위해 코드 수정
squaredNumbers = numbers.reduce((accumulator, currentValue) => {
    accumulator.push(currentValue * currentValue);
    return accumulator;
}, []);

console.log(squaredNumbers); // 출력: 올바른 결과

```

<br/>

### **🍳 회계 감사 추적**

특정 시점에 시스템 상태를 알고 싶은 경우 

reduce()로 과거에 어떤 일이 있었는지 기록할 수 있다. 

어떤 일이 있었는지 뿐만 아니라 어떤 과정을 통해 일이 생겼는지도 알 수 있다.

이런 느낌으로 생각된다...

예를 들어 어떤 회계 시스템이 있다고 가정해보면, 이 시스템은 거래 내역들을 관리하고 특정 시점에 어떤 일이 있었는지 추적할 필요가 있다. reduce()를 사용하여 과거에 어떤 일이 있었는지 기록

```js
const transactions = [
    { id: '001', type: '입금', amount: 100 },
    { id: '002', type: '출금', amount: -50 },
];

const auditTrail = transactions.reduce((trail, transaction) => {
    trail.push({
        id: transaction.id,
        type: transaction.type,
        amount: transaction.amount,
        timestamp: new Date() // 날짜를 추가하거나 조건을 추가해서 찾는거 같다.
    });
    return trail;
}, []);

console.log(auditTrail);

```

<br/>

---

<br/>

## **💻 요점 정리**

- map(), filter(), reduce()에 대해 알아봤다.

- map(), filter(), reduce()는 특별한 방법으로 배열을 반복할 수 있다.
  - 반복문을 대체해서 코드의 목적을 더 명확하게 할 수 있다.

- map()은 어떤 배열의 모든 항목에 함수를 적용해 새로운 배열로 바꾼다.
  - 각 항목은 지정한 콜백 함수에 의해 변환된다.

- filter()는 어떤 배열의 하위 집합을 선택해 새로운 배열로 만든다.
  - 술어를 전달해서 특정 항목을 선택할 수 있다.

- reduce()는 초깃값을 가지고 어떤 배열의 항목을 조합해 하나의 값을 만든다.
  - 데이터를 요약하거나 시퀀스를 하나의 값으로 만들 때 주로 쓴다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


