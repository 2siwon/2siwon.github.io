---
layout: post
title:  "Django : 18. 인스타그램(6) - pwd확인, SignupForm리팩토링, include와 with사용 template 리팩토링, CustomUserModel"
date:   2017-10-21 15:10:41 +0900
categories: Django
tag: [Django]
---

## password확인, SignupForm 리팩토링

#### member/forms.py

```python
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

    def clean_password2(self):
        password = self.cleaned_data['password']
        password2 = self.cleaned_data['password2']
        if password != password2:
            raise forms.ValidationError('Password1 and Password2 not equal')
        return password2

    def clean(self):
        if self.is_valid():
            setattr(self, 'signup', self._signup)
        return self.cleaned_data

    def _signup(self):
        username = self.cleaned_data['username']
        password = self.cleaned_data['password']
        User.objects.create_user(
            username=username,
            password=password,
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
            user = form.signup()
            return HttpResponse(f'{user.username}, {user.password}')
        print(form.cleaned_data)
        print(form.errors)

    # GET 요청시 SignupForm인스턴스를 form 변수에 할당 context에 같은 키/값으로 전달
    else:
        form = SignupForm()
    context = {
        'form': form
    }
    return render(request, 'member/signup.html', context)
```

<br><br>

## include, with 사용 리팩토링

#### templates/include/form-horizontal.html

```html
<form action="" method="POST" class="form-horizontal">
    {% csrf_token %}
    {% for field in form %}
    <div class="form-group {% if field.errors %} has-error {% endif %}">
        <label for="{{ field.auto_id }}"
               class="col-sm-2 control-label">
            {{ field.label }}
        </label>
        <div class="col-sm-10">
            {{ field }}

            {% if field.errors %}
                {% for error in field.errors %}
                    <span class="help-block">{{ error }}</span>
                {% endfor %}
            {% endif %}
        </div>

    </div>
    {% endfor %}

    {% if form.non_field_errors %}
        {% for error in form.non_field_errors %}
            <p class="text-danger">{{ error }}</p>
        {% endfor %}
    {% endif %}
    <button class="btn btn-primary btn-block">{{ submit_text }}</button>
</form>
```

#### member/login.html

```python
{% extends 'base.html' %}

{% block content %}
<div class="login">
    {% include 'include/form-horizontal.html' with form=login_form submit_text='로그인' %}
</div>
{% endblock %}
```

#### member/signup.html

```python
{% extends 'base.html' %}

{% block content %}
<div class="signup">
    {% include 'include/form-horizontal.html' with form=signup_form submit_text='회원가입' %}
</div>

{% endblock %}
```

#### member/views.py

```python
def login(request):

	.........
    form = LoginForm()
    context = {
        'login_form': form,
    }
    return render(request, 'member/login.html', context)

...........
...........

def signup(request):
	.........
	.........
    form = SignupForm()
    else:
        form = SignupForm()
    context = {
        'signup_form': form
    }
	.........
	.........
```

<br><br>

## CustomUserModel


setting.py와 admin.py에 아래와 같이 등록해준다.

#### settings.py

```python
AUTH_USER_MODEL = 'member.User'
```

#### admin.py

```python
from django.contrib.auth.admin import UserAdmin
from .models import User

admin.site.register(User, UserAdmin)
```


#### models.py

```python
from django.contrib.auth.models import AbstractUser
from django.db import models
```
