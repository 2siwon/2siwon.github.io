---
layout: post
title:  "Django : 03. Migrations"
date:   2017-10-7 15:10:41 +0900
categories: Django
tag: [Django]
---

**migrations** 는 `django-south` 프로젝트가 킥스타터 펀딩을 통해, Django 1.7부터 포함되었다. 1.7이 전에는 syncdb라는 커맨드를 사용했는데 `syncdb` 는 migration이라고 부를 수 없고 간단한 모델 내역을 생성해주었다. 특정 모델이 있으면 모델에 맵핑되는 테이블이 없을 때만 생성을 해준다. 즉, 테이블에 변경이 발생했을 경우 대응을 하지 못하는 문제점이 있다. migrations는 모델 변경 내역에 대한 히스토리 관리가 가능한 기능이다.

<br><br>

**관련 명령**

```shell
# 마이그레이션 파일 생성
$ manage.py makemigrations [app-name] 	
# 마이그레이션 적용
$ manage.py migrate [app-name]			

# 마이그레이션 적용 현황
$ manage.py showmigartions [app-name]
# 지정 마이그레이션의 SQL 내역
$ manage.py sqlmigrate [app-name] [migration-name]
```

<br><br>

**마이그레이션 파일 생성 및 적용**

1. 마이그레이션 파일 (초안) 생성하기 : makemigrations 명령
2. 해당 마이그레이션 파일을 DB에 반영하기 : migrate 명령

- 마이그레이션은 두 단계로 나눠진다.

1. django 모델의 모델 내역을 변경하고 makemigrations을 하면 migration file이 생성된다.
2. 이후 migrate를 하면 데이터베이스에 반영된다.

**모델 내역을 변경하면 자동으로 데이터베이스에 반영되면 편할텐데 왜 두 단계를 거쳐서 진행될까?**


예를들어 title이라는 필드의 이름을 바꾸고 싶은데 자동으로 DB에 반영된다고 생각해보자. title필드의 이름을 name으로 바꾸었다면 시스템에 따라 두 가지 해석이 존재할 수 있다. 처음에 의도한 대로 title을 name으로 rename하는 것. title필드를 지운 후 name필드를 생성하는 것. 후자를 선택할 경우 그 동안의 title필드의 모든 정보가 다 날아가버린다. 참 끔찍한 일이다.. 그러므로 django에서는 migration file이라는 일종의 작업지시서를 만든 후 필히 개발자에게 확인을 받은 후 DB에 적용하는 것이다.
