---
layout: post
title:  "Python : 10. virtualenv로 가상환경 설치하기"
date:   2017-09-17 15:10:41 +0900
categories: Python
---

virtualenv는 독립된 python 환경을 만들어주는 유용한 도구이다.
virtualenv로 가상환경 설치하는 방법을 알아보자.

## python : virtualenv로 가상환경 설치하기

> 프로젝트에 python 특정 버전을 가지는 가상환경을 구축하려고 한다.

- python 버전 매니저 - pyenv
- 버전 별 환경을 구성 플러그인 - pyenv-vertualenv

pyenv와 pyenv-virtualenv로 특정 python 버전을 갖는 환경을 구축할 수 있다.
 <br><br>

- brew가 설치되어 있지 않다면 먼저 설치해준다.

```
$ brew install pyenv
$ brew install pyenv-virtualenv
```

<br><br>

- 프로젝트 디렉토리에서 아래 명령을 실행하면, .python-version 파일이 생성되면서 프로젝트의 파이썬 버전을 정의할 수 있다.

```
$ pyenv local 3.6.2
$ pyenv virtualenv 3.6.2 venv
```

<br><br>

### 기타 명령

해당 프로젝트 디렉토리에서

- 가상환경 생성 및 Python 버전 지정

```
$ pyenv virtualenv 3.6.2 [이름]
(가상환경은 프로젝트 디렉토리가 아니라 pyenv 버전 하위에 생성되는 것에 주의한다.)
```

- pyenv 리스트 확인

```
$ pyenv versions
```

- 더 자세한 리스트 확인

```
$ pyenv virtualenvs
```

- 가상환경 생성 현재 python 환경으로

```
$ pyenv virtualenv [이름]
```

- 가상환경 사용

```
$ pyenv local 3.6.2 [이름]
```

- 가상환경 활성화/비활성화

```
$ pyenv activate [이름]
$ pyenv deactivate
```

- 가상환경 삭제

```
$ pyenv uninstall [이름]
```
