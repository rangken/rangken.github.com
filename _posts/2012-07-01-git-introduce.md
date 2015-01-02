---
layout: post
title: "Git Introduce"
tagline: "Git Introduce & git Install"
description: "Git!!"
category: ETC
tags: [Git]
---

![GIT Logo](/assets/images/git_logo.png)

##GIT 소개
Git 이란 프로그램 등의 소스 코드 관리를 위한 분산 버전 관리 시스템이다. 쉽게 말하자면 여러 개발자들이 공동으로 개발 할때 소스를 관리 하기 위한 프로그램 쯤 으로 생각하면 되겠다. 요즘은 오픈소스들이 대부분 GitHub 으로 제공되면서 Git이 점점더 활용되고 있다.  물론 [리누스 토발즈](https://github.com/torvalds)라는 스타 개발자가 개발한 거라 더더욱 주목 받는 점도 있는것 같다.  ( 리누즈 토발즈는 리눅스를 만든 핀란드 천재 개발자 )
![OCTO Logo](/assets/images/git_octo_logo.png)
![BIGBUCKET Logo](/assets/images/bigbucket_logo.png)
Git 은 리눅스를 이용해 서버를 세팅해도 되지만 여러가지 쉽지 않은 문제로 Github 이나 BitBucket 같은 SaaS 서비를 사용하는 편이다.
GitHub은 Public 프로젝트는 꽁짜지만 Private 프로젝트의 경우 유료이며 BitBucket은 Public Private 와 상관없이 Collaborator 5명까지는 꽁짜이다.
Github이 대세긴 하지만 Bitbucket 도 JIRA를 만든 회사로써 지속적인 업그레이드 중이라 많은 발전이 있을거라고 예상이된다~~

## Git 설치
Git 은 Free Software 로  [http://git-scm.com/downloads](http://git-scm.com/downloads) 에서 다운 받을수 있다.
Linux 사용하시는 분들은 쉽게
>sudo apt-get install git-core
이미 Git을 사용하고 있고 최신 버젼을 다운 받기 위해서는
>git clone https://github.com/git/git.git
Git 을 사용하기 위한 여러가지 GUI 툴이 있으나 툴에 의존해서는 많은것을 할 수 없으니 공부 차원에서 Command Line 을 사용하기를 추천합니다.
설치가 다 되었다면  Command Line 에
>git --version
버젼이 정확히 나온다면 설치는 완료된 상태다.

## Git Config

Git 설정을 하기 위해 Command Line에 여러 사항을 입력해준다.

아래 사항은 해주어도 되고 안해주어도 깃을 사용하는데 문제는 없다.
{% highlight bash linenos %}
git config --list; // 먼저 현재 설정된 config list 들을 출력
git config --global user.name "James Kim" // git 을 사용할때 사용할 자기 이름을 설정
git config --global user.email rangken@gmail.com // 메일설정
// 만약 github 을 사용한다면
git config --global github.user "Rangken" // Git 에 표시할 이름
git config --global github.email "rangken@gmail.com" // Git에 표시할 이메일
{% endhighlight %}

## Git Init
git 을 사용할 폴더로 이동한후에
git init 을 해준다.git init 은 해당 프로젝트에 .git 폴더를 생성한후해당 프로젝트가 git을 사용하도록 해준다.
git 폴더로 이동 ( cd .git) 하면 git에서 사용하고 있는 여러가지 폴더들을 볼 수 있다.
cat config 을 하면 해당 프로젝트에 설정된 git의 설정들을 볼 수 있다.
