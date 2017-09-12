---
layout: post
title:  "Git : 기초"
date:   2017-09-12 15:10:41 +0900
categories: Git
---

### Git 저장소(Repository) 만들기

`Git` 저장소를 만드는 방법은 두 가지다. `기존 프로젝트나 디렉토리`를 `Git 저장소`로 만드는 방법과 다른 서버에 있는 `저장소`를 `Clone`하는 방법이 있다.
<hr><br><br>
##### 기존 디렉토리를 Git 저장소로 만들기
원하는 프로젝트의 디렉토리로 이동한다.

```
$ cd /Users/user/your_repository
```

그리고 아래 명령어를 입력한다.
```
$ git init
```

##### 기존 저장소를 Clone 하기
다른 프로젝트에 Contribute 하거나 Git 저장소를 복사하고 싶을 때 clone 명령을 사용한다.
2siwon 라이브러리 소스코드를 clone 하려면 다음과 같이 실행한다.
```
$ git clone https://github.com/2siwon/2siwon
```

아래 명령어는 디렉토리 이름이 mylibgit 이라는 것만 제외하면 앞에 사용한 명령어와 동일하다.
```
$ git clone https://github.com/2siwon/2siwon mylibgit
```

<br>
### 리모트 저장소

다른 사람과 협업을 위해서 리모트 저장소가 필요하다 저장소를 관리하면서 데이터를 `push`혹은 `pull` 하는 것이다. 저장소 관리는 저장소의 추가, 삭제와 브랜치를 관리하고 추적할지 말지 등을 관리하는 것이다.
<hr><br><br>
#### 리모트 저장소 확인하기

`git remote` 명령으로 현재 프로젝트에 등록된 저장소를 확인 할 수 있다. 보통 저장소를 clone 하게 될 경우 origin이라는 이름으로 등록된다.

```
$ git clone git://github.com/2siwon/Git-practice.git
```
`-v` 옵션은 단축이름과 URL을 볼 수 있다. 또한 다른 사람의 Contributions를 쉽게 가져올 수 있다.

```
$ git remote -v
origin git://github.com/2siwon/Git-practice.git (fetch)
origin git://github.com/2siwon/Git-practice.git (push)
```

#### 리모트 저장소를 Pull 하거나 Fetch 하기

```
$ git fetch [remote-name]
```

`fetch` 명령어는 리모트 저장소에 있는 모든 데이터를 가져온다. 그리고 나면 리모트 저장소의 모든 브랜치를 로컬에서 접근 가능한 상태가 된다. 그러면 내용을 살펴보거나 언제든지 머지를 할 수 있다.

`fetch` 명령은 리모트 저장소의 모든 데이터를 모두 로컬로 가져오지만, 자동으로 `merge`하지 않는다. `fetch` 후에는 수동으로 `merge` 해야한다.

```
$ git pull
```
하지만 `git pull` 명령은 리모트 저장소 브랜치에서 데이터를 가져올 뿐 아니라 자동으로 로컬 브랜치와 머지시킬 수 있다. 먼저 `git clone` 명령은 자동으로 로컬의 master 브랜치가 리모트 저장소의 master 브랜치를 추적하도록 한다. 그리고 `git pull` 명령은 clone한 서버에서 데이터를 가져오고 그 데이터를 자동으로 현재 작업하는 코드와 `merge` 시킨다.

#### 리모트 저장소에 push하기

프로젝트를 공유하고 싶을 경우 push 명령을 이용한다.

```
git push [리모트 저장소 이름] [브랜치 이름]
```
`master` 브랜치를 `origin`서버에 push하려면 아래와 같이 서버에 push 한다.<br>
(clone 하면 보통 자동으로 origin 이름이 생성된다.)

```
$ git push origin master
```

#### 리모트 저장소 이름 바꾸기

```
$ git remote rename [원래 이름] [바꿀 이름]	 
```

#### 리모트 저장소 삭제하기

```
$ git remote rm [저장소 이름]
```
