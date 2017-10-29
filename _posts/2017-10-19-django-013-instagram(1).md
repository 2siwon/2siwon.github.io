---
layout: post
title:  "Django : 13. 인스타그램(1) - 프로젝트 세팅"
date:   2017-10-19 15:10:41 +0900
categories: Django
tag: [Django]
---

# 프로젝트 세팅

```
fc-instagram
├── .config_secret
│    ├── settings.common.json
.    .	  
.    .
.    .
```

<br><br>

## .config_secret 디렉토리

1. 장고에서 `password를 해싱`할 때 settings.py에 있는 `SECRET_KEY가 key값`이 된다. 이 값이 production환경에 나가면 문제가 생길 수 있으므로 `.config_secret 안의 settings.common.json 파일`에 따로 관리해준다.
2. `.gitignore` 파일에 추가하여 git에 push 할 때 올라가지 않도록 한다.

<br><br>

### settings.py

```python
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

# fc-intagram
ROOT_DIR = os.path.dirname(BASE_DIR)

CONFIG_SECRET_DIR = os.path.join(ROOT_DIR, '.config_secret')

f = open(os.path.join(CONFIG_SECRET_DIR, 'settings_common.json'))
config_secret_common_str = f.read()
f.close()

config_secret_common = json.loads(config_secret_common_str)

SECRET_KEY = config_secret_common['django']['secret_key']
```

### settings.common.json

```json
{
  "django": {
    "secret_key": "6hlp#ck2yl@sqfrmtxw*l5gvhs#h)tbu_f@5gmxlyc2&5bl&5*"
    }
}
```

<br><br>

## DB 설정

1. python에서 postgresql을 작동하기 위해선 `psycopg2`를 설치해야한다.(pip로 설치 후 requirements.txt에 추가)
2. 어떤 DB를 사용하는지 굳이 외부로 알릴 필요도 없고 DB서버 주소또한 표적이 될 수 있으니`settings.common.json` 파일에 따로 관리하자.

#### settings.common.json

```json
{
  "django": {
    "secret_key": "6hlp#ck2yl@sqfrmtxw*l5gvhs#h)tbu_f@5gmxlyc2&5bl&5*",
    "databases": {
      "default": {
        "ENGINE": "django.db.backends.postgresql",
        "HOST": "localhost",
        "POST": "5432",
        "NAME": "instagram",
        "USER": "siwon",
        "PASSWORD": "dltldnjs"
      }
    }
  }
}
```

#### settings.py

```
DATABASES = config_secret_common['django']['databases']
```

<br><br>

## MEDIA, STATIC, TEMPLATE 설정

`media파일에 접근하기 위한 주소정보`를 `settings.py`에 적어줘야한다.(MEDIA\_URL = '/media/')
MEDIA\_URL이 하는 일은 `MEDIA파일에 접근할 때`(사용자가 업로드한 파일에 접근할 때) `URL을 도메인URL/media/이미지필드가 갖고있는 고유의 주소`를 붙여서 새로운 URL을 만들어낸다.

> 실제로는 장고를 통해서 파일을 서빙하면 속도가 굉장히 느리다. 실제 프로덕션 환경에서는 장고까지 오지않고 웹서버에서 처리를 해줘야한다.


#### settings.py

```python
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_URL = '/media/'

STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATIC_URL = '/static/'

STATICFILES_DIRS = [
    STATIC_DIR,
]

TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates')

TEMPLATES = [
    {
        'DIRS': [
            TEMPLATE_DIR,
        ],
    }
]
```

지금같은 경우는 개발환경에서 쉽게 세팅을 해볼 수 있도록 urls.py에서 staticfiles라는 정적파일을 다루게 도와주는 어플리케이션을 사용해서 특정 URL로 왔을 때 특정 폴더로 이동할 수 있는 함수를 하나 제공한다.

MEDIA\_URL = '/media/'를 적어줬지만 이미지를 post하고 눌러보면 page not found가 뜬다.<br>
그 이유는 media/ 라는 부분이 url패턴에 없기 때문이다.

장고는 들어온 URL 요청을 항상 `url Resolver(urlpattenrs)`에서 판단. `media/ 라는 부분에 대한 예외의 경우`를 만들어줘야한다. media/라는 media URL로 접근을 했을 때는 장고의 urlpatterns에서 `view를 찾는게 아니라 갖고있는 media루트에 해당하는 폴더`에서 파일을 찾아서 리턴 해줘야 한다.

아래와 같이 예외의 경우를 만들어준다.

> urlpattern중에서 settings.MEDIA\_URL로 온 요청은 document\_root에서 찾는다.

#### urls.py

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(
    settings.MEDIA_URL,
    document_root=settings.MEDIA_ROOT,
)
```
