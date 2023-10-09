---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 14. 
author: admin
date: 2023-10-09 15:00:00 +900
lastmod: 2023-10-09 15:00:00 +900
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

# **🌈 14. 중첩된 데이터에 함수형 도구 사용하기**

이번 장에서 살펴볼 내용

- 해시 맵에 저장된 값을 다루기 위한 고차 함수를 만들기
- 중첩된 데이터를 고차 함수로 쉽게 다루는 방법
- 재귀를 이해하고 안전하게 재귀를 사용하는 방법
- 깊이 중첩된 엔티티에 추상화 벽을 적용해서 얻을 수 있는 장점

## **💻 객체를 다루기 위한 고차 함수**

이전 챕터에서는 배열을 다루는 고차 함수로 작업해봤다.

객체를 다룰 수 있는 고차함수가 있다면 유용할 것 같다.

<br/>

---

<br/>

## **💻 update() 도출하기**

🔻예시 코드

```js
function incrementField(item, field){
  let value = item[field];
  let newValue = value + 1;
  let newItem = objectSet(item, field, newValue);
  return newItem;
}

function decrementField(item, field){
  let value = item[field];
  let newValue = value - 1;
  let newItem = objectSet(item, field, newValue);
  return newItem;
}

function doubleField(item, field){
  let value = item[field];
  let newValue = value * 2;
  let newItem = objectSet(item, field, newValue);
  return newItem;
}

function halveField(item, field){
  let value = item[field];
  let newValue = value / 2;
  let newItem = objectSet(item, field, newValue);
  return newItem;
}
```

모든 코드가 비슷하다는 것을 알 수 있다. 하려는 동작만 다르다.

동작은 함수 이름에 들어 있고, 그 이름으로 부른다.

이 함수들로 어떤 객체라도 바꿀 수 있는 함수를 도출한다면 중복을 많이 없앨 수 있다.

이 코드에서는 동시에 두 가지 리팩터링을 해야한다.

- 함수 이름에 있는 암묵적인 인자를 암묵적 인자를 드러내기 리팩터링으로 동작 이름을 명시적인 인자로 바꾸기

- 명시적으로 바꿔야 할 인자가 일반값이 아닌 동작이기 때문에 함수 본문을 콜백으로 바꾸기 리팩터링으로 동작을 함수 인자로 받도록 해야 함.

```js
function incrementField(item, field){
  return updateField(item, field, function(value){
    return value + 1;
  })
}

function updateField(item, field, modify){
  let value = item[field];
  let newValue = modify(value);
  let newItem = objectSet(item, field, newValue);
  return newItem;
}
```

모든 동작을 고차 함수 하나로 합쳤다. 

특정 필드를 바꾸는 함수가 아니기 떄문에 함수 이름에 field는 빼고 일반적인 이름으로 변경해주자.

```js
function update(object, key, modify){
  let value = object[key];
  let newValue = modify(value);
  let newObject = objectSet(object, key, newValue);
  return newObject;
}
```

update()는 객체에 있는 값을 바꾼다.

바꿀 객체와 바꾸려는 키, 바꾸는 동작을 함수로 넘기면 된다.

objectSet()을 사용하기 때문에 카피-온-라이트 원칙을 따른다.

<br/>

---

<br/>

## **💻 리팩터링 : 조회하고 변경하고 설정하는 것을 update()로 교체하기**

```js
// 리팩터링 전
function incrementField(item, field){
  let value = item[field]; // 조회
  let newValue = value + 1; // 바꾸기
  let newItem = objectSet(item, field, newValue); // 설정
  return newItem;
}

//리팩터링 후
function incrementField(item, field){
  return updateField(item, field, function(value){
    return value + 1;
  })
}
```

리팩터링 전 코드를 보면 전체 동작은 세 단계다.

1) 객체에서 값을 조회
2) 값을 바꾸기 
3) 객체에 값을 설정(카피-온-라이트 사용)

### **🍳 조회하고 변경하고 설정하는 것을 update()로 교체하기 단계**

이 리팩터링은 두 단계로 되어 있다. 

1) 조회하고 바꾸고 설정하는 것을 찾는다.
2) 바꾸는 동작을 콜백으로 전달해서 update()로 교체한다.

#### **단계 1: 조회하고 바꾸고 설정하는 것을 찾는다.**

```js
function halveField(item, field){
  let value = item[field]; // 설정
  let newValue = value /2; //바꾸기
  let newItem = objectSet(item, field, newValue);
  return newItem;
}
```

#### **단계 2: update()로 교체한다.**

```js
function halveField(item, field){
  return update(item, field, function(value){
    return value / 2; //바꾸는 동작을 콜백으로 전달
  })
}
```

<strong  style="color:#ff6600">조회하고 변경하고 설정하는 것을 `update()`로 교체하기</strong> 리팩터링은 중첩된 객체에 적용하기 좋다.


<br/>

---

<br/>

## **💻 중첩된 update 시각화 하기**

```js
let shirt = {
  name : 'shirt',
  price : 13,
  options : { // 객체 안에 객체가 중첩
    color : 'blue',
    size : 3 // options 객체 안에 있는 값을 꺼내야 한다.
  }
}

function increamentSize(item){
  let options = item.options; // 조회
  let size = option.size; // 조회
  let newSize = size + 1; // 변경
  let newOptions = objectSet(options, 'size', newSize); // 설정
  let newItem = objectSet(item, 'options', newOptions); // 설정
  return newItem;
}// 리팩터링 했지만 뭔가 이상한데! 
```

<br/>

---

<br/>

## **💻 중첩된 데이터에 update() 사용하기**

### **🍳 조회하고 변경하고 설정하는 것을 update()로 교체하기**

```js
//원래 코드
function increamentSize(item){
  let options = item.options; 
  let size = option.size; //  조회
  let newSize = size + 1; //  변경
  let newOptions = objectSet(options, 'size', newSize); // 설정
  let newItem = objectSet(item, 'options', newOptions); 
  return newItem;
}

// 리팩터링 후 
function increamentSize(item){
  let options = item.options; // 조회
  let newOptions = update(options, 'size', increment); // 변경
  let newItem = objectSet(item, 'options', newOptions); // 설정
  return newItem;
}
```

한번 더 리팩터링할 수 있다.

```js
// 한 번 리팩터링한 코드
function increamentSize(item){
  let options = item.options; // 조회
  let newOptions = update(options, 'size', increment); // 변경
  let newItem = objectSet(item, 'options', newOptions); // 설정
  return newItem;
}

// 두 번 리팩터링한 코드 
function increamentSize(item){
  return update(item, 'options', function(options){
    return update(options, 'size', increment); 
  })
}
```

중첩된 객체에 중첩된 update를 사용할 수 있다는 중요한 사실을 알았다.

update()를 중첩해서 부르면 더 깊은 단계로 중첩된 객체에도 사용할 수 있다.


<br/>

---

<br/>

## **💻 updateOption() 도출하기**

update() 안에서 update() 를 호출 하는 코드를 만들었따.

이 코드를 일반화해서 updateOption() 을 만들 수 있다.

```js
function increamentSize(item){
  return update(item, 'options', function(options){
    return update(options, 'size', increment); 
  })
}
```

update()를 두 번 호출하고 `size` 데이터도 두 단계로 중첩된 것을 볼 수 있다.

데이터가 중첩된 만큼 update()를 호출해야 하는 것을 알 수 있다.

위 코드에서는 아직도 냄새가 난다. 함수 이름에 있는 암묵적 인자를 본문에서 두번이나 쓰고 있다. 

이것을 해결해보자.

```js
//암묵적 option 인자
function increamentSize(item){
  return update(item, 'options', function(options){
    return update(options, 'size', increment); 
  })
}

//명시적 option 인자로 수정
function increamentOption(item, option){
  return update(item, 'options', function(options){
    return update(options, option, increment); 
  })
}

//명시적 modify 인자로 수정
function updateOption(item, option, modify){
  return update(item, 'options', function(options){
    return update(options, option, modify); 
  })
}
```

이 함수는 제품(객체)과 옵션 이름, 옵션을 바꾸는 함수를 받는다.

```js
function updateOption(item, option, modify){
  return update(item, 'options', function(options){
                      // 여전히 코드 냄새!!
    return update(options, option, modify); 
  }) 
}
```

<br/>

---

<br/>

## **💻 update2() 도출하기**

새로운 암묵적 인자가 생겼다.

리팩터링을 한 번 더 리팩터링 하면 일반적인 함수 update2()를 도출할 수 있다.

```js
function updateOption(item, option, modify){
  return update(item, 'options', function(options){
                      // 여전히 코드 냄새!!
    return update(options, option, modify); 
  }) 
}
```

세 번째 리팩터링 

함수 이름, 인자를 일반적으로 바꾼다.

```js
function update2(object, key1, key2, modify){
  return update(object, key1,, function(value1){
    return update(value1, key2, modify); 
  }) 
}
```

좀 더 일반적인 함수가 되었다. update2() 는 두 단계로 중첩된 어떤 객체에도 쓸 수 있는 함수. 

그래서 함수를 쓸 때 두 개의 키가 필요하다.

```js
function incrementSize(item){
  return undate2(item, 'options', 'size', function(size){
    return size + 1;
  })
}
```

<br/>

---

<br/>

## **💻 incrementSizeByName()을 만드는 네 가지 방법**

알고 보니 객체가 한 번 더 중첩되어 있었다!

```js
let cart = {
  shirt : { 
    name : 'shirt',
    price : 13,
    options : {
      color : 'blue',
      size : 3
    }
  }
}
```

이 코드를 고치려면 update3()이 필요해 보이는데 ...

이 문제를 해결할 수 있는 네 가지 방법을 살펴보자.

장바구니 안에 특정 이름을 가진 제품의 크기 옵션을 늘리고 싶을 때 사용할 함수를 `incrementSizeByName()`이라고 해보자.

이 함수는 장바구니와 제품의 이름을 받아서 해당 제품의 크기 옵션을 늘린다. 

어떻게 구현할 수 있을까...?

### **🍳 옵션 1 : update()와 incrementSize()로 만들기**

장바구니 안에 중첩된 제품을 다뤄야 한다.

update() 는 중첩된 객체의 값을 바꿀 수 있기 때문에 incrementSize() 함수를 만들면 된다.

```js
function incrementSizeByName(cart, name){
  return update(cart, name, incrementSize);
}// 이미 있는 도구를 활용한 직관적인 방법.
```

<br/>

### **🍳 옵션 2 : update()와 update2()로 만들기**

```js
function incrementSizeByName(cart, name){
  return update(cart, name, function(item){
    return update2(item, 'options', 'size', function(size){
      // update()안에 있는 update2()로 incrementSize()를 인라인으로 구현
      return size + 1;
    })
  })
}
```

<br/>

### **🍳 옵션 3 : update()로 만들기**

두 번 중첩된 update()로 update2()를 인라인으로 만들 수 있다.

```js
function incrementSizeByName(cart, name){
  return update(cart, name, function(item){
    return update(item, 'options', function(options){
      return update(options, 'size', function(size){
        return size + 1; // update()만 불러서 인라인으로 구현
      })
    })
  })
}
```

<br/>

### **🍳 옵션 4 : 조회하고 바꾸고 설정하는 것을 직접 만들기**

```js
function incrementSizeByName(cart, name){
  let item = cart[item];
  let options = item.options;
  let size = options.size;
  let newSize = size + 1;
  let newOptions = objectSet(options, 'size', newSize);
  let newItem = objectSet(item, 'options', newOptions);
  let newCart = objectSet(cart, name, newItem);
  return newCart;
}
```

<br/>

---

<br/>

## **💻 update3() 도출하기**

옵션2의 코드를 가지고 암묵적 인자 드러내기 리팩터링을 통해 update3()을 만들어보자.

```js
// 옵션 2
function incrementSizeByName(cart, name){
  return update(cart, name, function(item){ //
    return update2(item, 'options', 'size', function(size){ // 암묵적 인자
      return size + 1;
    })
  })
}

// 리팩터링 코드
function incrementSizeByName(cart, name){
  return update3(cart, name, 'options', 'size', function(size){
                      // 경로 3개    
    return size + 1;
  })
}

function update3(object, key1, key2, key3, modify){
  return update(object, key1, function(object2){
    return update2(object2, key2, key3, modify);
  });
}
```

<br/>

---

<br/>

## **💻 nestedUpdate() 도출하기**

중첩된 개수에 상관없이 쓸 수 있는 nestedUpdate()를 만들어 보자.

패턴이 명확하지 않지만, 함수를 살펴보면 단서가 있다.

만약 updateX()를 만들려고 한다면 update() 안에 updateX-1()을 불러주면 된다.

다만 update0()는 두 가지 이유로 지금 패턴과 다르다.

하나는 사용하는 키가 없기 때문에 키가 한 개 필요한 update()를 부를 수 없다.

두 번째 이유는 `X-1` 이 `-1` 이 되기 때문에 경로 길이를 표현할 수 없다.

조회나 설정을 하지 않고 그냥 변경만 하는 함수이므로, 찾으려는 값만 있으면 되기 때문에 update0()은 modify()를 그냥 호출하는 함수가 된다.

```js
function update0(value, modify){
  return modify(value);
}
```

키의 개수와 동일하게 함수의 이름에 암묵적 인자가 포함되어 냄새가 난다. 

암묵적 인자를 들어내기 리팩터링을 해보자

```js
              //X        // X만큼의 키
function update3(object, key1, key2, key3, modify){
  return update(object, key1, function(value1){
    return update2(object2, key2, key3, modify);
              // X-1      // 첫 번째 키는 제외
  });
}
```

`depth`라는 인자를 추가해보자.

```js
function updateX(object, depth, key1, key2, key3, modify){
  return update(object, key1, function(value1){
    return updateX(value1, depth-1, key2, key3, modify);
  });     //재귀호출      depth-1 전달
}
```

깊이와 키 개수를 어떻게 맞출 수 있을까?

depth 인자와 실제 키 개수는 달라질 수 있어서 버그가 생길 것 같다.

키의 개수와 순서가 중요하다는 점이 단서가 된다.

모든 키를 배열로 넘긴다면 depth 인자는 배열의 길이가 된다.

```js
function updateX(object, keys, modify){
  let key1 = keys[0]; // 첫번째 키로 update() 호출
  let restOfKeys = drop_first(keys); // 나머지 키로 재귀 함수를 호출
  return update(object, key1, function(value1){
    return updateX(value1, restOfKeys, modify);
  });     //재귀호출      
}
```

update0()를 제외하면 같은 패턴을 가지고 있기 때문에 updateX()로 바꿔 쓸 수 있다. 

update0()는 update() 함수를 호출하지 않는데 어떻게 해야할까..

keys 배열의 길이가 0일 때는 키가 없다는 것이므로 이 경우에는 modify()를 호출해주고 그렇지 않은 경우에는 updateX()를 호출하면 될 것같다.

```js
function updateX(object, keys, modify){
  if(keys.length === 0){
    return modify(object);  // 재귀호출 없음
  }
  let key1 = keys[0]; // 첫번째 키로 update() 호출
  let restOfKeys = drop_first(keys); // 나머지 키로 재귀 함수를 호출
  return update(object, key1, function(value1){
    return updateX(value1, restOfKeys, modify);
  });     //재귀호출      
}
```

이렇게 해주면 키 길이에 상관없이 쓸 수 있는 updateX()함수가 생겼다.

updateX()라는 이름보다 `nestedUpdate()` 라는 이름을 사용하는 것이 더 일반적이다.

객체와 중첩된 객체의 값을 가르키는 키 경로와 바꿀 함수를 인자로 받는다.

그리고 빠져나오는 모든 경로에 있는 객체의 복사본을 만든다

```js
function nestedUpate(object, keys, modify){
  if(keys.length === 0){
    return modify(object);  // 종료 조건(경로의 길이가 0일 때)
  }
  let key1 = keys[0]; 
  let restOfKeys = drop_first(keys); // 종료 조건에 가까워지게 항목을 하나씩 없앰
  return update(object, key1, function(value1){
    return updateX(value1, restOfKeys, modify);
  });     //재귀호출      
}
```

<br/>

---

<br/>

## **😀 안전한 재귀 사용법**

재귀는 for나 while 반복문처럼 무한 반복에 빠질 수 있다.

가이드를 잘 지켜서 문제가 생기지 않도록 하자

### **🍳 1. 종료 조건**

재귀를 멈추려면 <strong  style="color:#ff6600">종료 조건</strong>(base case)이 필요하다.

종료 조건은 재귀가 멈춰야 하는 곳에 있어야 한다. 더는 재귀를 호출하지 않으므로 그 위치에서 재귀가 종료된다.

```js
function updateX(object, keys, modify){
  if(keys.length === 0){ // 종료 조건
    return modify(object);  // 재귀호출 없음
  }
  let key1 = keys[0]; 
  let restOfKeys = drop_first(keys); 
  return update(object, key1, function(value1){
    return updateX(value1, restOfKeys, modify);
  });    
}
```

종료 조건은 확인하기 쉽다.

보통 배열 인자가 비었거나 점점 줄어드는 값이 0이 되었거나, 찾아야 할 것이 없을 때 종료 조건이 된다. 종료 조건은 만들기 쉽다.

<br/>

### **🍳 2. 재귀 호출**

재귀 함수는 최소 하나의 <strong  style="color:#ff6600">재귀 호출</strong>이 있어야 한다.

재귀 호출이 필요한 곳에서 재귀 호출을 해야 한다.

```js
function updateX(object, keys, modify){
  if(keys.length === 0){
    return modify(object);  
  }
  let key1 = keys[0]; 
  let restOfKeys = drop_first(keys); // 남은 키가 하나 감소
  return update(object, key1, function(value1){
    return updateX(value1, restOfKeys, modify);
  });     //재귀호출      
}
```

<br/>

### **🍳 종료 조건에 다가가기**

재귀 함수를 만든다면 최소 하나 이상의 인자가 점점 줄어들어야 한다.

그래야 종료 조건에 가까워질 수 있다. 

- 예를 들어, 종료 조건이 빈 배열이라면 각 단계에서 배열 항목을 없애야 한다.

각 재귀 호출에서 한 단계씩 종료 조건에 가까워진다면 결국 종료 조건과 일치해 재귀 함수가 끝나게 된다.

가장 좋지 않은 것은 재귀 호출에 같은 인자를 그대로 전달하는 것이다. 이렇게 되면 무한 반복에 빠질 가능성이 높아진다.

<br/>

---

<br/>

## **🤔 깊이 중첩된 데이터에 추상화 벽 사용하기**

깊이 중첩된 데이터에 nestedUpdate()를 쓰려면 긴 키 경로가 필요하다.

키 경로가 길면 중간 객체가 어떤 키를 가졌는지 기억하기 어렵다. 중첩된 각 단계의 데이터 구조를 모두 기억해야 한다.

문제를 해결 하기 위해서는 같은 작업을 하면서 <strong  style="color:#ff6600">알아야 할 데이터 구조를 줄이는 것</strong>이다.

추상화 벽에 함수를 만들고 의미있는 이름을 붙여서 해결할 수 있다.

추상화 벽을 만들 때는 사용하려는 데이터의 이해도를 높일 수 있는 방향으로 만들어야 한다.

예시 코드를 보자.

```js
// 주어진 ID로 블로그를 변경하는 함수
function updatePostById(category, id, modifyPost){
  return nestedUpdate(category, ['posts', id], modifyPost);
}

// 글쓴이를 수정하는 함수
function updateAuthor(post, modifyUsers){
  return update(post, 'author', modifyUser);
}

// 사용자 이름을 대문자로 바꾸는 함수
function capitalizeName(user){
  return update(user, 'name', capitalize);
}

// 모두 함친 함수
updatePostById(blogCategory, '12', function(post){
  return updateAuthor(post, capitalizeUserName);
});
```

더 좋아진 점이 두 가지 정도가 있다.

1) 네 가지에서 세 가지로 줄었다는 점
   - 기억해야 할 것이 하나 줄었기 때문에 좋아졌다고 할 수 있다.

2) 동작의 이름이 있으므로 각각의 동작을 기억하기 쉽다.
   - 분류 안에 블로그 글이 있다는 것을 알고 있다.
   - 이제 어떤 키에 들어 있는지 기억하지 않아도 된다.
   - 글쓴이도 마찬가지

<br/>

---

<br/>

## **💻 요점 정리**

- update() 는 일반적인 패턴을 구현한 함수형 도구다.
  - update() 를 사용하면 객체 안에서 값을 꺼내 변경하고 다시 설정하는 일을 수동으로 하지 않아도 된다.

- nestedUpdate() 는 깊이 중첩된 데이터를 다루는 함수형 도구다
  - 바꾸려고 하는 값이 어디 있는지 가르키는 키 경로만 알면 중첩된 데이터를 쉽게 바꿀 수 있다.

- 보통 일반적인 반복문은 재귀보다 명확하다.
  - 하지만 중첩된 데이터를 다룰 때는 재귀가 더 쉽고 명확하다.

- 재귀는 스스로 불렀던 곳이 어디인지 유지하기 위해 `스택`을 사용한다.
  - 재귀 함수에서 스택은 중첩된 데이터 구조를 그대로 반영한다.

- 깊이 중첩된 데이터는 이해하기 어렵다.
  - 깊이 중첩된 데이터를 다룰 때 모든 데이터 구조와 어떤 경로에 어떤 키가 있는지 기억해야 한다.

- 많은 키를 가지고 있느 ㄴ깊이 중첩된 구조에 추상화 벽을 사용하면 알아야 할 것이 줄어든다.
  - 추상화 벽으로 깊이 중첩된 데이터 구조를 쉽게 다룰 수 있다.

<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


