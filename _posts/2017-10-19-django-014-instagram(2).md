---
layout: post
title:  "Django : 14. 인스타그램(2) - Post앱 생성"
date:   2017-10-19 15:10:41 +0900
categories: Django
tag: [Django, Instagram]
---


## post앱 생성 및 Post, PostComment 모델 추가

파이썬에서 `이미지를 처리`하기 위해선 `pillow` 를 설치해야 한다. `pillow`는 파이썬 이미지 라이브러리로써 여러 이미지 파일 포맷을 지원하고, 이미지 내부 데이터에 엑세스 할 수 있게하며, 다양한 이미지 처리 기능을 제공한다. 설치 후 requirements.txt에 등록해준다.

1. pip install pillow
2. pip freeze > requirements.txt
3. settings.py -> INSTALLED_APPS에 등록

> upload_to는 실제 파일이 데이터베이스에 저장되는 게 아니다. 실제로 데이터베이스 내용을 보면 `상대경로(우리가 지정한 미디어루트를 기준으로 한 경로)`가 저장되어 있다.


### models.py  

```	python
from django.db import models


# Create your models here.

class Post(models.Model):
    photo = models.ImageField(upload_to='photo')
    created_at = models.DateTimeField(auto_now_add=True)


class PostComment(models.Model):
    post = models.ForeignKey(Post)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

```

### settings.py

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'post',

    'django_extensions',
]
```
