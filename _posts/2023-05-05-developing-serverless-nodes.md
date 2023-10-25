---
layout: post
title: 16. 서버리스 노드 개발
author: admin
date: 2023-05-05 00:00:00 +900
lastmod: 2023-05-05 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [STUDY, NODEJS교과서] # 대문자로 작성
tags: [node, nodejs, 노드교과서] # 소문자로 작성
---

> 기존 블로그에 작성했던 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `NODEJS`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---

<br/>

# **🌈 서버리스 노드 개발**

## **💻 서버리스 이해하기**

서버리스(serverless, server+less)

서버가 없다는 뜻이지만 서버가 없는 것은 아니고, 서버를 직접 운영하지 않아도 된다는 뜻

개발자는 자신의 서비스 로직 작성에만 집중할 수 있음

단순히 코드를 업로드한 뒤, 사용량에 따라 요금을 지불하면 됨(함수처럼 호출할 때만 실행됨, FaaS(Function as a Service))

24시간 작동할 필요가 없는 서버인 경우, 서버리스 컴퓨팅을 사용하면 요금 절약

AWS는 Lambda, GCP에서는 Cloud Functions나 Firebase가 유명함

이를 활용해 NodeBird에서 업로드하는 이미지를 리사이징 및 저장할 것임

<br/>

---

<br/>

## **💻 AWS S3 사용하기**

### **🍳 AWS S3 사용해보기**

스토리지 섹션의 S3를 선택

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/82816eae-bce2-4a98-a37d-eb92b2df4f67)


### **🍳 버킷 만들기**

버킷 만들기나 시작하기 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/30ea1eb3-4a9c-4c97-a97c-9a4c39a3b8ad)

<br/>

### **🍳 버킷 리전 설정하기**

버킷 이름은 고유해야 하므로 고유한 이름을 사용할 것

- 이름만 정하고 계속 다음 버튼을 눌러 넘어감

- 권한에서 모든 퍼블릭 액세스 차단 체크박스 해제(실무에서는 해제하지 않는 것이 좋음)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/751e48f3-c12e-4cbe-9986-b36a516c11a1)


<br/>

### **🍳 버킷 생성 확인하기**

화면이 뜨면 nodebird 버킷 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/28cacca0-2574-434f-b138-7d88f97d7e76)

<br/>

### **🍳 버킷 정책 수정하기**

권한 – 버킷 정책 메뉴 선택

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/90940c4b-7d8b-429d-a964-ff1448919be5)

- 다음 코드를 입력 후 저장

```js
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "AddPerm",
			"Effect": "Allow",
			"Principal": "*",
			"Action": [
			    "s3:GetObject",
			    "s3:PutObject"
			    ],
			"Resource": "arn:aws:s3:::버킷명/*"
		}
	]
}
```

`s3:GetObject`는 S3로부터 데이터를 가져오는 권한
`s3:PutObject`는 S3에 데이터를 넣는 권한



![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6b0e7d6b-5344-4cf4-b6aa-12372dc604f9)

<br/>

### **🍳 액세스 키 발급받기**

상단 메뉴에서 계정 이름 클릭 후 내 보안 자격 증명 메뉴 선택

- 보안 자격 증명으로 계속 버튼 클릭

- 실 서비스에서는 IAM 사용자 시작하기 버튼 누를것

액세스 키 만들기 버튼 클릭

보안 액세스 키는 다시 볼 수 없으므로 키 파일 다운로드 버튼 눌러 저장

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/0aa167f9-5265-4e4a-a098-ee7527458cf3)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/998af48b-3c89-4b22-983c-88d4da4fd7ac)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a53e943c-92c1-43b5-a315-d74b1cfddf42)

<br/>

### **🍳 aws-sdk로 S3 도입하기**

multer-s3와 aws-sdk 패키지를 설치한 후 .env에 발급받은 보안 키 기입
```sh
npm i multer-s3 aws-sdk
```
🔻.env

```js
COOKIE_SECRET=nodebirdsecret
KAKAO_ID=5d4daf57becfd72fd9c919882552c4a6
SEQUELIZE_PASSWORD=nodejsbook
REDIS_HOST=redis-18954.c92.us-east-1-3.ec2.cloud.redislabs.com
REDIS_PORT=18954
REDIS_PASSWORD=JwTwGgKM4P0OFGStgQDgy2AcXvZjX4dc
S3_ACCESS_KEY_ID=AKIAID6RLNYHFCZEEODA
S3_SECRET_ACCESS_KEY=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
```
<br/>

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/0dd6127e-f203-4dba-89d4-855beb422895)

AWS.config.update로 AWS에 관한 설정을 함(ap-northeast-2는 서울 리전)

multer를 multerS3로 교체함(버킷은 여러분의 버킷명을 사용할 것)

req.file.location에 S3 버킷 이미지 주소가 담겨 있음

🔻 nodebird/routes/post.js

```js
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');
const { S3Client } = require('@aws-sdk/client-s3');
const multerS3 = require('multer-s3');

const { afterUploadImage, uploadPost } = require('../controllers/post');
const { isLoggedIn } = require('../middlewares');

const router = express.Router();

try {
  fs.readdirSync('uploads');
} catch (error) {
  console.error('uploads 폴더가 없어 uploads 폴더를 생성합니다.');
  fs.mkdirSync('uploads');
}

const s3 = new S3Client({
  credentials: {
    accessKeyId: process.env.S3_ACCESS_KEY_ID,
    secretAccessKey: process.env.S3_SECRET_ACCESS_KEY,
  },
  region: 'ap-northeast-2',
});
const upload = multer({
  storage: multerS3({
    s3,
    bucket: 'nodebird03',
    key(req, file, cb) {
      cb(null, `original/${Date.now()}_${file.originalname}`);
    },
  }),
  limits: { fileSize: 5 * 1024 * 1024 },
});

// POST /post/img
router.post('/img', isLoggedIn, upload.single('img'), afterUploadImage);

// POST /post
const upload2 = multer();
router.post('/', isLoggedIn, upload2.none(), uploadPost);

module.exports = router;
```

🔻 nodebird/controllers/post.js

```js
const { Post, Hashtag } = require('../models');

exports.afterUploadImage = (req, res) => {
  console.log(req.file);
  res.json({ url: req.file.location });
};

exports.uploadPost = async (req, res, next) => {
  try {
    const post = await Post.create({
      content: req.body.content,
      img: req.body.url,
      UserId: req.user.id,
    });
    const hashtags = req.body.content.match(/#[^\s#]*/g);
    if (hashtags) {
      const result = await Promise.all(
        hashtags.map(tag => {
          return Hashtag.findOrCreate({
            where: { title: tag.slice(1).toLowerCase() },
          })
        }),
      );
      await post.addHashtags(result.map(r => r[0]));
    }
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

### **🍳 이미지 업로드 시도하기**

[http://localhost:8001](http://localhost:8001){:target="_blank"}에 접속하여 로그인 후 이미지 업로드

S3 버킷에 이미지가 업로드된 것 확인

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/bbb0174c-4e6e-4fc3-bf92-5a90f5ef44f3)

<br/>

---

<br/>

## **💻 AWS LAMBDA 사용하기**

### **🍳 이미지 리사이징을 위해 람다 사용**

이미지 리사이징은 CPU를 많이 사용하기 때문에 기존 서버로 작업하면 무리가 감

Lambda라는 기능을 사용해 필요할 때만 서버를 실행해서 리사이징

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/558a0c32-f215-4871-b2b3-88263d16ea08)

<br/>

### **🍳 람다용 package.json 작성하기**

aws-upload 폴더 만든 후 package.json 작성

Lambda라는 기능을 사용해 필요할 때만 서버를 실행해서 리사이징
🔻aws-upload/package.json

```js
{
  "name": "aws-upload",
  "version": "0.0.1",
  "description": "Lambda 이미지 리사이징",
  "main": "index.js",
  "author": "leekoby",
  "license": "ISC",
  "dependencies": {
    "aws-sdk": "^2.634.0",
    "sharp": "^0.25.1"
  }
}
```
🔻 aws-upload/.gitignore
```js
node_modules
```

<br/>

### **🍳 sharp로 리사이징하기**

Sharp는 이미지 리사이징을 위한 라이브러리


- exports.handler가 람다 실행 부분

- event에 버킷과 데이터 정보가 들어 있음

- s3.getObject로 이미지를 버킷에서 가져옴

- sharp로 리사이징

- resize(가로, 세로, 모드), 모드는 inside(비율 유지하면서 꽉 차게)

- toFormat으로 확장자 지정, toBuffer로 버퍼로 변환

- S3.putObject로 버킷에 이미지 데이터 저장

- callback으로 람다 종료 및 응답 데이터 전달

🔻aws-upload/index.js

```js
const sharp = require('sharp');
const { S3Client, GetObjectCommand, PutObjectCommand } = require('@aws-sdk/client-s3');

const s3 = new S3Client();

// 고양이.png
// %CD%AE%AW.png
exports.handler = async (event, context, callback) => {
    const Bucket = event.Records[0].s3.bucket.name;
    const Key = decodeURIComponent(event.Records[0].s3.object.key); // original/고양이.png
    const filename = Key.split('/').at(-1);
    const ext = Key.split('.').at(-1).toLowerCase();
    const requiredFormat = ext === 'jpg' ? 'jpeg' : ext;
    // sharp에서는 jpg 대신 jpeg 사용합니다.
    console.log('name', filename, 'ext', ext);

    try {
        const getObject = await s3.send(new GetObjectCommand({ Bucket, Key }));
        const buffers = [];
        for await (const data of getObject.Body) {
            buffers.push(data);
        }
        const imageBuffer = Buffer.concat(buffers); // 버퍼로 가져오기
        console.log('put', imageBuffer.length);
        const resizedImage = await sharp(imageBuffer) // 리사이징
            .resize(200, 200, { fit: 'inside' })
            .toFormat(requiredFormat)
            .toBuffer();
        await s3.send(new PutObjectCommand({
            Bucket,
            Key: `thumb/${filename}`, // thumb/고양이.png
            Body: resizedImage,
        }))
        console.log('put', resizedImage.length);
        return callback(null, `thumb/${filename}`);
    } catch (error) {
        console.error(error);
        return callback(error);
    }
}
```
<br/>

### **🍳 코드 깃허브로 전송하기**

먼저 GitHub에 aws-upload 리파지토리로 올린 후 Lightsail 인스턴스에서 클론

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/b4f216dc-5efc-48cf-b0e6-28c797392e1d)

압축 후 S3로 업로드

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7f511bc1-2ab5-401f-937a-c9314aa8aabc)

<br/>

### **🍳 람다 서비스 설정하기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/dade6562-1ce8-49b7-8a05-062b13abe23c)

<br/>

### **🍳 새 함수 만들기**

함수 생성 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/02408863-eafd-41fc-8ade-58d09a186462)

함수명은 node-deploy로, 런타임은 Node.js 18.x으로

역할은 템플릿에서 새 역할 생성 선택, S3 객체 읽기 전용 권한 부여

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1eefc32e-268c-4697-b136-855c8ecf5052)

<br/>

### **🍳 zip 파일 업로드하기**

함수 코드 섹션에서 S3에 올린 파일을 선택

`https://버킷명.s3.지역명.amazonaws.com/파일명`

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/33ab1bd7-a89e-4b49-84b4-da5c5ad6f318)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/d60c5972-ea64-4efd-9385-f6333e869814)

<br/>

### **🍳 트리거 설정하기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/92ea4db2-3453-470f-9b7b-4e9ec9dd4d85)

모든 객체 생성 이벤트를 선택하고 접두사에 original/ 누른 후 저장

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c5c63135-e406-4463-b34c-3e0f9dcc6b22)

<br/>

### **🍳 NodeBird에 람다 연결하기**

기존 original 폴더 부분을 thumb(리사이징 됨) 폴더로 교체

🔻 nodebird/controllers/post.js

```js
const { Post, Hashtag } = require('../models');

exports.afterUploadImage = (req, res) => {
  console.log(req.file);
  const originalUrl = req.file.location;
  const url = originalUrl.replace(/\/original\//, '/thumb/');
  res.json({ url, originalUrl });
};

exports.uploadPost = async (req, res, next) => {
  try {
    const post = await Post.create({
      content: req.body.content,
      img: req.body.url,
      UserId: req.user.id,
    });
    const hashtags = req.body.content.match(/#[^\s#]*/g);
    if (hashtags) {
      const result = await Promise.all(
        hashtags.map(tag => {
          return Hashtag.findOrCreate({
            where: { title: tag.slice(1).toLowerCase() },
          })
        }),
      );
      await post.addHashtags(result.map(r => r[0]));
    }
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

이미지 리사이징이 오래걸려서 리사이징된 이미지가 일정 기간 동안 표시되지 않는 경우를 대비해 img 태그에 onerror 속성을 붙여서 리사이징 이미지를 로딩하는 데 실패하면 원본 이미지를 사용하도록 수정 

🔻 nodebird/views/main.html

![html](https://github.com/leekoby/leekoby.github.io/assets/118284808/f5f0c596-c35c-4813-b15c-04323bae52a4)

<br/>

### **🍳 이미지 업로드하기**

람다를 통해 이미지 리사이징 된 것 확인하기

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/baba604c-78b5-459d-95cd-1698f5fb0c73)

<br/>

---

<br/>

## **💻 Google Cloud Storage 사용하기**

### **🍳 Cloud Storage 이용하기**

좌측 메뉴에서 Storage를 선택한 후 버킷 만들기 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/36791fc3-ee14-4f7e-9dc9-e233d1b2618b)

<br/>

### **🍳 Cloud Storage 버킷 만들기**

버킷 이름은 고유해야 하므로 고유한 버킷 이름 정한 후 만들기 버튼 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/569e8c07-5978-42d1-a615-d05544a5be6b)

<br/>

### **🍳 버킷 권한 수정하기**

우측 버킷 메뉴에서 액세스 수정 선택

구성원 추가에 allUsers를 입력하고 저장소 개체 뷰어 선택 후 추가

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2144fa6c-9888-453c-8242-640c370ad342)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c2f6bf5b-44aa-4f18-9f7e-68e8ae61b1dc)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/07907f41-8df0-4899-9416-440db83131c0)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9dcb4bc2-22a2-4056-8025-a89f9e0f8e91)

- 위 단계에서 오류가 발생할 경우 공개 액세스 방지를 삭제해주면 됨

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a5e02761-5407-4d31-bdac-6ab9f3299cd4)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ab93746e-52f6-4bdd-893f-ea4ede7f0a2d)

<br/>

### **🍳 클라우드 스토리지 키 발급받기**

[https://console.cloud.google.com/apis/credentials](https://console.cloud.google.com/apis/credentials){:target="_blank"} 에 접속하기

사용자 인증 정보 화면에서 서비스 계정 키 선택

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9e5dce98-41fe-4b1d-b9d1-41d11b0f0b7d)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/af2d2082-5acb-44fd-b234-642a1ea7c14f)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/6800ed5e-a3e3-4fcb-9cbc-ca17210f5bce)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/45e80090-35e3-4a62-9d3e-17c124295e08)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5fdfb6b8-fd09-4ed3-9fe8-ad5363329570)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/e944d833-bdfb-45b5-921a-91e08f6da7b7)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7b49962c-433f-44c2-acb0-9593cf3b6549)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/ca8f14a7-fa7c-48b0-a3aa-232caa2d0489)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/a8a38058-c926-49ae-8839-7e83e29d8007)

<br/>

### **🍳 클라우드 스토리지 연결하기**

multer-google-storage와 axios 설치

- routes/post.js에 multer-google-storage를 multer 대신 연결

- 아까 다운받은 json 파일 이름을 keyFilename에 입력, projectId는 프로젝트의 

- 아이디 입력(홈 메뉴의 프로젝트 정보 섹션에 있음)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/1f394bf5-77f5-4037-b46d-e035a0c8625f)

```sh
npm i multer-google-storage
```

🔻 nodebird/routes/post.js

```js
const express = require('express');
const multer = require('multer');
const fs = require('fs');
const multerGoogleStorage = require('multer-google-storage');

const { afterUploadImage, uploadPost } = require('../controllers/post');
const { isLoggedIn } = require('../middlewares');

const router = express.Router();

try {
  fs.readdirSync('uploads');
} catch (error) {
  console.error('uploads 폴더가 없어 uploads 폴더를 생성합니다.');
  fs.mkdirSync('uploads');
}

const upload = multer({
  storage: multerGoogleStorage.storageEngine({
    bucket: 'nodebird3',
    projectId: 'node-deploy-358509',
    keyFilename: 'node-deploy-358509-a2917cd5849c.json',
    filename: (req, file, cb) => {
      cb(null, `original/${Date.now()}_${file.originalname}`);
    },
  }),
  limits: { fileSize: 5 * 1024 * 1024 },
});

// POST /post/img
router.post('/img', isLoggedIn, upload.single('img'), afterUploadImage);

// POST /post
const upload2 = multer();
router.post('/', isLoggedIn, upload2.none(), uploadPost);

module.exports = router;
```

🔻 nodebird/controllers/post.js

```js
const { Post, Hashtag } = require('../models');

exports.afterUploadImage = (req, res) => {
  console.log(req.file);
  res.json({ url: req.file.path });
};

exports.uploadPost = async (req, res, next) => {
  try {
    const post = await Post.create({
      content: req.body.content,
      img: req.body.url,
      UserId: req.user.id,
    });
    const hashtags = req.body.content.match(/#[^\s#]*/g);
    if (hashtags) {
      const result = await Promise.all(
        hashtags.map(tag => {
          return Hashtag.findOrCreate({
            where: { title: tag.slice(1).toLowerCase() },
          })
        }),
      );
      await post.addHashtags(result.map(r => r[0]));
    }
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

<br/>

### **🍳 이미지 업로드 시도하기**

[http://localhost:8001](http://localhost:8001){:target="_blank"}에 접속하여 로그인 후 이미지 업로드

클라우드 스토리지 버킷에 이미지가 업로드된 것 확인

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/edca2f99-3d66-4e54-9366-15bca7b3d00b)

<br/>

---

<br/>

## **💻 Google Cloud Functions 사용하기**

### **🍳 이미지 리사이징을 위해 펑션 사용**

이미지 리사이징은 CPU를 많이 사용하기 때문에 기존 서버로 작업하면 무리가 감

Cloud Functions라는 기능을 사용해 필요할 때만 서버를 실행해서 리사이징

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/59ec3563-35a1-459f-9ed9-2e6059bf9283)

<br/>

### **🍳 펑션용 package.json 작성하기**

gcp-upload 폴더 안에 package.json 작성하기

🔻 gcp-upload/package.json

```js
{
  "name": "gcp-upload",
  "version": "0.0.1",
  "description": "Cloud Functions 이미지 리사이징",
  "main": "index.js",
  "author": "leekoby",
  "license": "ISC",
  "dependencies": {
    "@google-cloud/storage": "^6.4.1",
    "gm": "^1.23.1",
    "sharp": "^0.30.5"
  }
}
```

🔻 gcp-upload/.gitingnore

```js
node_modules
```

<br/>

### **🍳 펑션으로 이미지 리사이징하기**

resizeAndUpload 메서드에 코드 작성

- storage.bucket(버킷명).file(파일명)
- readStream으로 파일 읽어들임
- sharp로 이미지 리사이징
- writeStream으로 파일 출력
- resolve로 응답 마무리

🔻 gcp-upload/index.js

```js
const storage = require('@google-cloud/storage')();
const sharp = require('sharp');

exports.resizeAndUpload = (data, context) => {
  const { bucket, name } = data;
  const ext = name.split('.').at(-1);
  const requiredFormat = ext === 'jpg' ? 'jpeg' : ext; // sharp에서는 jpg 대신 jpeg사용합니다
  console.log('name', name, 'ext', ext);

  const file = storage.bucket(bucket).file(name);
  const readStream = file.createReadStream();

  const newFile = storage.bucket(bucket).file(`thumb/${name}`);
  const writeStream = newFile.createWriteStream();

  sharp(readStream)
    .resize(200, 200, { fit: 'inside' })
    .toFormat(requiredFormat)
    .pipe(writeStream);
  return new Promise((resolve, reject) => {
    writeStream.on('finish', () => {
      resolve(`thumb/${name}`);
    });
    writeStream.on('error', reject);
  });
};
```

<br/>

### **🍳 코드 깃허브로 전송하기**

먼저 GitHub에 gcp-upload 리포지터리로 올린 후 컴퓨트엔진 인스턴스에서 클론

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f7823ed5-53ef-495f-b890-7c5323183573)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/79cadefe-7310-42c2-9dd3-d1ef4fc9c05b)

압축 후 클라우드 스토리지로 업로드

<br/>

### **🍳 코드 압축해서 클라우드 스토리지로 보내기**

압축 후 S3로 업로드

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/146fe246-ef60-441c-be46-4a63993db58f)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/c00274e9-1b5b-431d-874d-14cc59de3f4f)

<br/>

### **🍳 펑션 이용하기**

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/cccb6245-a08f-419a-9968-839a58259848)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/15c5e0ea-60fd-4f74-8bed-c18b01d48b87)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/2cee04e6-a532-4f64-a164-25c016111a8b)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/643cf239-f0b1-456b-b029-d1871f8c1237)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f3ee3bf1-042f-4ed8-85f3-ab3d816b43c7)

<br/>

### **🍳 NodeBird에 펑션 연결하기**

🔻 nodebird/controllers/post.js

```js
const { Post, Hashtag } = require('../models');

exports.afterUploadImage = (req, res) => {
  console.log(req.file);
  const filePath = req.file.path.split('/').splice(0, 3).join('/');
  const originalUrl = `${filePath}/${req.file.filename}`;
  const url = originalUrl.replace(/\/original\//, '/thumb/');
  res.json({ url, originalUrl });
};

exports.uploadPost = async (req, res, next) => {
  try {
    const post = await Post.create({
      content: req.body.content,
      img: req.body.url,
      UserId: req.user.id,
    });
    const hashtags = req.body.content.match(/#[^\s#]*/g);
    if (hashtags) {
      const result = await Promise.all(
        hashtags.map(tag => {
          return Hashtag.findOrCreate({
            where: { title: tag.slice(1).toLowerCase() },
          })
        }),
      );
      await post.addHashtags(result.map(r => r[0]));
    }
    res.redirect('/');
  } catch (error) {
    console.error(error);
    next(error);
  }
};
```

🔻 nodebird/views/main.html

![html](https://github.com/leekoby/leekoby.github.io/assets/118284808/1254ef22-757d-4b2c-a551-9de28345518c)

<br/>

### **🍳 이미지 업로드하기**

펑션을 통해 이미지 리사이징 된 것 확인하기

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/06915832-d806-48e6-b97c-830e9367a58b)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/f7f71a78-0687-46ef-b725-2513d64779eb)

<br/>

---

<br/>

## **💻 함께 보면 좋은 자료**

> [S3](https://aws.amazon.com/ko/s3/){:target="_blank"}
{:.prompt-info}

> [람다](https://aws.amazon.com/ko/lambda/features/){:target="_blank"}
{:.prompt-info}

> [Google Cloud Storage](https://cloud.google.com/storage?hl=ko){:target="_blank"}
{:.prompt-info}

> [Google Functions](https://cloud.google.com/functions?hl=ko){:target="_blank"}
{:.prompt-info}

> [sharp](https://www.npmjs.com/package/sharp){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## 📚 **레퍼런스**

>조현영. Node.js 교과서 = Node.js Textbook / 조현영 지음 (2022). Print.
{:.prompt-info}

>[[리뉴얼] Node.js 교과서 - 기본부터 프로젝트 실습까지](https://www.inflearn.com/course/%EB%85%B8%EB%93%9C-%EA%B5%90%EA%B3%BC%EC%84%9C){:target="_blank"}
{:.prompt-info}