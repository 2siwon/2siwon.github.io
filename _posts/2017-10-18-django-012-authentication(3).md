---
layout: post
title:  "Django : 12. Authentication(3)"
date:   2017-10-18 15:10:41 +0900
categories: Django
tag: [Django]
---

## Athentication in Web requests

django는 세션이랑 미들웨어(sessionMiddleware, authenticationMiddleware) 를 이용해서 `authentication` 시스템이 `request.objects`에서 내용을 찾게 되어있다.
(세션과 미들웨어를 사용하여 인증시스템을 요청 객체에 연결)

**is\_authenticated**

서버쪽에서 할당받은 쿠키가 있으면 항상 "네가 걔구나!" 라는 걸 쿠키의 만료일까지 인증 가능하다고 했다. **view에서 is_authenticated를 사용할 때 request 객체 안에 user가 들어오게 된다.** 쿠키를 보내 요청을 했을 때 서버가 그 request를 판단해서 만약 쿠키의 sessionid값이 이미 서버 쪽에 있을 경우 **해당하는 유저를 request에 넣어서(request.user속성을 제공받아) 모든 view를 사용할 수 있게 해준다.** 그렇지 않으면 `AnonymousUser의 인스턴스로 설정`된다.


```python
# 로그인에 성공했다면 template를 렌더링하는 context에 무조건 포함이 되어있다.(미들웨어에서 처리)
if request.user.is_authenticated:
	# Do something for authenticated users.
	...
else:
	# Do something for anonymous users.
	...
```

<br><br>

**Built-in template context processors**

###### django.contrib.auth.context_processors.auth

만약에 우리가 템플릿을 만드는 과정에서 어느 페이지든 간에 지금 로그인한 `user object를 항상` 쓰고싶다면?

1. view마다 context에 user object를 넣어준다. 하지만 너무 힘들다..
2. template context processors를 이용한다!

`template context processors`는 `template context에 무조건 들어가야 되는 내용`을 만들어준다!

> render()메서드를 사용할 때 첫번째 인자로 항상 request를 전달한다. 그 안에 user 인스턴스가 항상 들어가므로 렌더링되는 템플릿에서 user인스턴스를 사용할 수 있는것이다. 이 것을 가능하게 하는 것이 template context processor 이다.


<br><br>

**authenticate()**

###### From django.contrib.auth import authenticate

###### def authenticate(request=None, **credentials):

해당하는 User 객체가 있는지 인증
인증에 성공하면 self.user변수에 User객체가 할당, 실패시 None

```python
user = authenticate(
    username=username,
    password=password
)
if not self.user:
    raise forms.ValidationError(
        'Invalid login credentials!'
    )
```

<br><br>

**login()**

###### From django.contrib.auth import login

###### login(request, user, backend=None)


사용자 로그인을 위해서는 login() 메서드를 사용한다.

- Django의 Session에 해당 User정보(인스턴스)를 추가
- Response에는 SessionKey값을 Set-Cookie 헤더에 담아 보냄
- 이후 브라우저와의 요청응답에서는 로그인을 유지함

```python
From django.contrib.auth import login as django_login

if user is not None:
    django_login(request, user)
    return redirect('post_list')
else:
    return HttpResponse('Login credentials invalid')
```

<br><br>


**clean()**

`Form에 달린 clean()`과 `Field에 달린 clean()`이 있다.

`Field의 clean()`는 to\_python(), validate(), run\_validators()를 단순히 연속적으로 실행시켜주는 것이다.

`Form의 clean()`은 2개 이상의 필드를 동시에 검증해야 할 때, 모든 Field.clean() 이 후 실행된다.

<br>

`Form.clean()` 실행순서

각 필드마다 Field.clean() 실행 -> clean\_\<fieldname\> 실행 -> Form.clean() 실행

> clean\_fieldname과 다른점<br>
> clean_fieldname은 하나의 field에 대한 validation이고, clean()은 Form 자체에 대한 validation이다.


forms.py

```python
class LoginForm(forms.form):

	username = forms.CharField(
	.......
	)

	password = forms.Charfield(
		forms.PasswordInput(
	......
		),
	)

	# clean()은 모든 필드에 대한 검증이 끝난 후 실행된다.
	def clean(self):
	    cleaned_data = super().clean()
	    username = cleaned_data.get('username')
	    password = cleaned_data.get('password')

	    user = authenticate(
	        username=username,
	        password=password
	    )
	    if not user:
	        raise forms.ValidationError(
	            'Invalid login credentials!'
	        )
```

username : siwon<br>
password : dltldnjs<br>
이 user에 등록되어있다.

```python
>>> from member.forms import LoginForm
>>> form = LoginForm({'username':'siwon', 'password':'dltldnjs'})
>>> form

# 인스턴스의 현재상태
<LoginForm bound=True, valid=Unknown, fields=(username;password)>

# validation 검사
>>> form.is_valid()
True

# LoginForm에 대한 validation 통과
>>> form
<LoginForm bound=True, valid=True, fields=(username;password)>

# 필드에 대한 유효성검사 통과
>>> form.cleaned_data
{'password': 'dltldnjs', 'username': 'siwon'}

# 에러 메시지 empty string
>>> form.errors
{}
```

비밀번호를 틀리게 입력한다면?

```python
# 비밀번호를 틀리게 입력
>>> form = LoginForm({'username':'siwon','password':'dltldnjs1'})

# 현재는 is_valid()를 안했으므로 valid=Unknown 상태
>>> form
<LoginForm bound=True, valid=Unknown, fields=(username;password)>

# 비밀번호가 틀렸으므로 False
>>> form.is_valid()
False

# 필드에 대한 유효성 검사는 통과했지만 LoginForm에 대한 유효성 검사는 실패
>>> form.cleaned_data
{'password': 'dltldnjs1', 'username': 'siwon'}

>>> form
<LoginForm bound=True, valid=False, fields=(username;password)>

>>> form.errors
# all은 폼 전체에 난 에러
{'__all__': ['Invalid login credentials!']}
```
