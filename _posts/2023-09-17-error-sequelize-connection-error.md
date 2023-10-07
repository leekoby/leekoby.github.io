---
layout: post
title: ERROR - SequelizeConnectionError Unknown database 'db이름'
author: admin
date: 2023-09-17 09:00:00 +900
lastmod: 2023-09-17 09:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ERROR, MYSQL ERROR]
tags: [error, mysql, node]
---

> **해당 카테고리는 `error`를 만났을때 해결 방법을 기록해두기 위한 것입니다.**<br/>
{: .prompt-info}

> **구글링, 포럼 등에서 찾은 해결 방법들을 시도하며 해결해가는 과정을 기록하고, 추후 같은 문제가 발생했을 때 참고하고자 기록을 남깁니다.**  <br/>
{: .prompt-tip}

> **같은 방법을 시도하더라도 해결되지 않을 수 있으니 참고 부탁드립니다.**
{: .prompt-warning }

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e97d257b-5b36-4091-852e-888297a25480)

<br/>

## **Error 발생 상황**

nodejs `sequelize`로 `MySQL`을 사용하고 있었는데, 시작할 때 본 에러가 발생했다.

## **Error 내용** 

>ConnectionError [SequelizeConnectionError]: Unknown database '데이터베이스이름'<br/>
{: .prompt-danger }

<br/>

# **해결된 방법**

```bash
npx seqeulize db:create
```