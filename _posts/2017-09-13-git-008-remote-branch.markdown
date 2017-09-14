---
layout: post
title:  "Git : 08. Remote branch"
date:   2017-09-14 15:10:41 +0900
categories: Git
---
###### OSX를 기준으로 작성된 포스팅입니다.
### Remote branch
<hr><br>

> 리모트 Refs는 리모트 저장소에 있는 포인터인 레퍼런스다. 리모트 Ref가 있지만 보통은 remote tracking branch를 사용한다.

#### remote tracking branch
`remote tracking branch`는 remote branch를 추적하는 브랜치이다. 이 브랜치는 로컬에 있지만 움직일 수 없다. `remote server`에 연결될 때마다 `remote branch`에 따라서 자동으로 움직일 뿐이다. <br>
`remote tracking branch`는 일종의 북마크이며 `remote reposiory`에 **연결했던 순간**에 branch가 무슨 commit을 가리키고 있었는지를 나타낸다.
<br>
<br><br>
`remote branch`의 이름은 `(remote) / (branch)` 형식으로 되어 있다. 예를 들어 `remote repository origin`의 `master branch`를 보고싶다면 `origin/master`라는 이름으로 branch를 확인하면 된다. 다른 팀원과 함께 어떤 issue를 구현할 때 그 팀원이 `issue53 branch`를 서버로 push 했고 내 로컬에도 `issue53 branch`가 있다고 가정하자. 이때 서버(remote server)의 `issue53 branch`가 가리키는 commit은 로컬에서 `origin/issue53`이 가리키는 commit이다.

`git.ourcompabny.com`이라는 git server	가 있고 이 서버의 저장소를 하나 clone하면 git은 자동으로 `origin`이라는 이름을 붙인다. `origin`으로부터 저장소 데이터를 모두 내려받고 `master branch`를 가리키는 포인터를 만든다. 이 포인터는 `origin/master`라고 부르고 멋대로 조종할 수 없다. 그리고 git은 `로컬 master branch`가 `origin/master`와 `같은 commit을 가리키게 한다. 이제 이 `master branch`에서 작업을 시작할 수 있다.<br><br>


#### Clone 이후 remote server의 repository와 local의 master branch
![remote branch](https://git-scm.com/book/en/v2/images/remote-branches-1.png)

<br><br>

#### local과 remote server의 commit history는 독립적이다.
로컬 저장소에서 어떤 작업을 하고 있는데 다른 팀원이 `git.outcompay.com`서버에 push 하고 `master branch`를 업데이트한다. 그러면 이제 팀원 간의 히스토리는 서로 달라진다. `remote server`의 `repository`로부터 어떠한 데이터도 주고받지 않아서 `origin/master` 포인터는 그대로이다.
![remote branch](https://git-scm.com/book/en/v2/images/remote-branches-2.png)


<br><br>

#### git fetch로 remote branch 정보 업데이트
`remote server`로부터 `repository` 정보를 동기화 하기위해서는 `git fetch origin`명령을 사용한다. 명령을 사용하면 현재 `local repository`가 갖고 있지 않은 새로운 정보를 모두 내려받고, `update`한다. 그리고 `remote branch(origin/master)`의 포인터 위치를 `최신 commit`으로 이동시킨다.<br><br>

다시한번 말하지만 `remote branch`는 `remote server`와 `git fetch [remote server name]`을 이용하여 업데이트해야지만 `commit`을 옮길 수 있다.<br><br>
![remote branch](https://git-scm.com/book/en/v2/images/remote-branches-3.png)
<br><br>

#### server를 remote respository로 추가

remote repository를 여러 개 운영하는 상황을 이해할 수 있도록 개발용으로 사용할 git 저장소를 팀 내부에 하나 추가해보자. 이 저장소의 주소가 `git.teaml1.outcompany`이며 `git remote add`명령을 사용하여 현재 작업 중인 팀의 저장소를 추가한다. 이름을 `teamone`으로 짓고 긴 서버 주소 대신 사용한다.

```
git remote add teamone git://git.team1.outcompany.com
```

![remote branch](https://git-scm.com/book/en/v2/images/remote-branches-4.png)

<br><br>




#### 'teamone/master'의 remote tracking branch
`teamone server`를 `remote repository`로 추가 하고나면 `git fetch teamone` 명령으로 teamone 서버의 데이터를 내려받는다.

```
git fetch teamone
```

명령을 실행해도 `teamone server`의 데이터는 모두 `origin server`에도 있는 것들이라서 아무것도 내려받지 않는다. 하지만 이 명령은 `remote tracking branch`인 `team/master`가 `teamone server`의 `master branch`가 가리키는 commit을 가리키게 된다.
![remote branch](https://git-scm.com/book/en/v2/images/remote-branches-5.png)
<br><br><br>

### push 하기
<hr><br>
local의 branch를 `remote server`로 **전송**하려면 **쓰기 권한**이 있는 `remote repository`에 `push` 해야 한다. local repository의 branch는 자동으로 리모트 저장소로 전송되지 않는다. 명시적으로 branch를  `push` 해야 정보가 전송된다. 따라서 remote repository에 전송하지 않고 local branch에만 두는 **비공개 branch**를 만들 수 있다. 또 다른 사람과 협업하기 위한 branch를 따로 만들어 그 브랜치만 전송할 수도 있다.
