---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 11.
author: admin
date: 2023-10-08 12:00:00 +900
lastmod: 2023-10-08 12:00:00 +900
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

# **🌈 11. 일급 함수 2**

이번 장에서 살펴볼 내용

- 함수 본문을 콜백으로 바꾸기 리팩터링 심화

- 함수를 리턴하는 함수가 가진 강력한 힘을 이해하기

- 고차 함수에 익숙해지기 위해 여러 고차 함수를 만들어보기

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

## **💻 카피-온-라이트 리팩터링하기**

6장에서 살펴본 카피-온-라이트 패턴에 중복이 많아보인다. 

함수 본문을 콜백으로 바꾸기 리팩터링이 해결해줄 수 있을 것 같다.

### **🍳 리팩터링: 함수 본문을 콜백으로 바꾸기**

1. 본문과 본문의 앞부분과 뒷부분 확인

2. 함수 빼내기

3. 콜백 빼내기

### **🍳 카피-온-라이트 단계**

1. 복사본을 만든다. (앞부분)

2. 복사본을 변경한다. (본문)

3. 복사본을 리턴한다. (뒷부분)

<br/>

---

<br/>

## **💻 배열에 대한 카피-온-라이트 리팩터링**

### **🍳 1. 본문과 앞부분, 뒷부분을 확인하기**

```js
function arraySet(array, idx, value) {
  let copy = array.slice(); // 앞부분: 복사본을 만든다.
  copy[idx] = value; // 본문: 복사본을 변경한다.
  return copy; // 뒷부분: 복사본을 리턴한다.
}

// 비슷한 함수
function push(array, elem) {
  let copy = array.slice(); // 앞부분: 복사본을 만든다.
  copy.push(elem); // 본문: 복사본을 변경한다.
  return copy; // 뒷부분: 복사본을 리턴한다.
}

// 비슷한 함수
function drop_last(array) {
  let array_copy = array.slice(); // 앞부분: 복사본을 만든다.
  array_copy.pop(); // 본문: 복사본을 변경한다.
  return array_copy; // 뒷부분: 복사본을 리턴한다.
}

// 비슷한 함수
function drop_first(array) {
  let array_copy = array.slice(); // 앞부분: 복사본을 만든다.
  array_copy.shift(); // 본문: 복사본을 변경한다.
  return array_copy; // 뒷부분: 복사본을 리턴한다.
}
```

➡️ 복사하고 리턴하는 것은 같으므로 `arraySet()` 함수로 리팩터링 해보자.

<br/>

### **🍳 2. 함수 빼내기**

```js
// 원래 코드
function arraySet(array, idx, value) {
  let copy = array.slice(); // 함수로 빼낸다.
  copy[idx] = value;
  return copy;
}

// 함수로 빼낸 코드
function arraySet(array, idx, value) {
  return withArrayCopy(array);
}

function withArrayCopy(array) {
  let copy = array.slice();
  copy[idx] = value; // 아직 정의되지 않은 부분
  return copy;
}
```

아직 idx와 value가 정의되지 않았으므로 코드의 동작이 이뤄지지 않는다.

<br/>

### **🍳 3. 콜백 빼내기**

콜백은 배열을 변경하는 일을 하므로 `modify` 라고 해본다.

🔻 원래 코드

```js
function arraySet(array, idx, value) {
  return withArrayCopy(array);
}

function withArrayCopy(array) {
  let copy = array.slice();
  copy[idx] = value; // 본문을 인자로 만들어 전달한다.
  return copy;
}
```

🔻 콜백으로 빼낸 코드

```js
function arraySet(array, idx, value) {
  return withArrayCopy(array, function(copy) {
    copy[idx] = value;
  });
}

function withArrayCopy(array, modify) {
  let copy = array.slice();
  modify(copy)
  return copy;
} // 카피-온-라이트 원칙을 따르고 재사용할 수 있는 함수
```

<br/>

### **🍳 리팩터링으로 얻은 것**

1. 표준화된 원칙

2. 새로운 동작에 원칙을 적용할 수 있음

3. 여러 개를 변경할 때 최적화

<br/>

### **🍳 다른 함수에도 적용해보기**

```js
function push(array, elem) {
  return withArrayCopy(array, function(copy) {
    copy.push(elem);
  });  
}

function drop_last(array) {
  return withArrayCopy(array, function(copy) {
    copy.pop();
  });  
}

function drop_first(array) {
  return withArrayCopy(array, function(copy) {
    copy.shift();
  });  
}
```

<br/>

---

<br/>

## **💻 함수를 리턴하는 함수**

### **🍳 예시 상황**

```js
try {
  saveUserData(user); 
  // 이 한 줄만 다르고 나머지는 모든 코드에서 중복이 된다.
} catch (error) {
  logSnapErrors(error);
}

try {
  fetchProduct(productId); 
  // 이 한 줄만 다르고 나머지는 모든 코드에서 중복이 된다.
} catch (error) {
  logSnapErrors(error);
}
```

<br/>

### **🍳 해결 방법 1: 반복되는 코드 캡슐화하기**

```js
function withLogging(f) { // 반복되는 코드를 캡슐화한다.
  try {
    f();
  } catch (error) {
    logSnapErrors(error);
  }
}

withLogging(function() {
  saveUserData(user);
});

withLogging(function() {
  fetchProduct(productId);
});
```

로그를 남기기 위한 일반적인 시스템이 생겼으나, 여전히 두 가지 문제가 있다.

1. 어떤 부분에 로그를 남기는 것을 깜빡할 수 있다.

2. 모든 코드에 수동으로 `withLogging()` 함수를 적용해야 한다.

<br/>

### **🍳 코드를 감싸지 않고 그냥 함수를 호출할 수 있다면?**

🔻 원래 코드

```js
try {
  saveUserData(user);
} catch (error) {
  logSnapErrors(error);
}

try {
  fetchProduct(productId);
} catch (error) {
  logSnapErrors(error);
}
```

🔻 이름을 명확하게 바꿈

```js
// 로그를 남기지 않음을 알 수 있도록 명확한 이름
try {
  saveUserDataNoLogging(user);
} catch (error) {
  logSnapErrors(error);
}

try {
  fetchProductNoLogging(productId);
} catch (error) {
  logSnapErrors(error);
}
```

🔻 로그를 남기는 함수 

```js
// 함수를 호출할 때 로그가 남을 것이라고 예상할 수 있음
function saveUserDataWithLogging(user) {
  try {
    saveUserDataNoLogging(user);
  } catch (error) {
    logSnapErrors(error);
  }
}

function fetchProductWithLogging(user) {
  try {
    fetchProductNoLogging(productId);
  } catch (error) {
    logSnapErrors(error);
  }
}
```

그러나, 아직 본문(`logSnapErrors(error);`)이 계속 중복되는 문제가 있음

<br/>

### **🍳 익명 함수로 만들고 인자 이름도 조금 더 일반적인 이름으로 바꿔보자**

```js
function(arg) { // 앞부분
  try { // 본문
    saveUserDataNoLogging(arg);
  } catch (error) {
    logSnapErrors(error); // 뒷부분
  }
}

function(arg) { // 앞부분
  try { // 본문
    fetchProductNoLogging(arg);
  } catch (error) {
    logSnapErrors(error); // 뒷부분
  }
}
```

<br/>

### **🍳 함수 본문을 콜백으로 바꾸기 리팩터링을 적용해보자**

함수에 콜백 인자를 추가하는 대신 이 함수를 새로운 함수로 감싼다.

```js
function wrapLogging(func) {// 함수를 인자로 받음
  return function(arg) { // 나중에 실행됨.
    try {
      func(arg);
    } catch(error) {
      logSnapErrors(error); 
    }
  }
}

let saveUserDataWithLogging = wrapLogging(saveUserDataNoLogging)
// 리턴값을 변수에 할당해 이름을 붙인다.
// 로그를 남기지 않는 함수를 변환하디 위해 wrapLoggin() 함수 호출
```

`wrapLogging()` 함수는 `func` 함수를 받아서 `try/catch` 구문으로 감싼 함수를 리턴한다. 이제 로그를 남기지 않는 버전을 로그를 남기는 버전으로 쉽게 바꿀 수 있다. 어떤 함수라도 슈퍼 파워를 줄 수 있게 되었다.

```js
let saveUserDataWithLogging = wrapLogging(saveUserDataNoLogging)
let fetchProductWithLogging = wrapLogging(fetchProductNoLogging)
```

중복도 없앴고, 어떤 함수라도 같은 방식으로 로그를 남기는 함수로 쉽게 바꿀 수 있어졌다.

<br/>

---

<br/>

## **💻 요약**

- 고차 함수로 패턴이나 원칙을 코드로 만들 수 있다. 
  - 고차 함수를 사용하지 않는다면 일일이 수작업을 해야한다. 
  - 고차 함수는 한번 정의하고 필요한 곳에 여러 번 사용할 수 있다.

- 고차 함수로 함수를 리턴하는 함수를 만들 수 있다. 
  - 리턴 받은 함수는 변수에 할당해서 이름이 있는 일반 함수처럼 쓸 수 있다.

- 고차 함수를 사용하면서 잃는 것도 있다. 
  - 고차 함수는 많은 중복 코드를 없애 주지만 가독성을 해칠 수 있다. 
  - 잘 익히고 적절한 곳에 써야 한다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


