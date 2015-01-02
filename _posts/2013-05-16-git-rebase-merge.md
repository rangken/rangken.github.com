---
layout: post
title: "Git Rebase Merge"
description: "Git rebase Merge 사용하기"
category: ETC
tags: [Git]
---
# GIT Rebase 
GIT 에 Branch를 사용하다보면 Rebase를 사용 할 일이 많아진다. 물론 rebase를 사용하지 않더라도 큰 문제는 없지만 Rebase는 git 을 좀더 깨끗하게(?) 사용하게 도와준다. Rebase의 용도는 많지만 한마디로 정의하면 `Git 의 commit 들을 정리하기` 라고 할 수 있겠다.
# Overview
![merge-example](/assets/images/merge-example.png)<br>
그냥 Merge 를 실행 할 경우 <br>
![rebase-example](/assets/images/rebase-example.png) <br>
Rebase 후 Merge 실행 할 경우
# GIT Rebase vs Merge
git rebase 는 원하는 Branch를 현재 Branch나 특정 Branch에 히스토리에 맞게 재정렬 하도록 설정하기 위함이다. `git rebase <rebase 하려는 branch> <rebase 가 될 branch>
` 로 가능하다. 보통 git rebase branch-name 으로 사용해서 자동으로 현재 Branch에 대하여 rebase를 한다. <br>
<br>
![rebase-normal-merge](/assets/images/rebase-normal-merge.png) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Merge <br>
![rebase-rebase-merge](/assets/images/rebase-rebase-merge.png) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Rebas -> Merge(--ff) <br>
<br>
Master Branch에서 첫번째 커밋을 수행한후 develop Branch를 생성한다음에 develop Branch와 master Branch에 각각 커밋을 만들어 강제로 conflict 를 만들었다.
위에 그림은 master Branch에서 develop Branch를 merge 를 수행한것이다. develop에 추가된 commit을 가져오고 머지를 수행하기 위한 commit을 추가해서 master Branch가 변화하였다. Merge를 통해서 실제 일어난 일들을 자세히 알 수 있지만 여러 사람이 동시에 작업 할 경우 세세한 정리되지 않는 Merge 히스토리 까지 보고 싶지 않을 수 있다. <br>
이럴 경우 develop branch 에서 rebase 를 수행한후 master branch 에서 merge를 수행 할 경우 아래 그림 처럼 commit 로그들이 정리 되는 것을 확인 할 수 있다.
> rebase 를 수행 하려고 하는데 conflict 가 날 경우 rebase 를 위한 branch 가 생성되고 
> 해당 Branch에서 rebase 를 수행하게 되는데 conflict 를 해결 후에 git add 를 수행한후 
> git rebase --continue 를 실행 해주면 rebase 과정이 올바르게 수행된다. 도중에 
> rebase를 과정을 그만 두고 싶을 경우에는 git rebase --abort 를 수행해서 rebase 수행 > 전으로 돌아 갈 수 있다.
`B221B61- develop add content` commit 로그는 그냥 Merge를 실행 시킬 경우와 Rebase 후 Merge 를 실행 시킬때 커밋 해쉬가 다르다는 것을 알수 있다. 이것은 rebase 가 기존 마지막 커밋을 새로 Rebase 한후 master Branch 보다 앞선 새로운 로그를 만들기 때문이다. rebase 가 이런 과정을 수행하기 때문에 master Branch에서 merge를 수행 할 경우 develop Branch가 Master Branch 보다 앞서 있기 때문에 fast-forward Merge 가 실행된다.

rebase로 commit 로그 들은 정리가 되었지만 실제 Merge된 정보들에 대한 History는 표현되지 않고 잃어버리게 된다. 이는 Rebase 과정을 통해 Merge가 fast-forward Merge 가 수행 되었기 때문이다.(develop Branch에서 rebase 를 수행하지 않으면 3-way-merge가 일어나서 commit 로그들이 남음). 이렇게 merge를 수행 할 때 fast-forawrd 가 일어나더 라도 merge 에 대한 히스토리를 남기고 싶다면 `git merge --no-ff develop` 을 수행 하면 된다.<br>
<br>
![rebase-no-ff](/assets/images/rebase-no-ff.png) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Rebas -> Merge(--no-ff) <br>
<br>
위 그림에서 보는거 처럼 기존 한번에 합쳐졌던 `b221b61 - develop add content` commit 이 한번에 합쳐진것이 아니라 develop Branch에서 왔음을 정확히 알 수 있고 `dd5a897 - Merge branch` commit 을 통해 Branch로 합쳐 졌음을 알수 있다.
# Rebase 를 사용하는 이유
![merge-base](/assets/images/merge-base.png) <br>
![merge-bad](/assets/images/merge-bad.png) <br>
![rebase-good](/assets/images/rebase-good.png) <br>
<br>
위 그림은 rebase를 사용하지 않고 모든 branch 를 master Branch에 merge한 것이다. 그림만 봐도 알수 있지만 모든 branch 들이 master에 대해 정렬되 있지 않고 엉켜 있는 것을 알수 있다.
밑에 그림은 모든 Branch를 master에 rebase 한후 master Branch에서 merge를 실행 했다. iss1 을 머지 할때는 --no-ff 옵션 없이 fast-forward Merge를 수행했고 나머지는 --no-ff옵션을 사용해서 Branch에 대한 로그를 남겼다. 위에 그림 보다 좀더 정렬된 히스토리를 볼수 있다.
# GIT Rebase -i
Git rebase -i 옵션을 통해 여러 커밋들을 하나로 합치거나 커밋한 이름을 수정하거나 할 수 있다.
`git rebase -i HEAD~3` 현재 Head로 부터 3개 뒤로 떨어진 커밋들을 rebase 한다. rebase 를 수행하기 위해 새로운 branch 로 이동이 되는데 이 branch 에서 rebase 를 수행한다. <br>
rebase 를 하기 위한 여러 옵션이 있다.<br>
> pick - 해당 commit 은 선택한다. 즉 해당 commit은 유지 한다. <br>
> squash - 해당 커밋을 이전 커밋으로 흡수 되도록 한다. 무조건 앞에 커밋이 한개 있어야 한다.<br>
> reword - pick 과 같지만 commit 메세지를 수정한다.<br>
해당 옵션으로 설정을 변경하면 commit 들이 바껴져 있는 것을 확인 할 수 있다. 특정 branch 에서 자신의 여러 커밋들을 수행한후 master 에 머지 할 경우 commit 들을 다른 사람들이 보기 편하도록 정리한후 merge 를 실행 시켜주는 것이 좋다. 

![git-rebase-i1](/assets/images/git-rebase-i1.png)<br>
연속으로 커밋을 3번 수행 하였다. 해당 커밋중 위에 두 커밋을 합치기 위해 
`git rebase -i HEAD~2`을 수행
<br>
![git-rebase-i2](/assets/images/git-rebase-i2.png) <br>
두번째 commit을 pick 하고 세번째 commit 은 squash 를 수행하였다. 하지만 두번째 commit 의 commit Hash 번호는 바뀐것을 알 수 있다. rebase 를 통해 새로 정렬된 마지막 히스토리는 변경 된다.
# Reference
[dogfeet](http://dogfeet.github.io/articles/2012/git-merge-rebase.html) - http://dogfeet.github.io/articles/2012/git-merge-rebase.html