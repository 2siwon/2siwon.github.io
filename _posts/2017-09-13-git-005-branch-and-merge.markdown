---
layout: post
title:  "Git : 05. Branching and Mergeing"
date:   2017-09-13 15:10:41 +0900
categories: Git
---



###### OSX을 기준으로 작성된 포스팅입니다.
### Branch 와 Merge

> 실제 개발환경에서 겪을 만한 예제를 보자. `Branch` 와 `Merge`는 보통 이런 식으로 진행한다.

1. 작업 중인 웹사이트가 있다.
2. `새로운 이슈(issue53)`를 처리할 `issue53 Branch`를 하나 생성한다.
3. `issue53 Branch`에서 `작업을 진행`한다.

> 이때 중요한 문제가 생겨서 그것을 해결하는 `Hotfix branch`를 먼저 만들어야 한다. 그러면 아래와 같이 할 수 있다.

1. 새로운 이슈를 처리하기 위해 `이전의 운영(production, master) branch`로 `이동`한다.
2. `hotfix branch`를 새로 하나 `생성`한다.
3. 수정한 `Hotfix 테스트`를 마치고 `master branch로 Merge` 한다.
4. 다시 작업하던 `issue53 branch`로 옮겨가서 하던 일 진행한다.

<hr><br><br>

#### Branch

> 현재 `working directory`에서 이전에 `commit`을 몇 번 했다고 가정한다.

![branch](https://git-scm.com/book/en/v2/images/basic-branching-1.png)

<br><br><br>

> `issue53`에 집중할 수 있기 위해 `issue53`를 처리하기 위한 `issue53 branch`를 생성한다.

```
$ git branch issue53
$ git checkout issue53
```


```
$ git checkout -b issue53
Switcghed to a new branch "issue53"
```
둘 중 하나를 입력한다. `checkout`에 `-b옵션`을 주면 `branch 생성`과 `생성된 branch에 checkout`을 하는 두 명령을 한 라인으로 할 수 있다.

![branch](https://git-scm.com/book/en/v2/images/basic-branching-2.png)



<br><br><br>

> `hotfix branch`를 `checkout`했기 때문에(즉, `HEAD는 issue53 branch`를 가리킨다.) 뭔가 일을 하고 `commit`하면 `issue53 branch`가 앞서 나간다.

```
$ vim index.html
$ git commit -a -m 'added a new footer [issue53];
```

![branch](https://git-scm.com/book/en/v2/images/basic-branching-3.png)

만약에 `또 다른 issue`가 생긴다면 git에서는 다시 `HEAD를 master branch(production branch)`로 `checkout`해서 `새로운 issue에 대한 branch를 생성`하면 된다. <br><br>
하지만 그 전에 `working directory`를 정리하지 않으면 작업 중이던 `issue53 branch`와 `checkout 해야할 branch`인 `master(production) branch`가 하나 이상 같은 파일을 공유하고 그 파일이 수정되었을 경우 `conflict`가 일어나게 된다. 이 때 `working directory`의 작업 파일들을 `commit하여 정리`하거나 `stash를 사용`하여 작업을 저장하여 정리한 후 `master branch`로 `checkout`할 수 있다. <br><br><br>

>이 예제에서는 `working directory`에 있는 `모든 파일(untracted files, tracked files)`을 `commit`하고 `master branch`로 `checkout`하기로 하자.


```
$ git checkout master
Switched to branch master
```

그러면 `issue53`을 수정하기 위한 `issue53 branch`를 만들기 이전 모습으로 돌아가기 때문에 새로운 문제에 집중할 수 있는 환경이 만들어진다.

<br><br><br>

> 현재 `issue53`에서 `master로 checkout`을 하였고, `master branch`를 부모로 하는 `hotfix branch`를 만들고 `checkout`하였다. 이제 `hotfix branch`를 **문제해결 때까지 사용**한다.
>
> 즉, `HEAD`의 포인터는 현재 `hotfix`를 가리키고 있다.


```
$ git checkout -b hotfix
Switchged to a new branch 'hotfix'
$ vim indexmhtml
$ git commit -a -m 'fixed the broken email address'
[hotfix 1fb7853] fixed the broken email address
 1 file changed, 2 insertions(+)
```

![branch](https://git-scm.com/book/en/v2/images/basic-branching-4.png)

<br><br><br>


> `hotfix issue를 해결` 한 후 `master branch`에 다시 적용하려면 문제를 제대로 고쳤는지 테스트하고 `master branch`와 `merge`를 해야한다.

```
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
 index.html | 2 ++
 1 file changed, 2 insertions(+)
```
`merge`메시지에서 `fast-forward`가 보인다. `C4 commit`이 `C2 commit`에 기반한 `branch`이기 때문에 `branch pointer`는 그저 `최신 commit으로 이동`한다.

![branch](https://git-scm.com/book/en/v2/images/basic-branching-5.png)

<br><br><br>

> 급한 문제를 해결하고 `master branch에 적용`했다. 이제 더 이상 필요없는 `hotfix branch는 삭제`하자.

```
$ git branch -d hotfix
Deleted branch hotfix (38o0874c).
```

> 이제 다시 처음 `issue53`을 처리하던 `issue53 branch`로 되돌아가서 작업을 진행하자.

```
$ git checkout issue53
Switched to branch "issue53"

$ vim index.html
$ git commit -a -m 'finished the new footer [issue 53]'
[iss53 ad82d7a] finished the new footer [issue 53]
1 file changed, 1 insertion(+)
```

![branch](https://git-scm.com/book/en/v2/images/basic-branching-6.png)

현재 HEAD 포인터는 issue53을 가리키고 있다. `git merge master` 명령으로 `master branch`를 `issue53 branch`에 `Merge`하면 `issue53 branch`에 `hotfix`가 적용된다.
`issue53 branch`가 `master`에 `Merge` 할 수 있는 수준이 될 때까지 기다렸다가 `Merge` 하면 `hotfix`와 `issue53` 브랜치가 합쳐진다.

<br><br><br>

#### Merge

> `issue53` 구현을 마치고 `master branch`에 `merge`하는 과정을 살펴보자. `issue53 branch`를 `master branch`에 `merge`하는 것은 앞서 살펴본 `hotfix branch`를 `merge`하는 것과 비슷하다. `git merge 명령`으로 `합칠 branch`에서 `합쳐질 branch`를 `merge`하면 된다.

```
$ git checkout master
Switched to branch 'master'
$ git merge iss53
Merge made by the 'recursive' strategy.
README |    1 +
1 file changed, 1 insertion(+)
```

현재 `master branch`가 가리키는 `commit`이 `merge`할 `branch`의 조상이 아니므로 `Git`은 `fast-forward`로 `merge` 하지 않는다. 이 경우에 `Git`은 각 `branch`가 가리키는 `commit 두 개와 공통 조상 하나`를 사용하여 `3-way-Merge`를 한다.

![merge](https://git-scm.com/book/en/v2/images/basic-merging-1.png)
<br><br><br>

> 단순히 `branch pointer`를 `최신 commit`으로 옮기는 것(`fast-foward`)이 아니라 `3-way-merge`의 결과를 `별도의 commit`으로 만들고 나서 `해당 branch`가 `그 commit을 가리키도록 이동`시킨다. <br><br>
그래서 이런 `commit`은 `부모가 여러 개`고 `merge commit`이라고 부른다.

![merge](https://git-scm.com/book/en/v2/images/basic-merging-2.png)

<br><br><br>

`issue53 branch`는 문제를 해결했으니 더 이상 필요하지 않다. 삭제해보자.

```
$ git branch -d issue53
```

<br><br><br>
