---
layout: post
title: ERROR-0308010C-digital envelope routines--unsupported
author: admin
date: 2023-03-02 00:00:00 +900
lastmod: 2023-03-02 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [ERROR, NODE ERROR]
tags: [error, node]
---

> **해당 카테고리는 `error`를 만났을때 해결 방법을 기록해두기 위한 것입니다.**<br/>
{: .prompt-info}

> **구글링, 포럼 등에서 찾은 해결 방법들을 시도하며 해결해가는 과정을 기록하고, 추후 같은 문제가 발생했을 때 참고하고자 기록을 남깁니다.**  <br/>
{: .prompt-tip}

> **같은 방법을 시도하더라도 해결되지 않을 수 있으니 참고 부탁드립니다.**
{: .prompt-warning }

# **💢 error:0308010C:digital envelope routines::unsupported**

이 오류는 기본적으로 OpenSSL 버전 3을 지원하기 시작한 Node.js 버전 17에 주요 변경 사항이 있기 때문에 발생하는 것으로 보입니다. 

이 오류가 발생하는 이유 

Webpack과 같은 명령줄 도구는 "MD4 알고리즘"을 사용하여 파일 해시를 생성합니다. 이러한 파일 해시는 JavaScript 파일의 변경 사항을 추적하는 데 사용됩니다. 

기본적으로 OpenSSL 버전 3은 MD4 알고리즘을 활성화하지 않았습니다. Node.js를 버전 17 이상으로 업그레이드한 경우 Webpack을 사용하여 애플리케이션을 빌드할 때 이 오류가 표시됩니다. 

React, Vue, Angular 또는 기타 애플리케이션을 실행하는지 여부는 중요하지 않습니다. Webpack 빌드가 실패했기 때문에 이 오류가 발생하는 것으로 보입니다. 

단순히 Webpack5로 업그레이드하거나 빌드 스크립트를 수정하는 것으로 오류가 다시 나타나지 않을 수도 있고, 위의 방법으로 해결되지 않을 경우 Node version을 다운그레이드해야 할 수도 있습니다. 자세한 내용은 레퍼런스를 참고하면 도움을 받을 수 있습니다. 

## **요약** 

"0308010 c:digital envelope routines::unsupported" 오류는 Webpack이 최신 Node.js 버전에서 더 이상 지원되지 않는 MD4 알고리즘을 빌드 프로세스에 사용했기 때문에 나타납니다. 이 문제를 해결하려면 

- `Webpack5` 업그레이드, 

- NODE_OPTIONS 환경 변수를 `--openssl-legacy-provider`로 설정하거나 --openssl-legacy-provider 플래그를 webpack에 전달합니다. 

- 설치된 node.js 버전을 16 이하로 다운그레이드하는 것입니다.

## **📚 레퍼런스**

> [Node error:0308010C:digital envelope routines::unsupported](https://sebhastian.com/error-0308010c-digital-envelope-routines-unsupported/){:target="_blank"}
{:.prompt-info}

> [How to Fix error:0308010C:digital envelope routines::unsupported](https://www.datainfinities.com/49/error-0308010C-digital-envelope-routines-unsupported){:target="_blank"}
{:.prompt-info}
