---
layout: post
title:  "Django : 15. 인스타그램(3)"
date:   2017-10-19 15:10:41 +0900
categories: Django
tag: [Django]
---

## post의 list 구현

#### urls.py

```python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
+   url(r'^post/$', views.post_list, name='post_list'),
]
```

#### views.py

```python
from django.shortcuts import render
from .models import Post

def post_list(request):
    posts = Post.objects.all()

    context = {
        'posts' : posts,
    }

    return render(request, 'post/post_list.html', context)
```

<br><br>

## post_create view 구현

#### views.py

POST일 경우

1. 이미지 파일은 POST가 아니라 FILES로 받는다.
2. 전달 받은 photo를 매개변수로 post 생성
3. 이미지 출력
4. post_list 페이지로 가보면 순서대로 출력된 결과를 볼 수 있다.

else

post_create 페이지를 그대로 렌더링해준다.

```python
def post_create(request):
    photo = request.FILES.get('photo')
    if request.method == "POST" and photo:
        photo = request.FILES['photo']
        post = Post.objects.create(photo=photo)
        return HttpResponse(f'<img src="{post.photo.url}">')
    else:
        return render(request, 'post/post_create.html')

```

#### post_create.html

```python
<form action="" method="POST" enctype="multipart/form-data">
    {% csrf_token %}
    <input type="file" name="photo">
    <button>글을 쓰자</button>
</form>
```

<br><br>

## Post 생성에 PostForm 사용

#### views.py

1. POST 요청의 경우 PostForm인스턴스 생성과정에서 request.POST, request.FILES을 사용
2. form 생성과정에서 전달된 데이터들이 Form의 모든 field들에 유효한지 검사(is\_valid())
3. 유효할 경우 Post인스턴스를 생성 및 저장
4. 유효하지 않으면 'Form invalid!'를 출력
5. GET 요청의 경우, 빈 PostForm인스턴스를 생성해서 템플릿에 전달


```python
def post_create(request):
    if request.method == "POST":

        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            print(form.cleaned_data)
            post = Post.objects.create(
                photo=form.cleaned_data['photo'])
            return HttpResponse(f'<img src="{post.photo.url}">')
        else:
            return HttpResponse('Form invalid!')
    else:
        return render(request, 'post/post_create.html')
```

#### forms.py

```python
from django import forms

__all__ = (
    'PostForm'
)


class PostForm(forms.Form):
    photo = forms.ImageField()
```

<br><br>

## form으로 post_create.html의 input 요소 자동 생성

#### views.py

```python
def post_create(request):
    if request.method == "POST":
        # POST요청의 경우 PostForm인스턴스 생성과정에서 request.POST, request.FILES를 사용
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            print(form.cleaned_data)
            post = Post.objects.create(
                photo=form.cleaned_data['photo'])
            return HttpResponse(f'<img src="{post.photo.url}">')
        else:
            # GET요청의 경우, 빈 PostForm인스턴스를 생성해서 템플릿에 전달
            return HttpResponse('Form invalid!')
    else:
        form = PostForm()
        context = {
            'form': form,
        }
        return render(request, 'post/post_create.html', context)
```

#### post_create.html

form_create.html

```python
<form action="" method="POST" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form }}
    <button>글을 쓰자</button>


</form>
```

<br><br>

## post_detail 구현, base.html로 리팩토링, URL 404 처리

#### urls.py

```python
    url(r'^post/(?P<post_pk>\d+)/$',
        views.post_detail,
        name='post_detail'),
```

#### views.py

없는 페이지 URL접근 시 404처리

```python

def post_detail(request, post_pk):
        post = get_object_or_404(Post, pk=post_pk)
        context = {
            'post': post,
        }
        return render(request, 'post/post_detail.html', context)
```

#### base.html

post_detail외의 나머지 페이지들도 똑같이 리팩토링 해준다.

```html
{% load static %}
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Instagram</title>
    <link rel="stylesheet" href="{% static 'bootstrap/css/bootstrap.css' %}">
</head>
<body>
    <div class="container">
        <h1>Instagram</h1>
        <hr>
        {% block content %}
        {% endblock %}
    </div>
</body>
</html>
```

#### post_detail.html

```html
{% extends 'base.html' %}

{% block content %}
<div>
    {% if post.photo %}
    <img src="{{ post.photo.url }}" alt="">
    {% else %}
    <h3>이미지 파일이 없습니다</h3>
    {% endif %}
</div>
{% endblock %}
```

<br><br>

## comment_create 구현

#### urls.py

```python
    url(r'^post/(?P<post_pk>\d+)/comment/create/$',
        views.comment_create,
        name='comment_create'),
```

#### views.py

URL get parameter로 온 'post_pk'에 해당하는 Post instance를 'post' 변수에 할당.<br>
찾지 못하면 리턴

```python
def comment_create(request, post_pk):
    post = get_object_or_404(Post, pk=post_pk)
    if request.method == "POST":
        form = CommentForm(request.POST)
        if form.is_valid():
            comment = PostComment.objects.create(
                post=post,
                content=form.cleaned_data['content']
            )
            return redirect('post_detail', pk=post_pk)
    else:
        form = CommentForm()
        context = {
            'form': form
        }
        return render(request, 'post/comment_create.html', context)
```

#### comment_create.html

```html
{% extends 'base.html' %}

{%block content %}
<div>
    <form action="" method="POST">
        {% csrf_token %}
        {{ form }}
        <button>댓글 쓰기</button>
    </form>

</div>
{% endblock %}
```

<br><br>
