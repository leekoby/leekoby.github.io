---
layout: post
title: 3. 액션과 계산, 데이터의 차이를 알기
author: admin
date: 2023-09-27 00:00:00 +900
lastmod: 2023-09-27 00:00:00 +900
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

# **🌈 3. 액션과 계산, 데이터의 차이를 알기**

이번 장에서 살펴볼 내용

- 액션과 계산, 데이터가 어떻게 다른지
- 문제에 대해 생각하거나 코드를 작성할 때 또는 코드를 읽을 때 액션과 계산, 데이터를 구분해서 적용
- 액션이 코드 전체로 퍼질 수 있다는 것을 이해 
- 이미 있는 코드에서 어떤 부분이 액션인지 찾기

## **💻 액션과 계산, 데이터**


|액션|계산|데이터|
|:-|:-|:-|
|실행 시점과 횟수에 의존|입력으로 출력 계산|이벤트에 대한 사실|
| - 부수 효과<br/> - 부수 효과가 있는 함수 <br/> - 순수하지 않은 함수|- 순수함수 <br/> - 수학함수||
|- 이메일 보내기 <br/> - 데이터베이스 읽기| - 최대값 찾기 <br/> - 이메일 주소가 유효한지 확인| - 입력한 이메일 주소 <br/> -  은행 API로 읽은 수량|


함수형 코딩에서는 액션과 계산, 데이터를 구분하는 일을 아주 중요하게 생각한다.

문제에 대해 생각할 때도, 코딩을 할 때도, 코드를 읽을 때도 모두 액션, 계산, 데이터로 나누어 생각할 수 있다.

<br/>

---

<br/>

## **💻 액션과 계산, 데이터는 어디에나 적용할 수 있습니다.**

우리가 일상에서 자주 하는 장보기를 액션, 계산, 데이터로 나눌 수 있다.

- 냉장고확인하기
- 운전해서 상점으로 가기
- 필요한 것 구입하기
- 운전해서 집으로 오기

다 액션으로 보이는데 위 액션들을 아주 작게작게 쪼개어 보면 장보기가 액션으로만 이루어져 있지 않다는 것을 알 수 있다.

### **🍳 냉장고 확인하기**

- 냉장고를 확인하는 일은 확인하는 시점이 중요하기 때문에 `액션`이다. 

- 냉장고에 있는 물건들은 `데이터`라고 할 수 있으며 현재 재고라고 가정해보자.

<br/>

### **🍳 운전해서 상점으로 가기**

- 운전해서 상점으로 가는 것은 복잡한 행동이고 명확히 `액션`

- `데이터`

  - 상점 위치

  - 가는 경로

<br/>

### **🍳 필요한 것 구입하기**

- 구입하는 일은 `액션`이지만, 구입 과정을 작게 작게 쪼개어 볼 수 있다.

- 필요한 것 구입하기 👉 `액션`
  
- 필요하지만 없는 제품의 목록 만들기
  - 현재 재고 👉 `데이터`
  - 필요한 재고 👉 `데이터`
  - 현재 재고에서 필요한 재고 빼기 👉 `계산`
  - 장보기 목록 👉 `데이터`

<br/>

### **🍳 장보기 과정**


- 냉장고 확인 👉 `액션`

- 현재 재고 👉 `데이터`

- 필요한 재고 👉 `데이터`

- 현재 재고에서 필요한 재고 빼기 👉 `계산`

- 계산을 통해 완성된 장보기 목록 👉 `데이터`

- 운전해서 상점으로 가기 👉 `액션`

- 목록에 있는 것 구입하기 👉 `액션`

- 운전해서 집으로 오기 👉 `액션`

이렇게 반복하면 액션, 계산, 데이터를 더 많이 찾을 수 있고 풍부한 모델을 만들 수 있다.

처음에 생각했던 장보기를 위한 행동들에 더 많은 행동들이 추가된 것을 알 수 있다. 

점점 많아지면 복잡하다는 생각이 들 수 있겠지만, 액션에 숨어있는 다른 액션이나 계산 또는 데이터를 발견하기 위해 나눌 수 있는 만큼 나누는 것이 좋다.

<br/>

---

<br/>

## **💻 장보기 과정에서 배운것**


- 액션과 계산, 데이터는 어디에나 적용할 수 있다. 

- 액션 안에는 계산과 데이터, 또 다른 액션이 숨어 있을지도 모른다.

- 계산은 더 작은 계산과 데이터로 나누고 연결할 수 있다. 

- 데이터는 데이터만 조합할 수 있다. 

- 계산은 때로 '우리 머리속에서' 일어난다.

<br/>

---

<br/>

## **💻 새로 만드는 코드에 함수형 사고 적용하기**

쿠폰독은 쿠폰에 관심 있는 구독자들에게 이메일로 쿠폰을 매주 보내주는 서비스이다. 

쿠폰독에서는 사용자를 더 늘리기 위해 친구 10명을 추천하면 더 좋은 쿠폰을 보내주려고 한다. 

10명 이상 추천하면 Best 쿠폰을, 10명 이하는 Good 쿠폰을 보내줄 것이다.

이러한 서비스를 만들기 위해 코드를 작성한다고 했을 때 작성해야 하는 코드를 액션, 계산, 데이터로 분류해보자.


- 이메일 보내기 👉 `액션`

- 데이터 베이스에서 구독자 가져오기 👉 `액션`

- 쿠폰에 등급 매기기 👉 `계산`

- 데이터베이스에서 쿠폰 읽기 👉 `액션`

- 이메일 제목 👉 `데이터`

- 이메일 주소 👉 `데이터`

- 추천 수 👉 `데이터`

- 어떤 이메일이 쿠폰을 받을지 결정하기 👉 `계산`

- 구독자 DB 레코드 👉 `데이터`

- 쿠폰 DB 레코드 👉 `데이터`

- 구독자 목록 DB 레코드 👉 `데이터`

- 쿠폰 목록 DB 레코드 👉 `데이터`

- 이메일 본문 👉 `데이터`

<br/>

---

<br/>

## **💻 쿠폰 보내는 과정 그려보기**

### **🍳 데이터 베이스에서 구독자를 가져오기**

쿠폰을 이메일로 보내려면 데이터베이스에서 구독자를 가져와야 한다.

- DB에서 구독자 가져오기 👉 실행 시점에 의존하기 때문에 `액션`

- 구독자 목록 👉 `데이터`

<br/>

### **🍳 데이터 베이스에서 쿠폰 목록 가져오기**

- DB에서 쿠폰 목록 가져오기 👉 가져오는 시점이 중요하기 때문에 `액션`

- 쿠폰 목록 👉 `데이터`

<br/>

### **🍳 보내야 할 이메일 목록 만들기**


- 구독자 목록과 쿠폰 목록을 가져와 이메일 목록 계획하기 👉 `계산`

- 계획한 이메일 목록 👉 `데이터`

구독자 목록과 쿠폰 목록을 가져와 이메일 목록 계획하기 라는 계산을 조금 더 작은 계산으로 나눌 수도 있다.

- 쿠폰 목록 👉 `데이터`

  - good 쿠폰 선택하기 👉 `계산`

    - good 쿠폰 목록 👉 `데이터`

  - best 쿠폰 선택하기 👉 `계산`

    - best 쿠폰 목록 👉 `데이터`

어떤 구독자가 good 쿠폰을 받을지, best 쿠폰을 받을지 결정하는 것을 계산으로 만들 수 있다.

- 구독자 목록 👉 `데이터`

  - 쿠폰 등급 결정하기 👉 `계산`

  - 쿠폰 등급 👉 `데이터`

- 구독자 목록, good 쿠폰 목록, best 쿠폰 목록, 쿠폰 등급 데이터를 받아 쿠폰 등급이 "good"이면 good 이메일 만들기, "best"이면 best 이메일 만들기

이보다 더 작게 작게 나눌 수도 있고 더 작게 나눌 수록 구현하기 쉽다. 

하지만 충분히 구현하기 쉽다고 생각이 되면 이제 나누는 것을 멈추어야 한다.

<br/>

### **🍳 이메일 전송하기**

이메일 목록을 순회하면서 이메일 보내기 👉 `액션`

<br/>

### **🍳 계산을 쓰면서 걱정하지 않아도 되는 것**

함수형 프로그래밍에서는 액션보다 게산을 사용하려고 한다.

- 동시에 실행되는 것

- 과거에 실행되었던 것이나 미래에 실행할 것

- 실행 횟수


<br/>

---

<br/>

## **💻 쿠폰 보내는 과정 구현하기**

### **🍳 데이터베이스에서 가져온 구독자 데이터**

```js
const subscriber = {
  email : "leekoby@naver.com",
  rec_count : 16
};
```

<br/>

### **🍳 쿠폰 등급**

```js
const rank1 = "best";
const rank2 = "good";
```

<br/>

### **🍳 쿠폰 등급을 결정하는 것은 함수**

계산은 함수로 구현 

입력값은 함수 인자, 출력값은 함수의 리턴값

```js
function subCouponRank(subscriber){ //입력
  if(subscriber.rec_count >= 10){ // 계산
    return "best";
  } else {
    return "good" // 출력
  }
}
```

<br/>

### **🍳 특정 등급의 쿠폰 목록 선택하는 계산은 함수**

입력값은 전체 쿠폰 목록과 선택할 등급, 출력값은 선택한 등급을 가진 쿠폰 목록

```js
function selectCouponsByRank ( coupons, rank){ //입력
  let ret = {}; // 빈 배열로 초기화
  for (let c = 0 ; c < coupon.length ; c ++){ // 모든 쿠폰에 대해 반복
    let coupon = coupons[c];
    if(coupon.rank === rank){
      ret.push(coupon.code) 
      // 현재 쿠폰이 주어진 등급이랑 일치하면 쿠폰 코드를 배열에 삽입
    } 
  }
  return ret;
}
```

같은 입력값을 넣었을 때 같은 값이 나온다.

아무리 호출해도 외부에 영향을 주지 않는다.

<br/>

### **🍳 이메일은 그냥 데이터**

```js
const message = {
  from : "leekoby@gmail.com",
  to : "leekoby@naver.com",
  subject : "이번주 할인 쿠폰",
  body : "이번주 할인 쿠폰이 여기 있습니다. ...",
}
```

<br/>

### **🍳 구독자가 받을 이메일을 계획하는 계산**

```js
function emailForSubscriber(subscriber, goods, bests) {
    const rank = subCouponRank(subscriber)
    if (rank === "best") {
        return {
            from: "보내는 사람",
            to: subscriber.email,
            subject: "제목",
            body: `${최고등급의 쿠폰을 보내드립니다}: ${bests.join(", ")}`
        }
    } else {
        return {
            from: "보내는 사람",
            to: subscriber.email,
            subject: "제목",
            body: `${상급 쿠폰을 보내드립니다}: ${goods.join(", ")}`
        }
    }
}
```

<br/>

### **🍳 보낼 이메일 목록 준비**

```js
function emailsForSubscribers(subscribers, goods, bests) {
    const emails = []
    for (const subscriber of subscribers) {
        const email = emailForSubscriber(subscriber, goods, bests)
        emails.push(email)
    }
    
    return emails
}
```

<br/>

### **🍳 이메일 보내기는 액션**

```js
function sendIssue() {
    const coupons = fetchCouponsFromDB()
    const goodCoupons = selectCouponsByRank(coupons, "good")
    const bestCoupons = selectCouponsByRank(coupons, "best")
    const subscribers = fetchSubscribersFromDB()
    const emails = emailsForSubscribers(subsribers, goodCoupons, bestCoupons)
    
    for(const email of emails) {
        emailSystem.send(email)
    }
}
```

모든 것을 코드로 구현했을 때 데이터를 파악하는 것부터 계산과정과 추가적으로 들어가는 데이터를 도출하였고 액션단위로 모든 것을 묶어두었다. 

데이터는 사용 상의 제약이 많고 액션은 제약이 없기 때문에 데이터를 먼저 구현하고 계산을 구현한 후에 액션을 구현하는 것이 함수형 프로그래밍의 가장 일반적인 구현 순서이다.

<br/>

---

<br/>

## **💻 이미 있는 코드에 함수형 사고 적용**

제나가 자회사에 수수료를 보내기 위해 만든 코드이다. sendPayout() 함수는 실제 은행 계좌로 송금하는 액션이다.

```js
function figurePayout(affiliate) {
  let owed = affiliate.sales * affiliate.commission;
  if(owed > 100)
    sendPayout(affiliate.bank_code, owed); // 이 코드만 액션일까요?
}

function affiliatePayout(affiliates) {
  for(var a = 0; a < affiliates.length; a++)
    figurePayout(affiliate[a]);
}

function main(affiliates) {
  affiliatePayout(affiliates);
}
```

그런데 제나가 작성한 코드가 함수형 코드일까? 액션 코드는 하나만 있는 걸까?

제나가 말한 액션 코드 한 줄부터 시작해보면 실제 돈을 송금하는 코드이다. 

호출 시점이나 횟수가 중요하기 때문에 `액션`이라고 할 수 있다.

그런데 액션의 정의에 따르면 액션은 호출 시점이나 횟수에 의존한다. 

`figurePayout()` 함수는 액션인 `sendPayout()` 함수를 호출하기 때문에 이 함수 역시 호출 시점과 횟수에 의존하게 된다. 그래서 `figurePayout()` 도 `액션`이 된다.

또 `affiliatePayout()` 함수도 액션인 `figurePayout()` 함수를 호출하기 때문에 `affiliatePayout()` 함수도 `액션`이 된다.

같은 논리로 액션인 `affiliatePayout()` 함수를 호출하는 `main()` 함수도 `액션`이 된다.

그래서 제나가 작성한 코드는 `모두 액션`이다.

액션을 부르는 함수가 있다면 그 함수도 액션이 된다. 

이런 식으로 작은 액션 하나가 코드 전체로 퍼져 나가게 된다.

<br/>

---

<br/>

## **💻 액션의 다양한 형태로 나타난다.**

- 함수 호출
  ```js
  alert("Hello world");
  ```
  팝업 창이 뜨는 것은 액션이다.

- 메서드 호출
  ```js
  console.log("hello");
  ```
  콘솔에 출력한다.

- 생성자
  ```js
  new Date();
  ```
  부르는 시점에 따라 다른 값을 가지기 때문에 액션이다.

- 표현식 - 변수 참조
  ```js
  y
  ```
  y가 공유되고 변경 가능한 변수라면 읽는 시점에 따라 값이 다를 수 있다.

- 표현식 - 속성 참조
  ```js
  user.first_name
  ```
  user가 공유되고 변경 가능한 객체라면 읽는 시점에 따라 값이 다를 수 있다.

- 표현식 - 배열 참조
  ```js
  stack[0]
  ```
  stack이 공유되고 변경 가능한 배열이라면 첫번째 항목은 읽는 시점에 따라 값이 다를 수 있다.

- 상태 - 값 할당
  ```js
  z = 3;
  ```
  공유하기 위해 값을 할당했고 변경 가능한 변수라면 다른 코드에 영향을 주기 때문에 액션이다.

- 상태 - 속성 삭제
  ```js
  delete user.first_name;
  ```
  속성을 지우는 것은 다른 코드에 영향을 주기 때문에 액션이다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}

