---
layout: post
title: "Git Refspec"
date: 2014-04-16 10:45:23 +0900
comments: true
categories:
---

# Git remote
`$ git remote add origin git-url`
을 통해서 로컬 브랜치와 리모트 브랜치를 연결했다. 리모트 저장소는 여러개 있을수 있으므로 `$ git remote add qa git-url` 을 통해서 여러 리모트 저장소를
연결 할수 있다. `$git remote -v` 명령어를 실행하면 연결된 리모트 브랜치를 확인할수 있다.


# Git Refspec
`$ cat .git/config` 을 실행 하면 아래와 같은 내용을 확인할수 있다.
{% highlight bash linenos %}
[core]
  repositoryformatversion = 0
  filemode = true
  bare = false
  logallrefupdates = true
  ignorecase = true
  precomposeunicode = false
[remote "origin"]
  url = git@github.com:NextersAppFactory/Vobble.API.git
  fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
  remote = origin
  merge = refs/heads/master
[branch "jytest"]
  remote = origin
  merge = refs/heads/jytest
[merge]
  tool = vimdiff
[remote "qa"]
  url = git@github.com:rangken/gittest.git
  fetch = +refs/heads/*:refs/remotes/qa/*
{% endhighlight %}
`git config` 로 설정한 내용과 위에서 추가한 리모트 저장소의 내용을 볼수있다. 브랜치는 master 와 jytest 두개를 만었고 리모트 저장소는 origin 과 qa를 만들었다.
각 Refspec 을 자세히 보면

  `+refs/heads/*:refs/remotes/origin/*`

`+` 와 `<src>:<dest>` 형식으로 되어있다. `<src>`은 로컬 저장소의 레퍼런스고 `<dst>` 는 매핑되는 리모트 저장소의 래퍼런스이다.
`+` 가 없으면 Fast_forward 가 아니라도 업데이트 된다.

{% highlight javascript linenos %}
$ git log origin/master
$ git log remotes/origin/master
$ git log refs/remotes/origin/master
{% endhighlight %}
위 세개의 명령은 모두 리모트 저장소에 있는 master 브랜치에 로그를 출력하라는 의미이다.
`git log master` 는 로컬 저장소에 master 브랜치의 로그를 출력한다.
`git log refs/heads/master` 와 같은 의미이다.

만약에 fetch 부분을 `+refs/heads/master:refs/remotes/origin/master` 로 변경하면 master 브랜치에서만 가져올수 있고 jytest 브랜치에서는 정보를 가져올수
없도록 만들수 있다. 즉 * wildcard 의 뜻은 어떤 브랜치이든 위에 url 과 연결 하라는 뜻이다.
`$ git fetch origin master:refs/remotes/origin/master` 명령 처럼 명시적으로 브랜치를 지정해 줄 수 도 있다.
로컬 브랜치 master 로 리모트 브랜치 origin/master 의 정보를 가져오라는 뜻이된다.

{% highlight javascript linenos %}
[remote "origin"]
  url = git@github.com:NextersAppFactory/Vobble.API.git
  fetch = +refs/heads/*:refs/remotes/origin/*
  fetch = +refs/heads/*:refs/remotes/qa/*
{% endhighlight %}
위 처럼 두개의 fetch 저장소를 사용해도 된다.

# Refspec Push
`$ git push origin master:refs/heads/qa/master` 를 실행하면 현재 master 브랜치를 리모트의 qa/master 브랜치로 Push 할 수가 있다.
매번 지정해 주기 귀찮을 경우
{% highlight javascript linenos %}
[remote "origin"]
  url = git@github.com:NextersAppFactory/Vobble.API.git
  fetch = +refs/heads/*:refs/remotes/origin/*
  push = refs/heads/master:refs/heads/qa/master
{% endhighlight %}
해주면 `git push origin`을 실행 하면 알아서 로컬 ref/heads/master 에서 리모트 refs/heads/qa/master 푸시를 하게된다.

# 래퍼런스 삭제하기
Refspec 으로 서버에 있는 래퍼런스를 삭제할수 있다.

`$ git push origin :topic`
Refspec 의 형식은 `<src>:<dst>` 이고 `<src>` 를 비우고 실행하면 `<dst>` 를 비우라는 명령이 되고 `<dst>` 는 삭제된다.

# Reference
[Git-scm](http://git-scm.com/book/ko/Git의-내부-Refspec)
