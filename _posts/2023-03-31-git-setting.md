---
layout: post
title: git 환경설정
author: admin
date: 2023-03-31 00:00:00 +900
lastmod: 2023-03-31 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [COLLABORATION, GIT]  # 대문자로 작성
tags: [git, git setting] # 소문자로 작성
---

> 기존 블로그에 작성했던 포스트를 이전한 글입니다.
{:.prompt-info}

> 해당 포스트는 `GIT SETTING`를 학습하며 정리한 내용에 대한 포스트입니다.
{:.prompt-info}

<br/>

---


<br/>

# **🍀 git 환경설정**

## **🌈 환경설정 - 1**

Git을 이용할 때 필요한 환경 설정은 사용자 정보와 에디터 설정

### **💻 사용자 정보**

- Git을 설치하면 가장 먼저, 사용자 이름과 이메일 주소를 설정한다.

- 설정에 기록된 사용자 이름과 메일 주소를 앞으로 진행할 Git 커밋 내역에 기록

터미널 화면에 다음과 같이 입력. 

(나의 사용자 이름과 내 이메일 주소를 대신해 Github에 등록된 사용자 이름과 이메일 주소를 사용)

```sh
$ git config --global user.name "나의 사용자 이름" 
$ git config --global user.email "내 이메일 주소"
```

예를 들어 김코딩의 경우라면 다음과 같이 입력

```sh
$ git config --global user.name "kimcoding"
$ git config --global user.email "kimcoding@example.com"
```

- `--global` 옵션으로 설정하면, 사용자 홈에 저장되므로 git을 설정할 때 처음에 단 한 번만 입력

- 나중에 github의 사용자 이름이나 이메일을 변경한다면, 이 명령어를 다시 입력해야 한다.

- 만약 여러 프로젝트를 진행하고 있어서, 프로젝트마다 다른 사용자 이름과 이메일 주소를 사용하고 싶으면 `--global` 옵션을 빼고 명령을 실행

<br/>

---

<br/>

### **💻 에디터**

Git에서 커밋 메시지를 기록할 때, 특히 merge commit 확인 메시지가 나올 때 텍스트 에디터가 열린다. 

- 기본값으로 텍스트 에디터 vi가 열리는데, vi 에 익숙하지 않다면, nano로 변경하는 편이 좋다.

> [Git 최초 설정](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%B5%9C%EC%B4%88-%EC%84%A4%EC%A0%95){:target="_blank"}
{:.prompt-info}

<br/>

---

<br/>

## **🌈 환경설정 - 2**

추가 인증 방법에는 SSH를 이용하는 방법도 있다.

- ssh는 Secure shell의 줄임말(Secure SHell)

- 보안이 강화된 shell 접속을 뜻한다. 

- CLI 환경(터미널)에서 다른 PC에 접속하거나 요청할 때 사용

- 비대칭키를 이용해 사용자를 인증

<br/>

---

<br/>

### **💻 SSH 키 생성**

- ssh 키는 비대칭키로 구성

- 그 이름에서 유추할 수 있듯이 두 개의 키가 서로 대칭이 되지 않는 형태로 존재


다음의 명령어를 프롬프트에 입력하고, ssh 키 페어(쌍)을 생성

명령어를 입력 후 Enter 키를 몇 번 입력하면, ssh 키 페어가 생성

```sh
ssh-keygen 
```

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/fa036e9c-bd2d-4dc1-a282-bf0610a82f0d)

- `ssh-keygen` 명령어는 경로 `~/.ssh./` 에 두 파일 `id_rsa` 와 `id_rsa.pub` 를 생성

- 이 두 파일은 ssh 키 페어라고 하며, 이 중 `id_rsa.pub`는 누구에게나 공개해도 되는 <strong style="color:#ff6600">공개키</strong>(Public Key) 

- `id_rsa`는 <mark>공개되면 안 되고 나만 보관하고 있어야 하는 키</mark>라고 하여 <strong style="color:#ff6600">개인키</strong>(Private Key) 또는<strong style="color:#ff6600">비밀키</strong>(Secret Key)

SSH 키 페어를 생성하였으므로, 생성된 키 페어 중 공개키를 복사하여 gitub에 등록합니다.

<br/>

---

<br/>

### **💻 공개키(Public Key) 복사**

다음의 명령어를 프롬프트에 입력하여, 공개키를 복사

```sh
cat ~/.ssh/id_rsa.pub
```


![](https://github.com/leekoby/leekoby.github.io/assets/118284808/8a3c8004-8078-43bf-9dad-537197efd8c2)


- 화면에 출력된 키를 마우스로 드래그한 다음 복사 

복사하는 방법은 다음과 같다.

1. 마우스 우클릭 - 복사(copy)

2. 키보드로 복사하는 경우 OS에 따라, 사용자의 터미널 환경 설정에 따라 키가 다를 수 있다.

   - (Linux) Ctrl + Shift + C

   - (macOS) Cmd + C
  
메모장이나 채팅창에 붙여넣기하여 복사가 잘 되었는지 확인 후 복사가 잘 되었다면, 다음으로 이동하여 github에 공개키를 등록

<br/>

---

<br/>

### **💻 Github에 공개키 등록**

브라우저에서 [Github](https://github.com/){:target="_blank"}로 이동하여 로그인

우측 상단의 프로필 이미지를 클릭하고, Settings 에 진입

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/91291035-d0ac-4ab8-8185-f356e312e28b)

왼쪽의 내비게이션에서 `SSH and GPG keys` 를 선택

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/70462f5b-a79d-43a6-b5f0-87cfc32a19f2)

나타난 화면에서 SSH Keys 옆의 초록색 버튼 New SSH Key 를 클릭

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/c64f4afe-a61d-4a89-9027-365685260bac)

등록한 SSH 공개키를 구분할 수 있도록 사용자 임의로 Title을 작성

Key에는 복사해둔 공개키를 붙여 넣고, Add SSH Key 버튼을 클릭

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/5ec466bc-65a1-431c-aa9c-bf66cf580eb1)

Confirm access에서 Github 로그인에 필요한 비밀번호를 입력해 SSH key 등록을 승인

SSH 공개키가 정상적으로 등록되었는지 확인하려면, 다음의 단계를 따라 레포지토리를 `clone` 

<br/>

---

<br/>

### **💻 테스트하기**

공개키가 잘 등록되었다면, 다음의 과정이 정상적으로 수행되어야 한다.

Repository에서 초록색의 Code 버튼을 클릭하고, SSH 탭을 선택

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/55c5a6c9-2ae3-46bd-9e19-944365de16b7)

나타난 문자열을 복사

우측의 클립보드로 복사 버튼을 클릭해 복사하거나 문자열을 드래그한 후 복사할 수 있다.

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/9818374d-8500-4fdc-ae23-d47b555e86ce)

지금 복사한 내용은 다음과 같다.

> git@github.com:codestates-seb/fe-sprint-cli-practice.git


다음의 명령어를 프롬프트에 입력하고, 정상적으로 동작하는지 확인.

> git clone git@github.com:codestates-seb/fe-sprint-cli-practice.git

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/7708cb3d-cb06-473b-b868-5cea1d1d2914)

![image](https://github.com/leekoby/leekoby.github.io/assets/118284808/432e46d3-ae82-4984-93e2-3f9d50487e21)

`ls` 를 입력해 정상적으로 폴더가 생성된 걸 확인했다면, 잘 마무리 된 것이다. 

Optional 콘텐츠 `CLI 사용을 위한 인증 과정` 은 생략할 수 있다.

만약 정상적으로 동작하지 않는 경우, 다음과 같이 조치를 취해보자.

1. Github - Settings - SSH keys 에 등록한 공개키 삭제

2. 프롬프트에 `cat ~/.ssh/id_rsa.pub` 명령어를 입력하여 나타나는 공개키 복사

3. Github - Settings - SSH keys 에서 New SSH key 버튼 클릭

4. Title을 입력하고, 복사한 공개키를 Key에 붙여 넣고 Enter 키를 입력해 한 줄을 추가(개행)

5. Add SSH key 버튼을 클릭하고, 승인하여 공개키를 등록

위의 조치 후에도 여전히 동작하지 않는다면, 다음 CLI 사용을 위한 인증 과정으로 SSH 인증을 대체할 수 있다.

<br/>

---

<br/>

## **🌈 CLI 사용을 위한 인증 과정(optional)**

Github CLI를 사용하기 위해서는 인증 과정이 필요

사용할 수 있는 가장 쉽고 빠른 방법인 OAuth (Device Authorization) 인증 과정을 진행해보자.


1. 먼저 [GitHub CLI](https://cli.github.com/){:target="_blank"}를 설치

   - macOS에서는 homebrew 설치 후, 다음 명령을 이용하여 CLI를 설치

> $ brew install gh

우분투 리눅스에서는 다음 순서를 따른다.

```sh
$ curl -fsSL `<https://cli.github.com/packages/githubcli-archive-keyring.gpg>` 또는 sudo gpg --dearmor -o /usr/share/keyrings/githubcli-archive-keyring.gpg

$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] `<https://cli.github.com/packages>` stable main" 또는 sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null

$ sudo apt update

$ sudo apt install gh
```



> gpg: failed to start the dirmngr '/usr/bin/dirmngr': No such file or directory 에러가 난다면?
> 
> dirmngr 패키지가 필요하다. 
> 
> sudo apt-get install dirmngr 를 통해 설치 후 위 과정을 다시 시도

2. `gh auth login` 명령어를 통해 로그인을 시도

화살표 키를 이용해 다음 항목들을 선택 후 Enter

```sh
? What accout do you want to log into? GitHub.com
? What is your preferred protocol for Git Operations? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Login with a web browser
```

과정 중 실수가 발생했다면, `Ctrl + C` 를 눌러 중단할 수 있다.

3. Login with a web browser 옵션을 선택하면, 다음과 같이 one-time code 가 등장. 
Enter 키를 누르면, Device Activation을 가능하게 하는 창이 등장.


![](https://github.com/leekoby/leekoby.github.io/assets/118284808/3f315418-8682-4fac-8899-ef3b6b67ccb1)


![](https://github.com/leekoby/leekoby.github.io/assets/118284808/e3cea19c-3993-43ed-bde2-059f562d8e41)


위 화면을 실수로 닫았다면, [https://github.com/login/device](https://github.com/login/device){:target="_blank"}에 접속하면, 위 화면이 등장

4. 모든 인증 과정이 성공적으로 끝나면, 터미널 화면에서도 Logged in as 사용자이름 과 같이 인증이 완료

![](https://github.com/leekoby/leekoby.github.io/assets/118284808/97f42d3b-d028-4d41-9de7-db449decbfc3)

<br/>

---


<br/>


## **📚 레퍼런스**


> [Git 최초 설정](https://git-scm.com/book/ko/v2/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%B5%9C%EC%B4%88-%EC%84%A4%EC%A0%95){:target="_blank"}
{:.prompt-info}
