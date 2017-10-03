---
layout: post
title:  "Django : 03. pip freeze로 패키지 한번에 설치하기(개발환경 복구하기)"
date:   2017-09-25 15:10:41 +0900
categories: Django
---


개발환경이 바뀌었을 때를 생각해보자. 일일이 패키지를 설치해줘야 한다. <br>
이때 일일이 설치하지 않고 패키지들을 기록해놓을 수 있는 명령어가 pip freeze 명령어이다.

pip를 이용해 django를 설치하고 pip freeze명령어를 입력해보자.

```
$ pip install django
```

```
$ pip freeze
Django==1.11.5
pytz==2017.2
```

<br><br>

django패키지가 설치되고 버전이 출력된다. <br>
pip freeze > requirements.txt 명령을 입력하면 requirements.txt파일에 출력결과를 저장 할 수 있다.

```
$ pip freeze > requirements.txt
```

<br><br>

기록해놓은 파일을 이용해서 다시 개발환경 복구를 해보기 위해 django를 삭제하고 다시 설치해보자.

```
$ pip uninstall django
```

```
$ pip install -r requirements.txt
```

requirements.txt에 있는 내용을 가지고 자동으로 패키지를 설치해줌으로써 해당 프로젝트가 어떤 버전의 패키지를 썼는지 기억하지 않아도 개발환경을 셋팅 할 수 있다. github 저장소에서 프로젝트를 clone한 사람도 해당 파일이 있으면 가상환경 하나 만든 후 바로 세팅이 가능하다. <br>

**다만 python 버전이 몇인지는 모르니 README.md파일에 명시를 해주는 게 좋다.**
