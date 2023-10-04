---
layout: post
title: 웹 표준 (web-standard)
author: admin
date: 2023-02-28 00:00:00 +900
lastmod: 2023-02-28 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [WEB]  # 대문자로 작성
tags: [web standards] # 소문자로 작성
---

> 기존 블로그에 작성했던 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 사용자 친화 웹의 내용 중 웹표준에 대해 학습한 내용을 정리하며 기록한 내용입니다
{:.prompt-info}

<br/>

---

# **📖 웹 표준**

<br/>

## **💻 웹 표준이란**

- [W3C(World Wide Web Consortium)](https://www.w3.org/){:target="_blank"}에서 권고하는 <strong style="color:#3366ff">‘웹에서 표준적으로 사용되는 기술이나 규칙’</strong> 

- 사용자가 <strong style="color:#3366ff">어떠한 운영체제나 브라우저를 사용하더라도 </strong> 웹페이지가 <strong style="color:#3366ff"> 동일하게 보이고 정상적으로 작동 </strong>할 수 있도록 하는 웹 페이지 제작 기법 

- 웹 개발에 사용되는 언어인 `HTML`, `CSS`, `JavaScript` 등의 기술 

- 이 세 기술은 화면의 구조, 표현, 동작을 각각 담당 

- 표준 스펙을 잘 지키는 것뿐만 아니라 구조적 마크업(`XHTML`)과 표현 및 레이아웃(`CSS`) 및 사용자 행위 제어(`DOMScripting`)를 잘 분리하는 고급 홈페이지 구축 방식. 

- `CSS` 와 `HTML(XHTML)`로 웹 문서를 작성하는 것의 명확한 용어는 권고(`recommend`)라고 하며 버전과 상관없이 `HTML`, `XHTML`은 그 자체로 표준 

크롬, 에지, 사파리, 오페라, 파이어폭스 등 최신 웹 브라우저들은 모두 웹 표준을 지원한다. 

웹 표준에 맞추어 웹 페이지를 작성하면 어떤 브라우저를 사용하든 동일한 결과물을 얻을 수 있다. 

앞서 언급된 브라우저 호환 때문에 화면이 제대로 표시되지 않거나 기능이 작동하지 않는 등의 문제 상황을 방지할 수 있는 것이다. 

따라서 가능한 <strong style="color:#3366ff"> 웹 표준을 준수한 웹 페이지를 개발하기 위해서 노력 </strong>하는 것이 좋다.

<br/>

---

### **🔎 웹 표준의 장점**

<br/>

#### **운영관리** 

- 각 영역이 분리되면서 <strong style="color:#3366ff"> 유지 보수가 용이 </strong>해졌고, 코드가 경량화되면서 트래픽 비용이 감소하는 효과 

- 논리적이고 효율적으로 작성된 웹 문서는 코드의 양이 줄어 <strong style="color:#3366ff"> 파일 크기가 줄고 서버부담의 감소 </strong> 

- CSS와 HTML이 분리되어 유지보수에 들어가는 시간이 단축되고, <strong style="color:#3366ff"> 불필요한 마크업이 최소화되어 페이지 로딩속도가 향상 </strong> 

#### **브라우저 호환성** 

- 웹 브라우저의 종류나 버전, 운영 체제나 사용 기기 <strong style="color:#3366ff"> 종류에 상관없이 항상 동일한 결과 </strong>가 나오도록 할 수 있다. 

#### **검색 효율성 증대** 

- 웹 표준에 맞춰 웹 사이트를 작성하는 것만으로도 검색 엔진에서 더 높은 우선순위로 노출될 수 있다. 

- 적절한 HTML 요소의 사용, 웹 페이지에 대한 정확한 정보 작성 등 검색 효율성과 관련된 내용도 웹 표준에서 다루고 있기 때문 

- 검색봇이 시멘틱을 해석하는데 도움을 주기 때문에 <strong style="color:#3366ff"> 검색엔진 최적화 </strong>가 가능 

#### **웹 접근성 향상** 

- 다양한 브라우저, 휴대폰, 장애인 지원용 프로그램에서도 가능하므로 접근성이 향상

- 장애인, 고령자 등을 포함한 <strong style="color:#3366ff"> 사용자층도 확대 가능 </strong> 

<br/>

--- 

### **🔎 웹표준 관련 기술**

![웹표준 관련 기술](https://github.com/leekoby/leekoby.github.io/assets/118284808/44ec77d5-6db0-4730-9783-ea3a27203260)

-	`XHTML` ( eXtensible Hypertext Markup Language ) 

-	`CSS` (Cascading Style Sheets) 

-	`XML` (eXtensible Markup Language) 

-	`DOM`(Document Object Model) 

-	`ECMAScript` : ECMA international 의 ECMA-262 기술 명세에 정의된 표준화된 스크립트 프로그래밍 언어

<br/>

### **🔎 Semantic HTML** 

<br/> 

웹 표준에서는 HTML을 <strong style="color:#3366ff"> 시맨틱(semantic)하게 작성하는 것의 중요성을 강조 </strong> 

`시맨틱 HTML`은 영단어인 `semantic`과 `HTML`의 합성어이고, 각 단어의 의미는 다음과 같다.

- <strong style="color:#3366ff"> semantic </strong> : 의미의, 의미가 있는 이라는 뜻의 영단어 

- <strong style="color:#3366ff"> HTML </strong> : 화면의 구조를 만드는 마크업 언어 

그렇다면, 이 두 단어를 합쳐서 만든 <strong style="color:#3366ff"> 시맨틱 HTML은 HTML이 구조를 만드는 것을 넘어 의미를 갖도록 만들겠다는 의도 </strong>가 담겨있다고 할 수 있다.

<br/>

#### **🔗 `<div>` 와 `<span>` 으로 화면 구성하기** 

HTML에는 굉장히 많은 종류의 요소가 있다. 

하지만 어떤 요소를 가지고 있는지 전부 알지 못해도, `<div>` 와 `<span>` 두 가지의 요소만 알면 충분히 화면의 구조를 만들 수 있다. 

아래의 예시가 `<div>` 와 `<span>` 만 사용해서 화면을 구성한 형태다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/0cd6fe0b-0c84-4145-923f-649bbe067ed2)

예시의 HTML 구조만 보고 각 요소들이 어떤 역할을 할지 알아낼 수 있을까? 

추측해 보자면, 아마도 가장 상단의 요소가 헤더 역할을, 가장 좌측의 요소가 메뉴가 들어있는 내비게이션 바 역할을 할 가능성이 높을 것 같다. 

하지만, 확신할 수는 없다. 

위 이미지로는 화면이 어떤 구조로 나누어져 있는지 까지만 알 수 있고, 어떤 내용이 들어갈지는 명시되어 있지 않기 때문이다.

<br/>

#### **🔗 시맨틱 요소로 화면 구성하기**

<br/>

이번에는 화면을 같은 구조로 나누되, 다른 종류의 요소를 사용한 예시다. 

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e7ed1723-3c05-4130-b53b-7654935f9814)

`<div>` 와 `<span>` 요소로만 화면을 구성했을 때와 비교하면, 각 요소의 이름만 보고도 화면에서 어떤 역할을 하게 될지, 어떤 내용을 담게 될지 보다 더 명확하게 알 수 있게 되었다. 

요소의 이름에 맞게 화면을 구성한다면, `<header>` 는 헤더의 역할을, `<nav>` 는 내비게이션 바 역할을 하게 될 것이다. 

이처럼 <strong style="color:#3366ff"> 요소가 어떤 내용을 담게 될지, 어떤 기능을 하게 될지 확실하게 의미를 가지고 있는 요소를 시맨틱 요소 </strong>라고 한다.

<strong style="color:#3366ff"> 시맨틱 요소를 적절하게 사용하여 구성한 HTML을 시맨틱 HTML </strong>이라고 한다.

<br/>

### **🔎 시맨틱 HTML의 필요성**

그렇다면 `시맨틱한 HTML`은 왜 필요한 것일까?  

<br/>

#### **🔗 개발자간 소통**

여러 명의 개발자가 웹 페이지를 개발하면서 `<div>` 와 `<span>`으로만 HTML 코드를 작성한다고 해보자. 

그렇다면 요소의 이름을 보고서는 각 요소가 어떤 기능을 하는지 전혀 파악할 수 없기 때문에 주석을 작성해서 설명하거나 `id` 나 `class` 를 사용해서 일일이 표기해야 한다. 

만약 id와 `class` 를 사용할 경우에는 이름은 어떻게 지을 것인지도 협의해서 정해야 하고, 다른 개발자에게 `id` 와 `class` 이름이 어떤 의미인지 설명하는 시간도 추가로 필요하게 된다. 

이런 귀찮은 과정을 시맨틱 한 요소를 사용하기만 해도 없앨 수 있다. 

적어도 각 요소의 기능을 정의하느라 쏟아야 하는 시간은 들지 않을 것이다.

<br/>

#### **🔗 검색 효율성**

<strong style="color:#3366ff"> 검색 엔진은 `HTML 코드`를 보고 문서의 구조를 파악 </strong>한다. 

`<div>` 와 `<span>` 만 사용한 문서에서는 모든 요소가 비슷한 중요도의 내용을 담고 있다고 판단한다. 

하지만 시맨틱 요소를 사용하면, 어떤 요소에 더 중요한 내용이 들어있을지 우선순위를 정할 수 있고, 우선순위가 높다고 파악된 페이지를 검색 결과 상단에 표시하게 된다. 

웹 페이지를 검색 엔진에 더 자주 뜨게 만들고 싶을 때, 광고비 등의 추가 지출을 하지 않고 시맨틱 `HTML`을 잘 구성하는 것만으로도 어느 정도 효과를 볼 수 있다는 의미다.

<br/>

#### **🔗 웹 접근성**

<strong style="color:#3366ff"> 웹 접근성은 나이, 성별, 장애 여부, 사용 환경을 떠나서 항상 동일한 수준의 정보를 제공할 수 있어야 함 </strong>을 뜻한다. 

한 가지 예시를 들면, 시각 장애인의 경우 웹 페이지에 접근할 때 음성으로 화면을 스크린리더를 이용하게 되는데, 이때 HTML이 시맨틱 요소로 구성되어 있다면 화면의 구조에 대한 정보까지 추가로 전달해 줄 수 있어 콘텐츠를 좀 더 정확하게 전달할 수 있게 된다. 

<strong style="color:#3366ff"> 시맨틱 요소만 잘 사용해도 웹 접근성을 향상할 수 있다는 의미 </strong>다

<br/>

### **🔎 시맨틱 요소의 종류**

자주 사용되는 시맨틱 요소들은 다음과 같다.

| 요소 종류 | 설명 | 
| :--------------------: | :--------------------- | 
| `<header>` | 페이지나 요소의 최상단에 위치하는 머리말 역할의 요소 | 
| `<nav>` | 메뉴, 목차 등에 사용되는 요소| 
| `<aside>` | 문서와 연관은 있지만, 직접적인 연관은 없는 내용을 담는 요소 | 
| `<main>` | 이름 그대로 문서의 메인이 되는 주요 콘텐츠를 담는 요소 | 
| `<article>` | 게시글, 뉴스 기사 등 독립적으로 구분해 재사용할 수 있는 부분을 의미하는 요소 <br/> 각각의 `<article>`을 구분하기 위한 수단이 필요하며, 보통 제목(`<hgroup>`)을 포함하는 방법을 사용 | 
| `<section>` | 문서의 독립적인 구획을 나타내며, 딱히 적합한 의미의 요소가 없을 때 사용, 제목(`<hgroup>`)을 포함하는 경우가 많다 | 
| `<hgroup>` | 제목을 표시할 때 사용하는 요소로, `<h1>` ~ `<h6>` 요소가 `<hgroup>`다. | 
| `<footer>` | 페이지나 요소의 최하단에 위치하는 꼬리말 역할의 요소 |

<br/>

### **🔎 예시로 보는 시맨틱 HTML**

이번에는 MDN 사이트의 HTML 구조가 어떻게 구성되어 있는지 살펴보면서 실제로 시맨틱 요소가 어떻게 사용되고 있는지 살펴보도록 하자. 

> [MDN - Semantics ](https://developer.mozilla.org/ko/docs/Glossary/Semantics){:target="_blank"}
{:.prompt-info}

<br/>

#### **🔗 `<header>`**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/3c569035-195a-4650-b2e6-722ae4fb8ffb)

- 현재 페이지에서는 페이지 최상단에 위치하고 있다.

- 페이지 최상단에 위치하는 경우 사이트 전체에서 사용할 수 있는 내용을 담는다. 

- 지금은 로고, 사이트 이름, 사이트를 구성하는 메인 페이지들, 다크모드, 검색 폼 등의 콘텐츠가 들어가 있다. 

- `<article>`, `<main>` 등 다른 요소의 머리말 역할로도 사용할 수 있다 

- 우측의 `<aside>` 요소에도 `<header>` 요소가 들어가 있다.

<br/>

#### **🔗 `<nav>`**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1bc8f184-b4f5-42e8-8a8b-2cd75d71d285)

- 내비게이션 바의 역할로, 링크들을 담고 있다.

<br/>

#### **🔗 `<aside>`**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/850daaac-d8f6-40a1-b272-20814c40084b)

-	메인 콘텐츠와 관련이 있긴 하지만, 직접적인 연관은 없는 내용이 들어가는 요소

-	우측에 있는 제목별 북마크, 페이지 하단에 있는 콘텐츠 오류 제보 등의 내용이 `<aside>` 요소 안에 들어가 있다.

<br/>

#### **🔗 `<main>`**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b663bdd8-f27e-4c20-8100-7fe1b711d46c)

-	페이지의 메인 콘텐츠가 들어가는 요소.

-	해당 페이지의 주제인 Semantics와 관련된 내용이 들어있다.

<br/>

#### **🔗 `<article>`**

<br/>

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/48db7356-1724-4816-809e-cc9d0b57a26b)

-	메인 콘텐츠와 관련이 있긴 하지만, 직접적인 연관은 없는 내용이게시글, 뉴스 기사처럼 독립적으로 구분해 재사용할 수 있는 부분을 의미하는 요소

-	재사용하는 부분인 만큼, 각각의 `<article>` 을 식별할 수 있는 요소가 필요

	-	`<hgroup>` 요소를 사용하여 식별하는 경우가 많다.

<br/>

#### **🔗 `<section>`**

<br/>

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/8bbe0bc3-7ab2-4104-b148-15cfb20c1514)

-	문서의 독립적인 구획을 나타내며, 딱히 적합한 의미의 요소가 없을 때 사용

-	적합한 의미의 요소가 없을 때 사용하기 때문에, 의미를 부여하기 위한 요소를 포함시키는 것이 좋다.

  -	`<hgroup>` 을 포함하는 경우가 많다.

<br/>

#### **🔗 `<hgroup>`**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/da625ece-97e1-433c-8eb1-dbff50e49438)

-	제목을 표시하기 위해서 사용하는 요소

-	`<h1>` 부터 `<h6>` 까지 있으며, 숫자가 클 수록 글자의 크기는 작아진다.

	-	숫자가 작은 제목이 숫자가 큰 제목을 포함하는 방식으로 사용

-	해당 페이지에서는 `<article>` 요소의 제목으로는 `<h1>`, `<section>` 요소의 제목으로는 `<h2>` 를 사용하고 있다.

	-	`<hgroup>` 요소의 숫자만 봐도 `<article>` 요소가 `<section>` 요소를 포함하는 관계임을 파악할 수 있다.

<br/>

#### **🔗 `<footer>`**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/35d40049-e981-48b9-898d-1546b5dd449e)

-	페이지나 요소의 최하단에 위치하는 꼬리말 역할의 요소. 

- 사이트 제공자, 저작권, 정책, 사이트맵 등의 내용을 포함하고 있다. 

<br/> 

여기서 소개한 요소들 외에도 다양한 시맨틱 요소들이 있다. 예시로 분석해 본 [ MDN 사이트 Semantics 페이지 ](https://developer.mozilla.org/ko/docs/Glossary/Semantics){:target="_blank"}에서 확인해 볼 수 있다. 

시맨틱 요소가 무엇인지 이해했다면, 자주 들어가는 사이트가 `시맨틱 HTML`로 구성되어 있는지 콘솔창의 `element` 탭에서 직접 분석해 보는 것도 좋다.

<br/>

### **🔎 자주 틀리는 마크업**

#### **🔗 인라인 요소 안에 블록 요소 넣기**

HTML 요소는 표시 방법에 따라 인라인 요소, 블록 요소로 나뉜다. 

`인라인 요소`는 콘텐츠가 차지하는 만큼만, `블록 요소`는 가로로 넓게 화면 영역을 차지하며. 대표적인 인라인 요소로는 `<span>`, 블록 요소로는 `<div>` 가 있다.

<strong style="color:#ff6600"> 인라인 요소는 항상 블록 요소 안에 들어가야 하며, 반대의 경우는 있어서는 안 된다. </strong> 

보통 특정 요소가 인라인 요소인지 블록 요소인지 정확하게 알지 못할 때 이런 실수를 하게 된다.

```html
<!-- h1, div 요소는 블록 요소, -->
<!-- a, span 요소는 인라인 요소. -->
<a href=""> <h1>나쁜 예시 1</h1> </a>
<span> <div>나쁜 예시 2</div> </span>
```

<br/>

#### **🔗 `<b>`, `<i>` 요소 사용하기**

`<b>` 요소와 `<i>` 요소는 각각 글씨를 굵게 만들 때, 글씨를 기울일 때 사용하는 요소다. 

하지만 웹 표준을 준수하기 위해서는 이 요소들을 사용하지 않는 것이 좋다. 

시맨틱 하지 않은, 표현을 기준으로 이름이 지어진 요소이기 때문이다. 

대신 똑같은 스타일을 부여하면서 콘텐츠에 의미를 부여하는 <strong style="color:#3366ff">`<strong>` 요소와 `<em>` 요소를 사용 </strong>하는 것이 좋다. 

두 요소는 글씨에 효과를 줌과 동시에 각 콘텐츠를 강조하는 의미를 부여한다.

```html
<b>글씨를 두껍게</b>   -- 대체하기 -->  <strong>콘텐츠 매우 강조하기</strong>
<i>글씨 기울이기</i>   -- 대체하기 -->  <em>콘텐츠 강조하기</em>
```

<br/>

#### **🔗 `<hgroup>` 남용하기**

`<hgroup>` 요소들은 목차의 역할을 하면서 콘텐츠의 상하 관계를 표시하기 위해서 사용한다. 

이를 시각적으로 나타내기 위해서 숫자가 작을수록 글자의 크기가 크고, 숫자가 클수록 크기가 작습니다. 

그런데 이러한 특성 때문에 `<hgroup>` 의 시맨틱 요소로서의 역할을 간과한 채 글자에 스타일 속성을 적용하기 위한 목적으로 사용하는 경우가 종종 있다. 

이럴 경우 화면은 보기 좋을지 몰라도, 사용자에게 완전히 잘못된 화면 구조 정보를 전달하게 된다.

```html
<!-- 나쁜 예시 -->
<h1>엄청 큰 글씨</h1>
    <h3>적당히 큰 글씨</h3>
  <h2>큰 글씨</h2>
          <h6>엄청 작은 글씨</h6>
      <h4>그냥 글씨</h4>

<!-- 좋은 예시 -->
<h1>제목</h1>
  <h2>큰 목차</h2>
    <h3>작은 목차</h3>
    <h3>작은 목차</h3>
  <h2>큰 목차</h2>
    <h3>작은 목차</h3>
      <h4>더 작은 목차</h4>
      <h4>더 작은 목차</h4>
```

<br/>

#### **🔗 `<br />` 연속으로 사용하기**

<br/>

`<br />` 은 쭉 이어지는 텍스트 흐름에 줄 바꿈을 해주기 위해서 사용하는 요소다.

그런데 이러한 목적이 아니라 요소 사이에 간격을 만들기 위한 목적으로 남발해서는 안 된다. 

요소 사이에 간격이 필요한 경우에는 아예 별도의 단락으로 구별하거나 CSS 속성을 주어 여백을 조정해 주는 것이 바람직하다.

```html
<!-- 나쁜 예시 -->
요소 사이에 여백을 주고싶을 때
<br />
<br />
이렇게 하면 안 됨.

<!-- 좋은 예시  1 -->
<p>요소 사이에 여백을 주고싶을 땐</p>
<p>아예 별도의 단락으로 구별해야 함.</p>

<!-- 좋은 예시  2 -->
<!-- HTML 파일 -->
<p class="margin">요소 사이에 여백을 주고싶을 땐</p>
<p class="margin">CSS 속성으로 여백을 설정해야 함.</p>

<!-- CSS 파일 -->
.margin { margin: 10px }

```

<br/>

#### **🔗 인라인 스타일링 사용하기**

<br/>

웹 표준으로 HTML, CSS, JavaScript 등의 사용 방법을 정리하면서 각 영역이 분리되어 여러 이점을 얻을 수 있었다. 

그런데 HTML 요소 안에 인라인으로 스타일링 속성을 설정하는 것은 기껏 분리한 영역을 다시 합치는 것과 같다. 

<strong style="color:#3366ff">웹 표준을 지키기 위해서는, HTML과 CSS 코드를 분리해서 작성</strong>하도록 하자.

```html
<!-- HTML 파일 -->

<head>
  <style>
    h1 { color : "red" }
  </style>
</head>

(O) <h1>스타일링 속성은 CSS로 작성해주세요.<h1>
(O) <h2>style 요소를 사용해도, CSS 파일을 따로 작성해도 괜찮습니다.<h2>
(X) <h3 style="color: blue">이렇게 인라인 스타일링으로는 사용하지 마세요.</h3>  
 
<!-- CSS 파일 -->
h2 { color : "yellow" }
```

<br/>

여기에서 언급된 예시는 웹 표준을 저해하는 마크업 사용법이면서, 생각보다 자주 발생하는 상황이기도 하다. 이런 방법으로 마크업을 사용하지 않도록 주의하도록 하자

<br/>

---

<br/>

## **📚 레퍼런스**

> [ Semantics \| MDN ](https://developer.mozilla.org/ko/docs/Glossary/Semantics){:target="_blank"}
{:.prompt-info}

> [ W3C(World Wide Web Consortium)](https://www.w3.org/){:target="_blank"}
{:.prompt-info}

> [HTML Semantic Elements \| W3C ](https://www.w3schools.com/html/html5_semantic_elements.asp){:target="_blank"}
{:.prompt-info}

> [HTML5의 새로운 시맨틱 태그](https://velog.io/@zlevn/HTML5%EC%9D%98-%EC%83%88%EB%A1%9C%EC%9A%B4-%EC%8B%9C%EB%A7%A8%ED%8B%B1-%ED%83%9C%EA%B7%B8%EC%9D%98%EB%AF%B8-%EC%9A%94%EC%86%8C){:target="_blank"}
{:.prompt-info}

> [ 웹접근성과 웹표준 ](https://seulbinim.github.io/WSA/){:target="_blank"}
{:.prompt-info}

> [ 웹표준과 웹호환성 \| 웹발전연구소 ](http://www.smartebiz.kr/new/subpage02_02.html){:target="_blank"}
{:.prompt-info}

> [[WEB] 🌐 웹 표준 & 웹 접근성 이란? \| Inpa Dev](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-%EC%9B%B9-%ED%91%9C%EC%A4%80%EC%9D%98-%EC%9D%B4%ED%95%B4#%EC%9B%B9_%ED%91%9C%EC%A4%80web_standards){:target="_blank"}
{:.prompt-info}

> [HTML5 개요 웹 표준의 기본 지식](https://jeongchul.tistory.com/453){:target="_blank"}
{:.prompt-info}