---
layout: post
title:  "Django : 11. Authentication(2)"
date:   2017-10-17 15:10:41 +0900
categories: Django
tag: [Django]
---

장고 사용자 유저 모델은 직접 커스텀하여 사용 할 수 있지만 기본 유저 모델을 사용 할 수도 있다. 하지만 `장고시스템에서는 User model을 하나 밖에 사용하지 못한다.` 기본 유저 모델을 쓰면 커스텀을 사용 할 수 없고 커스텀 유저모델을 사용하면 기본 유저 모델을 사용하지 못한다.

<br><br>

사용자의 유저 모델인지 알아내는 방법

1. from django.contrib.auth.models import Group, Permission, User
2. from django.contrib.auth import get_user_model 메서드를 사용해서 확인할 수 있다.



`get_user_model()`이라는 메서드를 보면 django 시스템에서 쓰고있는 유저 모델이 일반 유저 모델인지 커스텀 유저모델인지 알 수 있다.

아래 코드에서 확인해보자!

```python
>>> User = get_user_model()
>>> User
django.contrib.auth.models.User
# 장고 기본으로 정의되어 있는 유저모델을 쓰고 있는 것을 알 수 있다.

>>> User.objects.create_user
<bound method UserManager.create_user of <django.contrib.auth.models.UserManager object at 0x10b5bf5f8>>
```

<br>

기본 유저 모델을 사용해서 유저 생성해보기

```python
>>> User.objects.create_user(username='abcd', is_staff=True, date_joined=timezone.now())
<User: abcd>
```

<br>

기본 필드 이외에 다른 필드를 넣으면 어떻게될까?

```python
>>> User.objects.create_user(username='abcd', is_staff=True, nickname='kickass')
<User: abcd>
TypeError : 'nickname' is an invalid keyword argument for this function

# 타입 에러 발생!
```

<br><br>

## Binding

**Form에 데이터가 바인딩 되었다!**



- POST 요청을 사용해서 양식을 제출하면 view에서 Form인스턴스를 다시 작성한다.

```python
form = SignupForm(request.POST)
```


<br><br>

**바운드 된 Form**

- 다시 작성된 form 인스턴스는 이제 **바운드 된 폼** 라고 부른다.

<br><br>

**is\_valid()**

- is\_valid()가 True면 cleaned\_data 속성에서 모든 유효성이 검사 된 Form data에 접근 할 수 있다.
- cleaned\_data는 is_\valid()를 통과하는 순간 변형하여 사용 할 수 있다. (ex - 전화번호를 받고 하이픈(-)으로 끊고 싶을 경우)

> is\_valid() 모듈 안에 있는 validation 함수들은 각 상황에 따라 쓰인다.<br>
> to\_python(), validate(), run\_validators(), clean(), clean\_\<fieldname>()


- 필드를 정의할 때는 정말로 특정한 형식의 데이터가 들어올 때를 가정한다.(ex - 핸드폰 번호)
- 이런 필드에는 필드의 존재 만으로 어떤 데이터가 들어왔을 때 자동으로 정제해서 데이터를 저장할 수 없다.
- char필드를 쓰고 입력받은 문자열이 username과 일치하는 유효성 검사는 굳이 특정 필드를 만들 필요가 없고 clean() 메서드를 사용하는 것이 좀 더 올바른 방법이다.

<br><br>

**to\_python()**

값에 접근할 때 값을 어떻게 편집해서 보여줄지에 대한 메서드

필드는 하나의 공간. 여러개의 값을 저장 할 수 없다. 그러므로 데이터가 들어갈 때는 쉼표(,) 단위로 이메일을 구분해서 저장한다. 만약에 field에 있는 데이터를 부를 때(to\_python()을 쓸 때) 값을 value.split(',')을 해서 보낸다. 그러면 가지고 있던 데이터는 쉼표(,)로 구분되어 있던 문자열이지만 돌려주는 데이터는 파이썬 list가 된다.

```python
class MultiEmailField(forms.Field):
	def to_python(self, value):
		if not value:
			return []
		return value.split(',')
```

<br><br>

**clean\_\<fieldname>()**

데이터가 binding 된 SignupForm 인스턴스를 생성할 때 forms.py 미리 특정 필드에 대해서 clean()을 한다.

forms.py

```python
def clean_username(self):
    data = self.cleaned_data['username']
    # 유저가 존재하면 faorms.ValidationError를 발생
    # 아니면 data를 리턴
    if User.objects.filter(username=data).exists():
        raise forms.ValidationError('Username already exists!')
    return data
```

> \_\_getattr\_\_(self, name)<br>
> 부른 메서드명에 대해서 동적으로 동작 할 수 있도록 안쪽에서 매직 메서드를 사용하고 있다.<br>
> 객체의 없는 속성을 참조하려 할 때 호출된다. 일반적으로 찾는 속성이 있다면 호출되지 않는다.<br>
>  '\_\_getattr\_\_` 은 인스턴스의 다른 속성에는 접근 할 수 없도록 설계 되어있다.

<br><br>
