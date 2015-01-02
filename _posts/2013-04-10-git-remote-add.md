---
layout: post
title: "Git remote add and Branch"
description: "git remote"
category: ETC
tags: [Git]
---
## GIT 리모트 
Git은 `git remote` 를 사용해 로컬 repository 를 외부에 있는 repository와 연결 한다.
외부에 있는 repository 는 여러개 일 수 있고 물리적으로 다른 Git repository 일수 있다.
즉 Git remote 란 ( 로컬 repository <-> 외부 repository ) 간에 연결 통로 역활을 해주는 것으로 이해 할 수 있다.
리모트를 추가하기 위해서 아래와 같이 하면 된다.
> $ git remote add (remote-name) (remote-url) <br>

- `remote-name` : remote 의 이름이다. 해당 이름으로 pull fetch push 등을 수행한다.
remote-name 의 기본값은 보통 origin 으로 설정하고 특별하게 추가할 경우 마음에 드는 이름을 추가 하면된다. 
- `remote-url` : git repository 가 위차한 url 이다. Github 에서는 https:// ssh:// 두 방식다 지원한다. <br>
ex) git remote add origin https://github.com/username/git-repo.git

> $ git remote -v  <br>
remote 로 등록된 정보를 출력해준다. <br>
> $ git remote show (remote-name) <br>
특정 remote-name 에 대한 정보를 출력한다. <br>
> $ git rename (remote-name) (new-remote-name) <br>
remote-name 이름을 new-remote-name 으로 변경한다. <br>

## Git Branch
Git 에는 repository에 여러개의 Branch를 갖을 수 있다. Branch 는 하나의 작업 단위로써 기본적으로 master branch 와 개인이 만드는 develop hotfix 등등 branch를 생성해서 사용 할 수 있다. <br>
브랜치(tracking branch)를 생성하기 위해서는 <br>
> git branch (branch-name) (remote-name/remote-branch-name) 
remote repository 에 있는 branch 를 로컬 branch로 만든다. (remote-branch -> local-branch)
- 'branch-name' : 로컬에 새로 생성할 branch 이름이다. 보통 remote 에 있는 repository의 이름과 똑같이 맞추는게 좋지만 다르게 할 수 도 있다.
- 'remote-name' : 여러 remote-name중 사용할 이름을 지정한다.
- 'remote-branch-name' : remote repository 에서 local 로 가져올 remote 에 있는 branch <br>
`git branch -v` 를 통해서 현재 있는 branch 들의 정보를 확인 할 수 있다. <br>

> ex) git branch develop origin/develop <br>

origin 으로 연결된 remote repository 에 develop 브랜치를 가져와 로컬에 새로운 develop branch 를 생성한다.<br>
`git remote show origin` 으로 origin 에 대한 정보를 보면
기존 pull 에대해서 (master <-> origin/master) 뿐아니라 (develop<->origin/develop) 도 추가적으로 연결 되 있는 것을 알수 있다. master branch 에서 pull 할경우 origin/master 에서 정보를 가져오며 develop 브랜치에서 pull 할경우 origin/develop 에서 정보를 가져온다.
<br>
Branch를 원격에서 생성하지 않고 로컬에 있는 branch 를 기준으로 생성 할 수 도 있다.
`git branch (branch-name)` 을 통해 현재에 있는 branch 를 기준으로 새로운 branch를 생성한다.

## Git checkout 
Git checkout 은 branch 간에 이동하기 위해서 사용한다.
`git checkout develop` 을 통해 develop branch 로 이동할 수 있다. 만약 develop branch 가 존재 하지 않는다면 이동 할 수 없다.<br>
`git checkout -b develop` 을 통해 branch가 없을경우 branch를 만들면서 해당 branch로 이동이 가능하다.

## Git push
Git push 는 remote 를 통해서 local repository 의 정보를 remote repository 로 올린다.
> $ git push (remote-name) (local-branch-name:remote-branch-name)
- `remote-name` : push 를 수행할 remote 이름을 정해준다.
- `local-branch-name` : push를 수행할 local 의 브랜치
- `remote-branch-name` : push를 받을 remote 의 브랜치

> ex) git push origin develop <br>

보통 remote-branch-name 을 생략하고 단순히 develop 으로 사용한다.(보통 local-branch-name 과 remote-branch-name 이 같기 때문이다.) 만약에 branch 이름이 다른데도 원격 branch-name을 지정해 주지 않았다면 local-branch-name 으로 새로 원격 브랜치를 만들거나 만약 해당 branch가 이미 있었다면 그 branch로 push를 수행하게 된다. <br>
즉 가능하면 local-branch-name 과 remote-branch-name은 같게 하는게 정신 건강에 좋다!

## Git pull
Git pull 은 remote-branch에 있는 정보를 local-branch로 가져온다.
> $ git pull (remote-name) (remote-branch-name:local-branch-name)
- `remote-name` : pull 를 수행할 remote 이름을 정해준다.
- `remote-branch-name` : pull을 할 remote 의 브랜치
- `local-branch-name` : pull이 수행되는 local 의 브랜치

> ex) git pull origin develop <br>

origin remote 로 develop 원격 브랜치를 pull 해온다. 여기서 local-branch-name 을 지정해 주지 않을경우 현재 사용하고 있는 branch에 pull 을 수행한다. 현재 master branch 에 있지만 develop branch에 pull 을 하고 싶을경우 `git pull origin develop:develop` 을 사용하여야 한다.
만약 local-branch-name 이 실제 존재 하지 않는것을 지정해줄 경우 해당 local-branch 를 자동으로 만들게 된다. 단 remote-branch-name 은 실제 존재하지 않는다면 에러가 발생한다.




