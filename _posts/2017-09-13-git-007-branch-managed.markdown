---
layout: post
title:  "Git : 07. Branch Menagement"
date:   2017-09-13 15:10:41 +0900
categories: Git
---

###### OSX를 기준으로 작성된 포스팅입니다.
### Branch 관리
<hr>
<br>

> `git branch` 명령은 단순히 브랜치를 만들고 삭제하는 것이 아니다. 아무런 옵션 없이 실행하면 branch의 목록을 보여준다.

```
$ git branch
  issue53
* master
  testing
```


`*` 기호가 붙어 있는 master branch는 현재 checkout 해서 작업 중인 branch이다. 즉, 지금 수정한 내용을 commit하면 `master branch`에 `commit`되고 포인터가 앞으로 한 단계 나아간다. <br>
`git branch -v`명령을 실행하면 branch마다 마지막 commit message도 함께 보여준다.


```
$ git branch -v
  iss53   93b412c fix javascript issue
* master  7a98805 Merge branch 'iss53'
  testing 782fd34 add scott to the author list in the readmes
```
<br><br><br>
> `--merged`명령은 이미 merge한 branch list을 확인 할 수 있다.

```
$ git branch --merged
  issue53
* master
```

`issue53 branch`는 앞에서 이미 merge했기때문에 목록에 나타난다. *가 붙어 있지 않은 branch는 `git branch -d`명령으로 삭제해도 되는 branch이다. 이미 다른 branch와 merge했기 때문에 삭제해도
정보를 잃지 않는다.
<br><br>
반대로 현재 checkout한 branch에 merge 하지 않은 branch를 살펴보려면 `git branch --no-merged`명령을 사용한다.

```
$ git branch --no-merged
  testing
```

위에는 없었던 다른 branch가 보인다. 아직 merge 하지 않은 commit을 담고 있기 때문에 `git branch -d`명령으로 삭제되지 않는다.

```
$ git branch -d testing
error: The branch 'testing' is not fully merged.
If you are sure you want to delete it, run 'git branch -D testing'.

```

merge 하지 않은 branch를 강제로 삭제하려면 -D 옵션으로 삭제한다.
