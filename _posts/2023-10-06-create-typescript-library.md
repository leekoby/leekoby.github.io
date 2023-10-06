---
layout: post
title: 직접 타입스크립트 라이브러리 만들기
author: admin
date: 2023-10-06 09:00:00 +900
lastmod: 2023-10-06 09:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [TYPESCRIPT, TYPESCRIPT + NODEJS] # 대문자로 작성
tags: [typescript, nodejs, typescript + nodejs] # 소문자로 작성
---

> 해당 포스트는 [Node.js에 TypeScript 적용하기(feat. NodeBird)](https://www.inflearn.com/course/nodejs-typescript-%EC%A0%81%EC%9A%A9/dashboard){:target="_blank"} 강의로 `typescript + nodejs`의 내용을 복습하며 필요한 내용을 정리한 포스트입니다.
{:.prompt-info} 

<br/>

---

<br/>

# **🌈 직접 타입스크립트 라이브러리 만들기**

## **💻 axios 만들기**

axios 폴더 생성 후 `npm init`

```sh
npm init
npm i typescript
```

node환경에서도 axios사용이 가능하지만 이번에는 브라우저 환경용만 만들어본다.

🔻 pakagea.json

```js
{
  "name": "axios",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "leekoby",
  "license": "ISC",
  "dependencies": {
    "typescript": "^5.2.2"
  },
  "types": "./index.d.ts"
}

```

🔻 tsconfig.json

```js
{
  "compilerOptions": {
    "strict": true,
    "declaration": true,
    "lib": ["DOM", "ES2020"]
  }
}
```

🔻 index.ts
```ts
export interface AxiosConfig {
  headers?: {
    [key: string]: string;
  };
  withCredentials?: boolean;
  //쿠키 전달할 때 써야함
}

export interface AxiosData {
  [key: string]: any;
}

export interface AxiosResult {
  data: any;
  status: number;
  statusText: string;
}

//타입가드
function isAxiosData(data: any): data is AxiosData {
  //데이터 is 리턴타입
  if (data !== null) return false;
  if (data instanceof FormData) return false;
  return typeof data === 'object';
}

export interface Axios {
  //data, config는 optional
  defaults: {
    baseUrl: string;
    headers: {
      [key: string]: string;
    };
  };
  get(url: string, config?: AxiosConfig): Promise<AxiosResult>;

  put(
    url: string,
    data?: string | AxiosData | FormData,
    config?: AxiosConfig
  ): Promise<AxiosResult>;

  patch(
    url: string,
    data?: string | AxiosData | FormData,
    config?: AxiosConfig
  ): Promise<AxiosResult>;

  post(
    url: string,
    data?: string | AxiosData | FormData,
    config?: AxiosConfig
  ): Promise<AxiosResult>;

  delete(url: string, config?: AxiosConfig): Promise<AxiosResult>;

  options(url: string, config?: AxiosConfig): Promise<AxiosResult>;

  head(url: string, config?: AxiosConfig): Promise<AxiosResult>;
}

const axios: Axios = {
  defaults: {
    baseUrl: '',
    headers: {},
  },
  get(url, config) {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        } else {
          reject({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        }
      };
      xhr.onerror = function () {
        reject({
          data: xhr.responseText,
          status: xhr.status,
          statusText: xhr.statusText,
        });
      };
      xhr.open('GET', axios.defaults.baseUrl + url);
      const headers: { [key: string]: any } = { ...axios.defaults.headers, ...config?.headers };
      Object.keys(headers).map((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
      xhr.withCredentials = config?.withCredentials || false;
      xhr.send();
    });
  },
  put(url, data, config) {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        } else {
          reject({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        }
      };
      xhr.onerror = function () {
        reject({
          data: xhr.responseText,
          status: xhr.status,
          statusText: xhr.statusText,
        });
      };
      xhr.open('PUT', axios.defaults.baseUrl + url);
      const headers: { [key: string]: any } = { ...axios.defaults.headers, ...config?.headers };
      Object.keys(headers).map((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
      xhr.withCredentials = config?.withCredentials || false;
      if (isAxiosData(data)) {
        xhr.send(JSON.stringify(data));
      } else {
        xhr.send(data);
      }
    });
  },
  patch(url, data, config) {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        } else {
          reject({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        }
      };
      xhr.onerror = function () {
        reject({
          data: xhr.responseText,
          status: xhr.status,
          statusText: xhr.statusText,
        });
      };
      xhr.open('PATCH', axios.defaults.baseUrl + url);
      const headers: { [key: string]: any } = { ...axios.defaults.headers, ...config?.headers };
      Object.keys(headers).map((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
      xhr.withCredentials = config?.withCredentials || false;
      if (isAxiosData(data)) {
        xhr.send(JSON.stringify(data));
      } else {
        xhr.send(data);
      }
    });
  },
  post(url, data, config) {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        } else {
          reject({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        }
      };
      xhr.onerror = function () {
        reject({
          data: xhr.responseText,
          status: xhr.status,
          statusText: xhr.statusText,
        });
      };
      xhr.open('POST', axios.defaults.baseUrl + url);
      const headers: { [key: string]: any } = { ...axios.defaults.headers, ...config?.headers };
      Object.keys(headers).map((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
      xhr.withCredentials = config?.withCredentials || false;
      if (isAxiosData(data)) {
        xhr.send(JSON.stringify(data));
      } else {
        xhr.send(data);
      }
    });
  },
  delete(url, config) {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        } else {
          reject({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        }
      };
      xhr.onerror = function () {
        reject({
          data: xhr.responseText,
          status: xhr.status,
          statusText: xhr.statusText,
        });
      };
      xhr.open('DELETE', axios.defaults.baseUrl + url);
      const headers: { [key: string]: string } = { ...axios.defaults.headers, ...config?.headers };
      Object.keys(headers).map((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
      xhr.withCredentials = config?.withCredentials || false;
      xhr.send();
    });
  },
  options(url, config) {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        } else {
          reject({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        }
      };
      xhr.onerror = function () {
        reject({
          data: xhr.responseText,
          status: xhr.status,
          statusText: xhr.statusText,
        });
      };
      xhr.open('OPTIONS', axios.defaults.baseUrl + url);
      const headers: { [key: string]: any } = { ...axios.defaults.headers, ...config?.headers };
      Object.keys(headers).map((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
      xhr.withCredentials = config?.withCredentials || false;
      xhr.send();
    });
  },
  head(url, config) {
    return new Promise((resolve, reject) => {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        } else {
          reject({
            data: xhr.responseText,
            status: xhr.status,
            statusText: xhr.statusText,
          });
        }
      };
      xhr.onerror = function () {
        reject({
          data: xhr.responseText,
          status: xhr.status,
          statusText: xhr.statusText,
        });
      };
      xhr.open('HEAD', axios.defaults.baseUrl + url);
      const headers: { [key: string]: any } = { ...axios.defaults.headers, ...config?.headers };
      Object.keys(headers).map((key) => {
        xhr.setRequestHeader(key, headers[key]);
      });
      xhr.withCredentials = config?.withCredentials || false;
      xhr.send();
    });
  },
};

export default axios;

```

<br/>

---

<br/>

## **💻 라이브러리 빌드하기**

브라우저에서 동작하게 작성했기 때문에 실행해보려고 하면 에러가 발생한다.

일반적인 상황은 아니지만 이를 해결하기 위해서 `Webpack`과 `Babel`을 사용한다.

강의에서는 `awesome-typescript-loader`를 사용한다. 하지만 이 패키지의 경우 4버전 이상을 지원하지 않는 것으로 보인다. 

현재 설치한 타입스크립트 버전이 5.2.2 버전이므로 `ts-loader` 패키지를 설치했다.

```sh
npm i webpack webpack-cli ts-loader
npm i @babel/preset-env @babel/core
npm i babel-plugin-add-module-exports
```

🔻 webpack.config.js

```js
const path = require('path');

module.exports = {
  mode: 'development',
  entry: {
    index: './index.ts',
  },
  module: {
    rules: [
      {
        test: /\.ts$/,
        loader: 'ts-loader',
        exclude: /node_modules/,
      },
      {
        test: /\.js$/,
        use:{
          loader:'babel-loader',
          options:{
            presets:['@babel/preset-env']
          }
        },
        exclude:/node_modules/
      }
    ],
  },
  output: {
    path: path.join(__dirname),
    filename: '[name].js',
    libraryTarget:'umd'
  },
  resolve:{
     extensions:['.ts','.js']
   }
};

```

`ts-loader` 를 사용해서 강의와는 조금 다르게 설정해줬다.

원하는대로 올바르게 작동할 경우 

`npm publish`를 통해 배포할 수 있다. (npmjs에 가입되어있어야함.)

<br/>

---

<br/>

## **📚 레퍼런스**

> [Node.js에 TypeScript 적용하기(feat. NodeBird)](https://www.inflearn.com/course/nodejs-typescript-%EC%A0%81%EC%9A%A9/dashboard){:target="_blank"}
{:.prompt-info}
