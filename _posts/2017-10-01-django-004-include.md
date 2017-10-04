---
layout: post
title:  "Django : 04. include 함수 이용 URL 연결하기"
date:   2017-10-1 15:10:41 +0900
categories: Django
---


django는 새로운 Application을 생성하고 views에서 만든 모듈을 화면에 뿌려주기 위해서 최상위 urlconf에서 연결 해줘야한다.<br>

그리고 보통 **localhost/polls/** 과 같은 주소값을 갖는다.<br>
polls라는 Application을 생성하고 url 연결을 한다고 해보자.

- project_name/config/urls.py

```
from django.conf.urls import url
from django.contrib import admin

from blog.views import question_detail

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^polls/(?P<pk>\d+)/$', question_detail, name='question_detail'),
]
```

이렇게 최상위 urlconf에서 urlpattern들을 적다보면 앱이 하나씩 추가될수록 코드가 지저분해지고 관리하기 수월하지 않을 수 있다. 이때 **include** 함수를 사용하여 리팩토링을 할 수 있다.<br><br>

- project_name/config/urls.py

```
from django.conf.urls import url
from django.contrib import admin

from blog.views import question_detail

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^polls/', include('polls.urls')),
]
```

- project_name/polls/urls.py

```
from django.conf.urls import url
from .views import question_detail, vote

urlpatterns = [
    url(r'^(?P<pk>\d+)/$', question_detail, name='question_detail'),
    url(r'^(?P<pk>\d+)/vote/$', vote, name='vote'),
]
```

이렇게 해당 Application 디렉토리 안에 urlconf를 할 수 있게 **urls.py** 파일을 만들어주고 프로젝트 최상위 urlconf(urls.py)에서는 해당 모듈을 바라보게 설정해준다.
