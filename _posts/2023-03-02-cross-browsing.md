---
layout: post
title: 크로스 브라우징
author: admin
date: 2023-03-02 00:00:00 +900
lastmod: 2023-03-02 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [WEB]  # 대문자로 작성
tags: [cross browsing] # 소문자로 작성
---

> 기존 블로그에 작성했던 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `크로스브라우징`에 대해 학습한 내용을 정리하며 기록한 내용입니다
{:.prompt-info}

<br/>

---

<br/>

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/926761c7-fce9-4e90-bd39-02f8074912bd)

🌈 **크로스 브라우징(Cross Browsing)**

## **📖 크로스 브라우징(Cross Browsing)이란?**

-	<strong style="color:#3366ff"> 웹 사이트에 접근하는 브라우저의 종류에 상관 없이 `동등`한 화면과 기능을 제공할 수 있도록 만드는 작업을 의미</strong>

  - 여기에서 중요한 포인트는 ‘동일한’이 아니라 <strong style="color:#3366ff"> ’동등한’</strong>이라는 표현 

-	크로스 브라우징은 모든 브라우저에서 완전히 똑같은 화면이 보이도록 만드는 것이 아니다. 

-	브라우저마다 사용하는 렌더링 엔진이 다르기 때문에 화면을 완전히 동일하게 만드는 것은 불가능

-	크로스 브라우징의 목표는 <strong style="color:#3366ff"> 모든 브라우저에서 동등한 수준의 정보와 기능을 제공하는 것</strong>

아마 크로스 브라우징이 되지 않아 불편했던 일을 한 번씩은, 어쩌면 아주 많이 겪었을 수도 있다. 

한국에서 인터넷 익스플로러(Internet Explorer)가 계속해서 브라우저 점유율 1위를 차지하게 만들었던, 1위에서 내려온 뒤에도 계속해서 높은 점유율을 차지하게 만든 원인이기도한 ActiveX 때문이다.

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/9aa289c0-8417-43f6-ba41-a225b0b16739)

세계 브라우저 점유율과 한국 브라우저 점유율을 비교해보면, 지금은 압도적인 점유율을 차지하고 있는 크롬이 인터넷 익스플로러의 점유율을 처음 추월한 시점이 각각 2012년도와 2016년도임을 볼 수 있다. 

한국이 무려 4년이나 뒤쳐져 있는 수치다. 한국의 정보화 수준을 생각했을 때에는 특히 이상한 현상인데, 이는 공공기관을 포함한 한국의 웹 사이트들이 `ActiveX`의 사용을 사용자에게 거의 강제했기 때문이다.

`ActiveX`는 마이크로소프트사에서 개발한 인터넷 익스플로러용 플러그인으로, 웹 브라우저와 웹 문서만으로는 이용할 수 없는 추가 기능들을 사용할 수 있게 해주는 브라우저 외부 프로그램이라고 생각하면 된다.

물론 `ActiveX` 덕분에 웹 브라우저에서 음악, 동영상 등의 멀티미디어 콘텐츠를 자유롭게 즐길 수 있게 되었고, 은행이나 주식과 같은 금융 거래도 가능해졌고, 사용자의 신원을 증명할 수 있게 되어 집에서도 공문서를 출력할 수 있게 되는 등 웹 브라우저의 기능을 거의 무한대로 확장할 수 있게 됐다.

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/59b4740d-07de-4790-857d-f9598e4ca6a2)

하지만 문제점은 정말 무한대로 확장하면서 뭔가 작업 하려고 하면 `ActiveX`를 설치하라는 문구가 뜨고, 필요한 `ActiveX`가 하나라도 설치되어 있지 않으면 기능이 제대로 작동되지 않는 등 배보다 배꼽이 커지는 현상이 발생했으며, 인터넷 익스플로러에서만 사용 가능했기 때문에 다른 브라우저를 사용하는 일은 거의 불가능했다. <strong style="color:tomato"> `ActiveX` 때문에 크로스 브라우징이 불가능한 상황</strong>이 된 것이다.

그런데 `ActiveX`만이 인터넷 익스플로러가 크로스 브라우징에 악영향을 줬던 요소는 아니다. 인터넷 익스플로러는 최신 스펙의 CSS와 자바스크립트를 제대로 지원하지 못했고, 이 때문에 크로스 브라우징을 위해서 웹 표준을 저해하는 코드를 작성해야만 했다. 다른 웹 브라우저들은 최신 문법을 지원하는데, 오롯이 인터넷 익스플로러만을 위해서 크로스 브라우징에 시간과 비용을 투자해야 했다. 이런 여러 이유 때문에 인터넷 익스플로러는 웹 개발자 사이에서 ‘`공공의 적`‘이라고 까지 불리게 되었다고 한다.

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/90377ea1-3ccb-4564-a5a1-8075e706954e)

이렇게 말도 많고 탈도 많던 인터넷 익스플로러는 2022년 6월 15일부로 지원을 종료하여 역사의 뒤안길로 사라지게 되었다. 크로스 브라우징의 가장 큰 걸림돌이었던 브라우저가 은퇴하게 된 것이다. 물론 마이크로소프트사의 새로운 웹 브라우저인 엣지(Edge)에서 아직 ActiveX를 사용하는 웹 사이트들을 위해서 인터넷 익스플로러 모드를 최소 2029년까지 제공할 예정이라고 밝혔지만, 이제 크로스 브라우징을 할 때 인터넷 익스플로러 때문에 머리를 싸매야하는 일은 거의 없을 것이다.

그럼 인터넷 익스플로러의 퇴출로 난이도가 한결 낮아진 크로스 브라우징은 어떻게 진행하면 되는지 알아보자.

<br/>

---

<br/>

## **📖 크로스 브라우징 워크 플로우**

### **1. 초기 기획**

-	초기 기획 단계에서 어떤 웹 사이트를 만들 것인지 정확하게 결정

	-	어떤 콘텐츠와 기능이 있어야 하는지, 디자인은 어떻게 할지 등의 사항을 결정

-	이 사이트의 고객이 누구일지 생각 

	-	고객이 사용하는 브라우저는 무엇일지, 기기는 무엇일지 고민이 필요 

-	타겟 고객층이 주로 사용하게 될 브라우저와 기기를 파악했다면, 여기에 맞는 기술을 사용해서 개발할 수 있도록 기획

<br/>

### **2. 개발**

-	코드를 작성할 때 사용하는 코드가 각 브라우저에서의 호환성이 어떤지 파악하고 사용 


	-	[MDN](https://developer.mozilla.org/ko/){:target="_blank"}, [Can I Use](https://caniuse.com/){:target="_blank"}에서 코드의 호환성을 확인 가능 

-	아래 예시는 MDN에서 제공하는 브라우저 호환성 정보

  - 인터넷 익스플로러에서의 호환성이 좋지 않은 것을 확인할 수 있다. 

	-	만약 주요 고객층의 인터넷 익스플로러 사용 비율이 높다면 해당 코드는 사용하지 않는 것이 좋다.
    
    ![](https://github.com/leekoby/leekoby.github.io/assets/118284808/c81a1a15-e424-4ce6-add9-d9c80942dc2f)

코드를 작성하다보면 크로스 브라우징을 힘든 상황을 만나게 될 수도 있는데, 이런 경우에는 이를 인정하고 대체 수단을 마련한다. 또한, 개발중인 웹 사이트가 일부 오래된 브라우저에서는 어쩔 수 없이 제대로 기능하지 않을 수도 있다는 사실을 알고 있는게 좋다.


### **3. 테스트 / 발견**


각 기능을 구현한 후에는 그 기능에 대한 테스트가 필요

> -	안정적인 데스크톱 브라우저(크롬, 엣지, 파이어폭스, 오페라, 사파리 등)에서 테스트를 진행
>
> -	휴대폰 및 태플릿 브라우저(삼성 인터넷, 사파리, 안드로이드 기기의 크롬 등)에서 테스트를 진행
> 
> -	그 외에도 초기 기획 단계에서 목표했던 브라우저가 있다면 해당 브라우저에서 테스트를 진행
>
> -	Window, Linux, Mac 등 다양한 운영 체제에서도 테스트를 진행

직접 테스트를 수행할 수도 있지만, 자동으로 테스트를 진행해주는 도구를 이용하는 것도 방법 

TestComplete, LambdaTest, BitBar 등의 크로스 브라우징 테스트 툴이 있다.

### **4. 수정 / 반복**

테스트 단계에서 버그가 발견되었다면 수정이 필요하다. 버그가 발생하는 위치를 최대한 좁혀서 특정하고, 버그가 발생하는 특정 브라우저에서의 해결 방법을 정해야 한다. 섣불리 코드를 수정했다가는 다른 브라우저에서 버그가 발생할 수 있으므로, 조건문을 작성해 다른 코드를 실행하게 하는 방식으로 고쳐나가는 것이 좋다. 수정이 완료되면 3번 과정부터 반복한다.

<br/>

---

<br/>

웹 페이지를 개발할 때 웹 표준만 잘 지켜도 크로스 브라우징을 어느 정도 실현할 수 있다고 한다. 웹 표준 자체가 어떠한 운영체제나 브라우저를 사용하더라도 웹페이지가 동일하게 보이고 정상적으로 작동할 수 있도록 하는 웹 페이지 제작 기법이기 때문이다. 특히 웹 표준을 오히려 저해하는 코드를 작성해야했던 인터넷 익스플로러가 지원을 종료하게 되었기 때문에 크로스 브라우징에 힘써야하는 상황이 많이 줄어들었다. 따라서 당장은 브라우저에 따라 제대로 표시되지 않는 화면, 작동하지 않는 기능이 있을 수 있음을 인지하고 개선하려는 태도를 가지고, 웹 표준에 맞는 코드를 작성하도록 노력해야 한다고 생각한다.

<br/>

---

<br/>

## **📚 레퍼런스**

> [Cross Browsing 가이드](http://www.mozilla.or.kr/docs/web-developer/standard/crossbrowsing.pdf){:target="_blank"}
{:.prompt-info}

> [MDN](https://developer.mozilla.org/ko/){:target="_blank"}
{:.prompt-info}

> [Can I Use](https://caniuse.com/){:target="_blank"}
{:.prompt-info}

> [W3C(World Wide Web Consortium) ](https://www.w3.org/){:target="_blank"}
{:.prompt-info}

> [Cross Browsing](https://pxd-fed-blog.web.app/cross-browsing/){:target="_blank"}
{:.prompt-info}

> [크로스브라우징 이슈에 대처하는 자세](https://kwangjae.com/1014){:target="_blank"}
{:.prompt-info}

> [크로스브라우징](https://okayoon.tistory.com/entry/%ED%81%AC%EB%A1%9C%EC%8A%A4-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A7%95cross-browsing){:target="_blank"}
{:.prompt-info}

