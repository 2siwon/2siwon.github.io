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
```