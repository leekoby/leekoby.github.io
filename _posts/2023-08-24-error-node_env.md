---
layout: post
title: node_env 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다
author: admin
date: 2023-08-23 21:00:00 +900
lastmod: 2023-08-23 21:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [Error, node]
tags: [error]
---

> 해당 카테고리는 `error`를 만났을때 해결 방법을 기록해두기 위한 것입니다.<br />
{: .prompt-info}

> 구글링, 포럼 등에서 찾은 해결 방법들을 시도하며 해결해가는 과정을 기록하고, 추후 같은 문제가 발생했을 때 참고하고자 기록을 남깁니다.  <br />
{: .prompt-tip}

> 같은 방법을 시도하더라도 해결되지 않을 수 있으니 참고 부탁드립니다.
{: .prompt-warning }

![Node Logo Image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ea0a8852-5354-4475-b67a-0662e72610c0)


<br />

# Error 발생 상황

Jekyll Blog를 만드는 중에 Build 시 아래와 같은 에러메세지가 출력되며 정상적으로 Build 되지 않는 문제가 있었다.


# Error 내용 

>ERROR : 'node_env'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다.<br />
{: .prompt-danger }




<br />

# 해결된 방법


```bash
 npm install -g win-node-env
```