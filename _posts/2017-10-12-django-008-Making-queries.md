---
layout: post
title:  "Django : 07. Making queries(1)"
date:   2017-10-12 15:10:41 +0900
categories: Django
tag: [Django]
---

**Making queries** 데이터 모델을 생성하면 Django는 자동으로 객체를 생성, 검색, 업데이트 및 삭제할 수 있는 데이터베이스 추상화 API를 제공한다. API를 사용하는 방법을 알아보자. 자세한 내용은 [데이터 모델 레퍼런스](data model reference)을 참조하자.

<br><br>

models.py

```python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):              # __unicode__ on Python 2
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):              # __unicode__ on Python 2
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    n_comments = models.IntegerField()
    n_pingbacks = models.IntegerField()
    rating = models.IntegerField()

    def __str__(self):              # __unicode__ on Python 2
        return self.headline
```

<br><br>

**Creating objects**

오브젝트 생성방법<br>
(save()할 때까지 db접근안함)

```python
>>> from blog.models import Blog
>>> b = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')
>>> b.save()
```

<br>

변경사항 저장

```python
b5.name = 'New name'
b5.save()
```

SQL의 UPDATE 문과 동일하다.

<br><br>

**ForeignKey와 ManyToManyField 필드 저장하기**

ForeignKey

```python
>>> from blog.models import Blog, Entry
>>> entry = Entry.objects.get(pk=1)
>>> cheese_blog = Blog.objects.get(name="Cheddar Talk")
>>> entry.blog = cheese_blog
>>> entry.save()
```

<br><br>

Many-to-many

```python
>>> from blog.models import Author
>>> joe = Author.objects.create(name="Joe")
>>> entry.authors.add(joe)
```

이 때는 중간 모델이 자동으로 생성된다. making\_queries\_entry\_authors

<br><br>

**한번에 여러개의 레코드를 추가하는 쿼리문 만들기**

```python
>>> john = Author.objects.create(name="John")
>>> paul = Author.objects.create(name="Paul")
>>> george = Author.objects.create(name="George")
>>> ringo = Author.objects.create(name="Ringo")
>>> entry.authors.add(john, paul, george, ringo)
```

<br><br>

**오브젝트 가져오기**

모델 클래스의 '매니저'를 이용하여 QuerySet을 만든다.<br>
쿼리셋은 db에 있는 객체들의 모음이다.<br>
쿼리셋은 SELECT문과 같으며 filter는 WHERE나 LIMIT과 같다.<br>

또한 매니저는 모델 클래스에서만 접근 가능하다. 객체에서의 접근은 불가능하다.


```python
>>> Blog.objects
<django.db.models.manager.Manager object at ...>
>>> b = Blog(name='Foo', tagline='Bar')
>>> b.objects
Traceback:
    ...
AttributeError: "Manager isn't accessible via Blog instances."
```

<br><br>

**모든 데이터 가져오기**

DB에 있는 모든 객체들의 쿼리셋을 가져온다.

```python
all_entries = Entry.objects.all()
```

<br><br>

**필터로 특정 객체 가져오기**

all()은 모든 객체를 가져오지만 QuerySet을 구체화하여 특정 객체만 가져오려고 한다.<br><br>

filter(\**kwargs)<br>
지정된 파라미터와 일치하는 객체가 포함된 QuerySet을 가져온다.

```python
# DB에 요청 한번
Entry.objects.filter(pub_date__year=2006)

# DB에 요청 두번
Entry.objects.all().filter(pub_date__year=2006)
```

exclude(\**kwargs)<br>
지정된 파라미터와 일치하지 않는 객체만 포함된 QuerySet을 가져온다.

```python
Entry.objects.exclude(pub_date__year=2006)
```

<br><br>

**Chaining filters**


QuerySet의 변환값은 QuerySet이므로 다시 새로운 요청을 할 수 있다.<br>
아래는 headline이 'What'으로 시작되는 쿼리셋에서 pub\_date가 오늘 이후인 쿼리셋 중에 pub\_date가 '2005-01-30'인 쿼리셋을 return한다.

```python
>>> Entry.objects.filter(
...     headline__startswith='What'
... ).exclude(
...     pub_date__gte=datetime.date.today()
... ).filter(
...     pub_date__gte=datetime.date(2005, 1, 30)
... )
```

<br><br>

**Field lookups**

filter(), exclude(), get()과 같은 QuerySet 메소드들의 키워드 인자로 질의문을 구체화한다.<br>
lookup 키워드 인자 형식은 'field\_\_lookuptype=value' 이다.

```python
>>> Entry.objects.filter(pub_date__lte='2006-01-01')
```

위의 QuerySet은 아래 SQL문과 같은 쿼리문을 DB에 요청한다.

```python
SELECT * FROM blog_entry WHERE pub_date <= '2006-01-01';
```
