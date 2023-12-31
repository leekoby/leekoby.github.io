---
layout: post
title: 쏙쏙 들어오는 함수형 코딩 - CHAPTER 18. 
author: admin
date: 2023-10-18 09:00:00 +900
lastmod: 2023-10-18 09:00:00 +900
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

# **🌈 18. 반응형 아키텍처와 어니언 아키텍처**

이번 장에서 살펴볼 내용

- 반응형 아키텍처로 순차적 액션을 파이프라인으로 만드는 방법

- 상태 변경을 다루기 위한 기본형

- 도메인과 현실 세계의 상호 작용을 위해 어니언 아키텍처

- 여러 계층에 어니언 아키텍처를 적용하는 방법

- 전통적인 계층형 아키텍처와 어니언 아키텍처 비교

  - 반응형 아키텍처 : 순차적인 액션을 표현하는 방식을 뒤집는다.
  - 어니언 아키텍처 : 함수형 프로그래밍으로 현실 세계를 다루기 위한 고수준의 개념

## **💻 두 아키텍처 패턴은 독립적이다.**

두 패턴은 서로 다른 단계에서 사용

반응형 아키텍처는 순차적 액션단계

어니언 아키텍처는 서비스의 모든 단계

함께 사용할 수도 있고 따로 사용할 수도 있음

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9b3176dc-d197-4b14-ab23-b258b800ed39)

### **🍳 반응형 아키텍처**

- 코드에 나타난 순차적 액션의 순서를 뒤집는다.

- 효과와 그 효과에 대한 원인을 분리해서 코드에 복잡하게 꼬인 부분을 풀 수 있다.

<br/>

### **🍳 어니언 아키텍처**

- 웹 서비스나 온도 조절 장치 같은 현실 세계와 상호작용하기 위한 서비스 구조를 만든다.

- 함수형 사고를 적용한다면 자연스럽게 쓸 수 있는 아키텍처

<br/>

---

<br/>

## **💻 변경에 대한 원인과 효과가 강력하게 결합**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/894e002c-c0a7-4fa1-9385-ab4468f8ba18)

- 한쪽에 뭔가 추가하면 다른 쪽에 있는 모든 것을 변경하거나 복제해야 한다.

- 반응형 아키텍처를 사용

<br/>

---

<br/>

## **💻 반응형 아키텍처는 무엇인가**

- 반응형 아키텍처는 애플리케이션을 구조화하는 방법

- 반응형 아키텍처의 핵심 원칙은 이벤트에 대한 반응으로 일어날 일을 지정하는 것

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2a856b17-f08b-4898-bfa9-b50d2b214e98)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e8bdef78-943e-49dc-998f-7a94d64fb716)

<br/>

---

<br/>

## **💻 반응형 아키텍처의 절충점**

- 원인과 효과가 결합한 것을 분리한다.

  - 어떤 경우에는 원인과 효과를 분리하면 코드가 읽기 어려워진다.

  - 하지만 코드가 더 유연하고 하려고 하는 것을 정확하게 표현할 수 있다.

- 여러 단계를 파이프라인으로 처리한다.

- 타임라인이 유연해진다.

<br/>

---

<br/>

## **💻 함수형 프로그래밍과 변경 가능한 상태**

- 변경 가능한 상태를 잘 관리해야 한다.

- 새로운 사용자와 사용자의 행동을 파악하는 것이 중요

- 중요한 것은 상태를 안전하게 사용하는 것

<br/>

---

<br/>

## **💻 반응형 아키텍처가 시스템을 어떻게 바꿨나**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/33cdc49b-bd48-4a4d-8698-ebf347bef26f)

- 어떤 것이 바뀔 때 실행되는 핸들러로 만들었다.

반응형 아키텍처는 코드에 3가지 중요한 영향을 준다.

- 원인과 효과가 결합된 것을 분리한다.

- 여러 단계를 파이프라인으로 처리

- 타임라인이 유연해짐

<br/>

---

<br/>

## **💻 어니언 아키텍처는 무엇인가**

- 현실 세계와 상호작용하기 위한 서비스 구조를 만드는 방법

- 둥글게 겹겹이 쌓인 양파 모양

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/268d6a79-06de-4db5-bd82-fc07a79c11a1)

- 특정 계층이 꼭 필요하다고 강제하지 않음

함수형 시스템이 잘 동작할 수 있는 규칙

1. 현실 세계와 상호작용은 인터랙션 계층

2. 계층에서 호출하는 방향은 중심 방향

3. 계층은 외부에 어떤 계층이 있는지 모름

<br/>

---

<br/>

## **💻 함수형 아키텍처**

### **🍳 전통적 아키텍처 VS 함수형 아키텍처**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b9570561-7c62-4437-8825-6c1c44cb369c)

- 데이터베이스 계층과 도메인 계층의 관계의 차이가 크다.

- 함수형 아키텍처는 도메인 계층이 데이터베이스 계층에 의존하지 않음

- 데이터 베이스 동작은 값을 바꾸거나 데이터베이스에 접근하기 때문에 액션

- 데이터베이스는 변경 가능하고 접근하는 모든 것을 액션으로 만든다는 것이 핵심

- 도메인을 포함해 그래프에 가장 위에 있는 것까지 모두 액션

- 액션과 계산을 명확하게 구분하려고 하고 도메인 로직은 모두 계산으로 만들어야 한다고 생각함

- 데이터베이스를 도메인과 분리하는 것이 중요

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/0225e120-3ab1-431c-8fd9-4c24a3f80365)

<br/>

---

<br/>

## **💻 변경과 재사용이 쉬워야 한다.**

- 어떤 의미에서 소프트웨어 아키텍처는 변화를 다루는 일

- 어니언 아키텍처는 인터렉션 계층을 바꾸기 쉽다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b0ee8b23-b8e8-44b6-acd9-52da24e51662)

다시 정리해보면

- 어니언 아키텍처는 데이터베이스나 API 호출과 같은 외부 서비스를 바꾸기 쉽다.

- 가장 높은 계층에서 사용하기 때문

- 도메인 계층은 외부 서비스에 의존하지 않아서 테스트하기 좋다.

- 어니언 아키텍처는 좋은 인프라보다 좋은 도메인을 강조

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/bb294987-9524-4961-843d-eafd197bf64f)

전형적인 아키텍처에서 도메인 규칙은 데이터베이스를 부른다. 하지만 어니언 아키텍처에서는 안 된다.

전형적인 아키텍처

- 계층이 순서대로
- 웹 요청은 핸들러가 처리
- 핸들러는 데이터베이스에 접속하고 클라이언트에게 응답하기 위해 가장 높은 웹 계층으로 결과를 리턴

어니언 아키텍처

- 경계선이 경사짐
- 웹서버와 핸들러, 데이터베이스는 인터랙션 계층에 속함.

<br/>

---

<br/>

## **💻 도메인 규칙은 도메인 용어를 사용**

프로그램의 핵심 로직을 `도메인 규칙` 또는 `비즈니스 규칙`이라고 한다.

도메인 규칙에는 제품, 이미지, 가격, 할인 등과 같은 용어를 사용

데이터 베이스는 도메인을 나타내는 용어가 아니다.

> <strong  style="color:#ff6600">도메인 규칙은 도메인 용어를 사용한다.</strong>
> 
> <strong  style="color:#ff6600">도메인 규칙에 솏하는지 인터랙션 계층에 속하는지 판단하려면 코드에서 사용하는 용어를 보자</strong>
{:.prompt-info }

<br/>

---

<br/>

## **💻 가독성을 따져 보자**

특정 패러다임의 장점이 항상 좋은 것이 아니다.

가독성을 결정하는 요인은 여러가지가 있다

- 사용하는 언어
- 레거시 코드와 코드 스타일
- 사용하는 라이브러리 
- 개발자들의 습관

### **🍳 코드의 가독성**

도메인 계층을 계산으로 만들어 인터랙션 계층과 분리하면서 읽기 좋은 코드를 만들자

<br/>

### **🍳 개발 속도**

비지니스 이유로 기능을 빨리 출시해야 하는 경우가 있다. 

이런 경우 많은 것을 타협해야 한다.

이런 경우도 나중에 아키텍처에 맞춰 코드를 정리할 준비를 하는 것이 좋다.

<br/>

### **🍳 시스템 성능**

시스템 성능과 타협해야 할 수도 있다.

변경 가능한 데이터 구조는 불변 데이터 구조보다 빠르다.

성능 개선과 도메인을 계산으로 만드는 것은 따로 생각하는 것이 좋다.

최적화는 인터랙션 계층에서 하고 도메인 계층은 재사용 가능한 계산으로 만드는 것

<br/>

---

<br/>

## **💻 요점 정리**

- 반응형 아키텍처는 코드에 나타난 순차적 액션의 순서를 뒤집는다.

  - X를 하고 Y를 하는 것을 X가 발생하면 Y를 하도록 변경

- 반응형 아키텍처는 액션과 계산을 조합해 파이프라인을 만든다.

  - 파이프라인은 순서대로 발생하는 작은 액션들의 조합이다.

- 읽고 쓰는 동작을 제한해 변경 가능한 일급 상태를 만들 수 있다.

- 어니언 아키텍처는 넓은 범위에서 소프트웨어를 세 개의 계층으로 나눈다.

  - 인터랙션과 도메인, 언어 계층

- 가장 바깥 인터랙션 계층은 액션으로 되어 있다.

  - 도메인 계층과 액션을 사용하는 것을 조율한다.

- 도메인 계층은 도메인 로직과 비즈니스 규칙과 같은 소프트웨어의 동작으로 되어 있다.

  - 도메인 계층은 대부분 계산으로 구성된다.

- 언어 계층은 소프트웨어를 만들 수 있는 언어 기능과 라이브러리로 되어 있다.

- 어니언 아키텍처는 프랙털이다.

  - 액션의 모든 추상화 수준에서 찾을 수 있다.

프랙털(fractal) :

- 일부 조각이 젼체와 비슷한 구조

- 어니언 아키텍처를 소프트웨어 전체에 적용할 수도 있지만, 작은 부분에서도 같은 모습으로 적용할 수 있기 때문에 프렉털에 비유


<br/>

---

<br/>

## 📚 **레퍼런스**

> Normand, 김은민, and Normand, Eric. (쏙쏙 들어오는) 함수형 코딩 : 심플한 코드로 복잡한 소프트웨어 길들이기 / 에릭 노먼드 지음 ; 김은민 옮김 (2022). Print.
{:.prompt-info}


