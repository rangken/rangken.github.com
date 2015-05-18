---
layout: post
title: "Git Add , Git Commit"
description: "Git 기본"
category: ETC
tags: [Git]
---

## Git 기초 <br>
![GIT STATUS](/assets/images/git_status.png)
<br>
Git 은 기본적으로 각 파일의 상태를 가지고 있다.
git status 명령어를 이용해 해당 파일들의 Status 를 알수 있다.

> Untracked : 아직 git 에 등록되지 않은 파일이다. <br>
> unmodified( traceked ) : git에 등록된 파일이지만 수정되지는 않은 상태이다.<br>
> modified : 해당 파일이 수정된 상태이다.<br>
> staged : Commit 을 할 수 있는 상태이다.<br>


Git init 으로 git 프로젝트를 만든 후 파일을 하나 만든 상태에서 git status 을 한다면 
`nothing added to commit but untracked files present (use "git add" to track)`
상태인것을 확인 할 수 있다. 이상태는 해당 파일이 untracked 상태라는 것을 알려주고 git add 명령어를 통해 파일을 unmodified 상태로 바꾸라는 것이다.

> git add . 
명령어로 tracked 상태로 변경해준다. 그후에 status를 확인하면 `Changes to be committed:` 
상태로 변경 된다. 해당 상태는 커밋하기 위한 상태이다.

> git commit -m "first commit"

으로 커밋을 해준다.
그후 status을 확인하면 <br>
`nothing to commit (working directory clean)`
상태인것을 확인 할 수 있다.!
**간단히 정리하자면** <br>
+ 파일 추가(untracked)
+ git add . (tracked, unmodified)
+ 파일 수정 (modified )
+ git add . (staged)
+ git commit -m "asd" (tracked, unmodified)

> Nothing to commit -> commit 할게없음 <br>
> Untracked files -> 아직 추적되지 않았음( add 해야됨) <br>
> Changes to be committed -> Commit 해야됨 <br>
> Changes but not update -> add 해야함 ( 한번은 commit 된 상태) <br>


## Git add
간단히 git add 명령어는 해당 파일을
untracked -> tracked 상태로 바꾸거나
commit 하기 위한 상태 ( staged) 상태로 바꿀 수 있는 명령어 이다.
git add option 을 넣어서 각 파일의 상태를 변경하는 명령어 이다. <br>
`git add filename`
   파일명에 해당 하는 파일을 add 한다.<br>
`git add *.c`
   확장자가 c 파일인 모든 파일을 add 한다.<br>
`git add .`
   변화된 파일과 추가된 파일 모두를 add 한다.
(단 삭제된 파일은 add 하지 않는다)
대부분 add 할때는 이명령어를 자주 사용한다.<br>
`git add -u `
   변화된거랑 삭제된 것만 add 한다.
( 단 추가된 파일은 add 하지 않는다)<br>
`git add -A`
 git add . 와 git add - u 를 동시에 실행 한 것과 동일한 명령어 이다. 삭제된 파일이 있을경우 이명령어를 통해 add 를 한다.

## Git commit
 git commit 은 git 에 가장 기본이 되는 명령어 이다. git은 commit 단위로 변화를 저장하기 때문에 git commit 은 git add 와 다르게 신경써서 해주는 것이 좋다.
 너무 자주 할 경우 commit 이 너무 많아 복잡해 지며 너무 드물게 할경우 commit 된 내용을 잘 알 수 없다. 해당 commit 으로 상태를 돌릴 수 있으므로 어느 특정 작업이 완벽히 완료된 후에 commit 하는 것이 좋겠다. <br>
`git commit -m "first commit"`
   first commit 이라는 이름으로 commit 을 수행한다. -m 옵션은 editer를 열지 않고 바로 커밋을 수행한다.<br>
`git commit -v "second commit"`
   -v 옵션은 editer 를 열어서 해당 사항을 확인후 커밋할 수 있다.<br>
`git add -a "third commit"`
  -a 옵션은 git add 를 수행하지 않아도 바로 add 를 수행후에 commit 을 실행 해준다.
  -m 옵션과 같이 사용할 할 수 있다. <br>
`git commit --amend`
  이전에 add 된 목록들을 이전 commit 에 포함해서 다시 commit 을 수행해준다. <br>

## Checkout & Reset
`git reset HEAD readme`
  모르고 add 해서 Change to be committd 상태가 되었다면 다시 Staged 된 상태를 unStaged 상태로 변경 <br>
`git checkout --readme`
  수정된 파일을 Untracked 상태로 변경한다. <br>
