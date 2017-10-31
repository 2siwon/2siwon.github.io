---
layout: post
title:  "Django : 16. 인스타그램(4) - forms.py에 부트스트랩 적용, 역참조(related_name), next(template), signup 구현, 유저중복검사 구현"
date:   2017-10-20 15:10:41 +0900
categories: Django
tag: [Django, Instagram]
---


## form의 widget들에 form-contorl 추가

#### forms.py

부트스트랩을 사용하기 위해서 widget에 form-control을 추가

```python
from django import forms

__all__ = (
    'PostForm'
)


class PostForm(forms.Form):
    photo = forms.ImageField(
        required=True,
        widget=forms.ClearableFileInput(
            attrs={
                'class': 'form-control',
            }
        )
    )


class CommentForm(forms.Form):
    content = forms.CharField(
        widget=forms.Textarea(
            attrs={
                'class': 'form-control',
            }
        )
    )
```

<br><br>

## related_name 지정

`related_name`을 지정해서 역참조를 더 편하게 해보자.

1. 원래는 post.postcomment_set.all로 역참조를 해야한다.
2. post.comments.all로 접근 할 수 있게 해보자.

#### models.py

```python
from django.db import models


# Create your models here.

class Post(models.Model):
    photo = models.ImageField(upload_to='photo')
    created_at = models.DateTimeField(auto_now_add=True)


class PostComment(models.Model):
    post = models.ForeignKey(
        Post,
        related_name='comments',
    )
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

```

<br><br>

## post_list에서 next를 지정해서 다시 원래위치로 돌아오도록 설정

1. comment\_create에서 리다이렉트가 post\_detail이므로 next 지정
2. 돌아오는 위치가 항상 맨 위이므로 댓글 단 위치로 돌아오도록 설정

#### post_list.html

```html
{% raw %}
{% extends 'base.html' %}

{% block content %}
<div>
    <a href="#last">라스트로 갑시다</a>
    {% for post in posts %}
    <div id="post-{{ post.pk }}">
        <a href="{% url 'post_detail' post_pk=post.pk %}">
            <img src="{{ post.photo.url }}" alt="">
        </a>

        <form action="{% url 'comment_create' post_pk=post.pk %}?next=/post/#post-comments-{{ post.pk }}" method="POST">
            {% csrf_token %}
            {{ comment_form }}
            <button class="btn btn-block btn-primary">댓글 쓰기</button>
        </form>

        <ul id="post-comments-{{ post.pk }}"> {% for comment in post.comments.all %}
            <li>{{ comment.content }}</li>
            {% endfor %}
        </ul>
    </div>
    {% endfor %}
    <div id="last">Last</div>
</div>


{% endblock %}
{% endraw %}
```

#### comment_create

```python
def comment_create(request, post_pk):
    post = get_object_or_404(Post, pk=post_pk)
    if request.method == "POST":
        form = CommentForm(request.POST)
        if form.is_valid():
            PostComment.objects.create(
                post=post,
                content=form.cleaned_data['content']
            )
            next = request.GET.get('next')
            if next:
                return redirect(next)
            return redirect('post_detail', post_pk=post_pk)

```

<br><br>

## signup 구현

#### urls.py

```python
    # Member
    url(r'^member/signup/$',
        member_views.signup,
        name='signup'),
```

#### member/views.py

```python
from django.http import HttpResponse
from django.shortcuts import render
from django.contrib.auth import get_user_model

User = get_user_model()

def signup(request):
    if request.method == "POST":
        username = request.POST['username']
        password = request.POST['password']

        if username and password:
            user = User.objects.create_user(
                username=username,
                password=password
            )
        return HttpResponse(f'{user.username}, {user.password}')

    return render(request, 'member/signup.html')
```

#### member/signup.html

```html
{% raw %}
{% extends 'base.html' %}

{% block content %}
<div class="signup">
    <form action="" method="POST">
        {% csrf_token %}
        <label>Username <input type="text" name="username"></label>
        <label>Password <input type="password" name="password"></label>

        <button class="btn btn-primary btn-block">회원가입</button>
    </form>
</div>

{% endblock %}
{% endraw %}
```

<br><br>

## 유저 중복 검사 기능 추가

#### member/views.py

```python
def signup(request):
    if request.method == "POST":
        username = request.POST['username']
        password = request.POST['password']

        if username and password:
            # 이미 username항목이 주어진 username값으로 존재하는 User가 있는지 검사
            if User.objects.filter(username=username).exists():
                return HttpResponse(f'Username {username} is already exist')
            # username, password가 주어졌고 중복되는 User가 없다면 User생성
            user = User.objects.create_user(
                username=username,
                password=password
            )
        return HttpResponse(f'{user.username}, {user.password}')

    return render(request, 'member/signup.html')
```
