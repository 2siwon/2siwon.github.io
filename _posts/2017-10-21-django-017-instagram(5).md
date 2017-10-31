---
layout: post
title:  "Django : 17. 인스타그램(5) - SignupForm, field custom validation, 로그인(LoginForm)"
date:   2017-10-21 15:10:41 +0900
categories: Django
tag: [Django, Instagram]
---

## SignupForm을 사용

#### member/forms.py

```python
from django import forms


class SignupForm(forms.Form):
    username = forms.CharField(
        widget=forms.TextInput(
            attrs={
                'class': 'form-control'
            }
        )
    )
    password = forms.CharField(
        widget=forms.PasswordInput(
            attrs={
                'class': 'form-control'
            }
        )
    )
```

#### member/views.py

```python
def signup(request):
    if request.method == "POST":
        # 데이터가 binding된 SignupForm인스턴스를 생성
        form = SignupForm(request.POST)
        # 해당 form이 자신의 필드에 유효한 데이터를 가지고 있는지 유효성 검사
        if form.is_valid():
            # 통과한 경우 정제된 데이터 (cleaned_data)에서 username과 password 항목을 가져옴
            username = form.cleaned_data['username']
            password = form.cleaned_data['password']

            # 이미 username항목이 주어진 username값으로 존재하는 User가 있는지 검사
            if User.objects.filter(username=username).exists():
                return HttpResponse(f'Username {username} is already exist')
            # username, password가 주어졌고 중복되는 User가 없다면 User생성
            user = User.objects.create_user(
                username=username,
                password=password
            )
            return HttpResponse(f'{user.username}, {user.password}')
    # GET 요청시 SignupForm인스턴스를 form 변수에 할당 context에 같은 키/값으로 전달
    form = SignupForm()
    context = {
        'form': form
    }
    return render(request, 'member/signup.html', context)
```

#### member/signup.html

```html
{% raw %}
{% extends 'base.html' %}

{% block content %}
<div class="signup">
    <form action="" method="POST">
        {% csrf_token %}
        {{ form }}
        <button class="btn btn-primary btn-block">회원가입</button>
    </form>
</div>

{% endblock %}
{% endraw %}
```

<br><br>

## username field custom validation

#### member/forms.py

views.py에 있던 username field 중복 검사를 forms.py안의 메소드로 넣어서 is_valid()와 동시에 실행되도록 한다.

```python
from django import forms
from django.contrib.auth import get_user_model

User = get_user_model()


class SignupForm(forms.Form):
    username = forms.CharField(
        widget=forms.TextInput(
            attrs={
                'class': 'form-control'
            }
        )
    )
    password = forms.CharField(
        required=False,
        widget=forms.PasswordInput(
            attrs={
                'class': 'form-control'
            }
        )
    )

    # is_valid() 할 때 아래 있는 메소드가 한번 돈다.
    # clean_<field_name>
    def clean_username(self):
        data = self.cleaned_data['username']
        if User.objects.filter(username=data).exists():
            raise forms.ValidationError('Username already exists!')
        return data
```

<br><br>

## login 기능 구현

#### urls.py

```python
    url(r'^member/login/$',
        member_views.login,
        name='login'),
```

#### member/views.py

```python
...
from django.shortcuts import render, redirect
from django.contrib.auth import get_user_model, authenticate, login as django_login
...

...
def login(request):
    # POST 요청(Form submit)의 경우
    if request.method == "POST":
        username = request.POST['username']
        password = request.POST['password']
        # 해당하는 User가 있는지 검증
        user = authenticate(
            username=username,
            password=password,
        )
        # 인증에 성공하면 user변수에 User객체가 할당 실패시 None
        if user is not None:
            # Django의 Session에 해당 User정보를 추가,
            # Response에는 SessionKey값을 Set-Cookie 헤더에 담아 보냄
            # 이후 브라우저와의 요청응답에서는 로그인을 유지함
            django_login(request, user)
            return redirect('post_list')
        else:
            return HttpResponse('Login credentials invalid!')
    else:
        # GET요청에서는 Form을 보여줌
        return render(request, 'member/login.html')
...
```

#### templates/base.html

```html
{% raw %}
...
    <div class="container">
        <a href="{% url 'post_list' %}"><h1>Instagram</h1></a>
        <p>
            <div> {{ user }} </div>
            {% if user.is_authenticated %}
                {{ user.username }} 님 환영합니다! [로그아웃]
            {% else %}
                로그인해주세요
            {% endif %}
        </p>
        <hr>
        {% block content %}
        {% endblock %}
    </div>
...
{% endraw %}
```

#### templates/member/login.html

```html
{% raw %}
{% extends 'base.html' %}

{% block content %}
<div>

    <form action="" method="POST">
        {% csrf_token %}
        <input type="text" name="username">
        <input type="password" name="password">
        <button class="btn btn-primary btn-block">로그인</button>
    </form>

</div>
{% endblock%}
{% endraw %}
```

<br><br>

## Form에 인증 및 로그인 기능을 포함

views.py에 있던 validation 기능들을 forms.py로 옮김

#### forms.py

```python
class LoginForm(forms.Form):
    username = forms.CharField(
        widget=forms.TextInput(
            attrs={
                'class': 'form-control'
            }
        )
    )

    password = forms.CharField(
        widget=forms.PasswordInput(
            attrs={
                'class': 'form-control'
            }
        )
    )

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.user = None

    def clean(self):
        cleaned_data = super().clean()
        username = cleaned_data.get('username')
        password = cleaned_data.get('password')

        self.user = authenticate(
            username=username,
            password=password
        )

        if not self.user:
            raise forms.ValidationError(
                'Invalid login credentials!'
            )
        else:
            # 자기자신에 login() 메서드 추가
            setattr(self, 'login', self._login)

    def _login(self, request):
        django_login(request, self.user)
```

#### views.py

이제 views.py는 단순히 폼에 대한 validation check만 진행 후 로그인

```python
def login(request):
    # POST 요청(Form submit)의 경우
    if request.method == "POST":
        form = LoginForm(request.POST)
        if form.is_valid():
            form.login(request)
            return redirect('post_list')
        else:
            return HttpResponse('Login credentials invalid!')
    else:
        # GET요청에서는 Form을 보여줌
        return render(request, 'member/login.html')
```
