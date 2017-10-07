---
layout: post
title:  "Django : 01. 장고 튜토리얼"
date:   2017-09-25 15:10:41 +0900
categories: Django
tag: [Django]
---

django 의 프로젝트 구조, 새 프로젝트 구성방법, MTV pattern 등을 알아보자.


## requirements

- Python 3.6

## 프로젝트 구조

```python
projects/
	django/ # Django project들
		djangogirls_project/ # Django project folder
			.git
			.gitignore
			  Django, Python, Linux, macOS,
			  PyCharm
			  # Custom
			  .idea/
			.python-version (pyenv local)
			requirements.txt

			djangogirls/ # Django application folder
				manage.py
				djangogirls/ # Django settings folder
					__init__.py
					settings.py
					urls.py
					wsgi.py
```

## 새 프로젝트 구성

1. 프로젝트 디렉토리 생성
2. git init
3. .gitignore 파일 생성(python, pycharm, macOS, django) -> .idea/ 추가
4. pyenv virtualenv 3.6.2 [이름] - 가상환경생성
5. pyenv local [이름] - 생성한 가상환경 디렉토리에 설정
6. pip install django
7. GitHub에 [이름]저장소 추가
8. git freeze > requirements.txt 파일 생성
9. 로컬 git에 GitHub remote저장소 추가
10. pycharm에서 django-admin startproject [프로젝트 이름]
11. config로 디렉토리 이름 refactor(적용 안될 시 pycharm 재시작)
12. settings.py -> INSTALLED_APPS에서 [프로젝트 이름]추가
13. PyCharm으로 해당 프로젝트 폴더 open후 interpreter설정
  macOS: /usr/local/var/pyenv/versions/





### MVC pattern

Model-View-Controller pattern

> Django: Model-Template-View, MTV

- Model: Data (DB)
- View: 사용자에게 제공되는 화면(또는 기능)
	- Template (HTML)

- Controller: Model과 View사이에서 데이터를 가공하는 역할 <- urlresolver에 연결
	- View (Python function)

### Request와 Response간에 일어나는 일

1. 사용자의 요청이 server에 도달 (URL로의 HTTP요청)
2. server는 해당 요청 URL을 Django에 전달
3. Django는 전달받은 URL을 urlresolver로 분석해서 작업을 처리할 Controller(View)에 연결
4. Controller는 요청을 받아 사용자에게 제공할 View(Template)를 응답으로 리턴
5. server는 리턴받은 응답을 사용자에게 전달
