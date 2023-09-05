---
layout: post
title: 계층형 조회 (Connect By)
author: admin
date: 2023-08-29 04:00:00 +900
lastmod: 2023-08-29 04:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [CERTIFICATION, SQLD]  # 대문자로 작성
tags: [sql, sqld, connect by] # 소문자로 작성
---

> 해당 포스트는 `SQLD`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br />

> 이론을 학습한 내용을 정리하고 퀴즈, 기출문제 등을 기록하려고 합니다.
{:.prompt-tip}

<br />

# **🌈 계층형 조회**

## **💻 계층형 조회**

**<span style="color:#ff6600">­계층형 조회는 Oracle 데이터베이스에서 지원하는 것으로 계층형으로 데이터를 조회</span>**할 수 있다.

예를 들어 부장에서 차장, 차장에서 과장, 과장에서 대리, 대리에서 사원 순으로 트리 형태의 구조를 위에서 아래로 탐색하면서 조회하는 것이다. 

물론 역방향 조회도 가능하다.

> **<span style="color:#3366ff">ORDER SIBLINGS BY</span>를 사용하여 <span style="color:#ff6600">전체 테이블을 대상으로 하는 것이 아닌 특정 컬럼을 기준으로 정렬을 수행할 수 있다.</span>**
{:.prompt-tip}

**계층형 조회 테스트 데이터 입력**

![계층형 조회](https://github.com/leekoby/leekoby.github.io/assets/118284808/8d902099-f3c4-4d89-8d88-f9ba278f66d2){: width="500" height="500" }

![계층형 조회](https://github.com/leekoby/leekoby.github.io/assets/118284808/768110ea-36f8-44fa-9b98-1e70ecd4b056){: width="500" height="500" }

- PRIOR 자식 = 부모 형태를 사용하면 계층 구조에서 **<span style="color:#ff6600">순방향 전개</span>**를 수행한다.

- **<span style="color:#ff6600">Connect by는 트리 형태의 구조로 질의를 수행하는 것으로 `START WITH구는 시작 조건`을 의미하고 `CONNECT BY PRIOR는 조인 조건`이다. </span>**

- **<span style="color:#ff6600">Root 노드로부터 하위 노드의 질의를 실행한다.</span>**

- **<span style="color:#ff6600">계층형 조회에서 MAX(LEVEL)을 사용하여 최대 계층 수를 구할 수 있다.</span>**

-  즉, 계층형 구조에서 마지막 Leaf Node의 계층값을 구한다.

![계층형 조회](https://github.com/leekoby/leekoby.github.io/assets/118284808/97a06220-d867-4715-8709-840723962440){: width="500" height="500" }

- 위의 SQU게서 MAX(LEVEL)이 4이므로 **<span style="color:#ff6600">트리의 최대 깊이는 4</span>**이다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9b0f734c-e192-4664-ac8b-ffaf040e82ed){: width="500" height="500" }

- 위의 예에서 EMPNO와 MGR 칼럼 모두 사원번호가 입력되어 있다.

- 하지만 MGR은 관리자 사원번호를 가지고 있다. 

- 즉, MGR 1000번은 1001과 1002의 사원을 관리한다.

- **<span style="color:#ff6600">계층형 조회 결과를 명확히 보기 위해서 LPAD 함수를 사용할 수 있다.</span>**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9b6f74d4-5d49-4825-9583-d21960262a2c){: width="500" height="500" }

>**<span style="color:#ff6600">CONNECT_BY_ISLEAF</span>**
> - 계층형 쿼리에서 해당하는 로우가 자식노드가 있는지 없는지 여부를 체크
> - 자식 노드가 있는 경우 0, 자식 노드가 없을 경우 1
> - 해당 데이터가 리프 데이터면 1, 그렇지 않으면 0 

>**<span style="color:#ff6600">CONNECT_BY_ROOT</span>**
> - 계층형 쿼리에서 최상위 노드를 찾고자 할 경우 사용

>**오라클 LPAD 함수**
> - 좌측에 자리수 만큼 채워주는 함수 
> - LPAD (변수, 길이, 변형자)

- 위의 예를 보면 4*LEVEL - 1이 있다, LEVEL 값이 Root이면 1이 된다. 따라서 4(1—1)=0이
된다.

- 즉, Root일 때는 LPAD(’ ’, 0)이므로 아무런 의미가 없다.

- 하지만, LEVEL 값이 2가 되면 4 * (2 - 1) = 4가 된다. LPAD(’ ’, 4)이므로 왼쪽 공백 4칸을 화면
에 찍는다.

- 결과적으로 LPAD는 트리 형태로 보기 위해서 사용한 것이다. 

- 왜냐하면 LEVEL 값은 Root에 1을 되돌리고 그다음 자식은 2, 그리고 그다음 자식은 3이 나오기 때문이다.

<br/>

**CONNECT BY 키워드**

|키워드	| 설명|
|:-|:-|
|LEVEL|	- 검색 항목의 깊이를 의미한다.<br/> - 즉, 계층구조에서 가장 상위 레벨이 1이 된다.|
|CONNECT_BY_ROOT|	- 계층구조에서 가장 최상위 값을 표시한다.|
|CONNECT_BY_ISLEAF| -	계층구조에서 가장 최하위 값을 표시한다.|
|SYS_CONNECT_BY_PATH	|- 계층구조에서 전체 전개 경로를 표시한다.|
|NOCYCLE|	- 순환구조가 발생지점까지만 전개된다.<br/> - 중복이 생겼을 때 루프를 돌지 않기 위해 사용|
|CONNECT_BY_ISCYCLE|-	순환구조 발생 지점을 표시한다.|

<br/>

## 🎯 **추가 내용**

### ❌

#### 👉

<br/>

## 📚 **레퍼런스**

> 정미나. (유튜브 선생님에게 배우는) 유선배 SQL개발자(SQLD) 과외노트 / [정미나 저] (2023). Print.
> {:.prompt-info }

> 한국데이터산업진흥원. SQL 자격검정 실전문제 : 국가공인 SQL전문가·국가공인 SQL개발자 / 한국데이터산업진흥원 [편] (2020). Print.
> {:.prompt-info }

> [SQL 개발자 (SQLD) 자격증 따기 Part.2 / [유용한IT학습] ](https://www.inflearn.com/course/sqld-%EC%9E%90%EA%B2%A9%EC%A6%9D-%EC%B4%88%EA%B8%89-2/dashboard){:target="\_blank"}
> {:.prompt-info }
