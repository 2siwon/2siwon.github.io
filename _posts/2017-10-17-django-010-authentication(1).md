---
layout: post
title:  "Django : 10. Authentication(1)"
date:   2017-10-17 15:10:41 +0900
categories: Django
tag: [Django]
---

**authentication** authentication은 사용자 인증, 이 사용자가 있는지 없는지 판단한다. authorization은 해당 유저의 권한을 나타낸다. authentication은 이 둘을 모두 의미하는 단어이다.

<br><br>

## overview

**auth system**

- User
- Permission(Boolean) - 사용자의 권한
- Group - 둘 이상의 사용자에게 권한을 적용하는 방법, 사용자마다 일반사용자그룹, 관리자그룹, 스태프그룹 등 권한을 적용하는 방법이다.
- A configurable password hashing system
- Forms and view tools for logging in users, or restricting content - Form과 View에서 쓰는 도구들
- A pluggable backend system - 인증 백엔드 시스템(ex - 페이스북 유저 로그인 api), 사용자가 자기 자신을 인증하는 방법, 기본적으로 있는 백엔드 시스템은 username, password사용하는 백엔드 시스템이 있고 특정 SNS 로그인, password없이 로그인 등 여러가지 루틴들은 백엔드 시스템을 직접 만들어서 붙여아한다.

<br><br>

**auth 서드파티 패키지**

django의 인증시스템은 매우 일반적인 것들만 지원한다. 웹 인증 시스템에서 흔히 볼 수 있는 기능을 제공하지 않는다. 이 때 서드파티 패키지가 solution 중 하나이다.

- password strength checking
- Throttling of login attemypespts
- Authentication against third-parties(ex - facebook)

<br><br>

**Cookie, session**

HTTP 프로토콜은 Connectionless, stateliss한 프로토콜이다. 만약 한번 요청을 한 후 끊겼을 때 다음에 요청을 보낼 때 "나 누구야" 라고 상태를 가질 수 없다. 만약 로그인을 유지하고 싶다면 문제가
되는 것이다. 이 때 사용하는 게 session과 cookie이다.


cookie - 쿠키에는 이름, 값, 만료 날짜, 경로 정보가 들어있다. 자동로그인을 할 때, 체크하지 않으면 만료시간이 0이다. 0은 그 브라우저를 닫는 순간 쿠키가 사라진다. 쿠키는 항상 HttpHeader에 key값을 담아서 요청한다. 서버는 로그인 요청을 받았을 경우 고유한 key를 만들고 쿠키의 value(서버쪽에서 승인 받을 key)를 가지는 세션을 만든다.  그리고 서버의 자신의 메모리(django db)에 그 데이터를 저장을 한다.

session - 이런 session(key, value)은 어떤 유저인지를 판단하는 기준이된다.

로그인요청을 보낼 때 username, password를 보낸다 -> 서버쪽에서 승인 -> 서버쪽에서 사용자를 특정할 수 있는 고유값을 hash해서 만듬, 고유값을 판단할 수 있는 key값을 클라이언트에 전달 -> 클라이언트는 받은 key값과 추가정보(expire\_date등..)를 추가해서 자신의 쿠키에 저장

> tip -  chrome의 secret tap을 사용하면 세션유지가 불가능하다.

<br><br>

**Installation**

기본적인 설치 및 설정은 django-admin startproject에 의해 생성된 settings.py -> INSTALLED\_APPS에 되어있다.

1. `django.contrib.auth` 는 인증 프레임 워크의 핵심과 기본 모델 포함
2. `django.contrib.contenttypes` 은 장고 내용 유형 시스템으로, 만들 모델과 관련된 권한을 허용(모델을 만들면 모델 한개에 대한 contenttype이 생김, 이를 응용해서 어떤 모델에 대해서 사용자가 이 모델의 수정, 쓰기, 삭제 등을 사용할 때 contenttype을 사용한다.)

그리고 auth, contenttypes 는 두 개의 MIDDLEWARE에서 돌아간다.

1. sessionMiddleware - 세션값을 관리해주는 미들웨어. 세션에 대한 정보가 django db에 저장된다. 만약 서버를 껐다켜는 순간 모든 쿠키가 날아간다.
2. AuthenticationMiddle - sessionID로 특정 유저를 판단하는데 있어서 좀 더 쉽게 판단 할 수 있게 하는 미들웨어

이 설정을 적용한 상태(settings.py -> INSTALLED\_APPS에 두 가지 미들웨어 등록)에서 manage.py migrate를 할 경우 인증 관련 모델 및 권한에 필요한 db 테이블(django\_session)이 생성된다.

<br><br>

## django authentication 사용하기

기본적인 것 외에는 extension과 customization을 해야한다.

**User objects**

일반적으로 사이트와 상호 작용하는 사람들을 말한다. 엑세스 제한, 사용자 프로필 등록, 제작자와 컨텐츠 연결 등을 가능하게 한다. `superusers`나 admin `staff`도 마찬가지로 특별한 속성이 설정된 **같은 User object** 일 뿐이다.

default 유저의 주요 속성

- username
- password
- email
- first_name
- last_name

주요한 것일 뿐이며 실제로는 더 많이 갖는다.

<br><br>

**Creating Users**

create\_user() 함수를 사용하여 유저를 생성한다.<br>
하지만 objects.create()가 있는데 왜 create\_user()가 따로있을까?

objects.create() 같은 경우에는 db에 그 내용을 바로 넣기때문에 hash된 password가 아닌 raw-password가 들어가게된다. create_user()에는 hash기능이 들어가 있다.

<br><br>

**create_user()**

지금까지 models.Manager가 데이터베이스에 접근할 수 있는 권한을 가진 객체였다. 하지만 user를 다루는 manager는 별개로 있다. user는 authentication, hashing, create user등 여러가지 기능을 담당한다.
