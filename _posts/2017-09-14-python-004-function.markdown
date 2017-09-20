---
layout: post
title:  "Python : 04. 함수"
date:   2017-09-14 15:10:41 +0900
categories: Python
---

## 함수

반복적인 작업을 하는 코드를 재사용이 가능하게 정의해 놓은 것.

```
def 함수명(매개변수[parameters]) :
		동작
```

<br><br>

### 함수의 정의, 실행

```
# 실행 시 'call func'를 print하는 함수 정의
>>> def func():
...   print('call func')
...

# 함수 자체는 function객체를 참조하는 변수
>>> func
<function func at 0x10dabf378>

# 함수를 실행시키기 위해 () 사용
>>> func()
call func
```

<br><br>

### 리턴값이 있는 함수 정의

```
>>> def return_true():
...   return True
...
>>> return_true()
True
```

함수의 결과로 Bool값을 갖는 데이터를 리턴하여 if문이나 while문의 조건으로 사용 가능하다.

### 매개변수를 사용하는 함수 정의

```
>>> def print_arguments(something):
...   print(something)
...
>>> print_arguments('ABC')
ABC
```

<br><br>

### 매개변수(parameter)와 인자(argument)의 차이

함수 내부에서 함수에게 전달되어 온 변수는 매개변수라 부르며, 함수를 호출할 때 전달하는 변수는 인자로 부른다.

```
# 함수 정의때는 매개변수
def func(매개변수1, 매개변수2):
  ...

# 함수 호출시에는 인자
>>> func(인자1, 인자2)
```
<br><br>

### 리턴값이 없을 경우

함수에서 리턴해 주는 값이 없을 경우, 아무것도 없다는 뜻을 가진 `None`객체를 얻는다.

<br><br>

### 위치 인자(Positional arguments)

매개변수의 순서대로 인자를 전달하여 사용하는 경우

```
>>> def student(name, age, gender):
...   return {'name': name, 'age': age, 'gender': gender}
...
>>> student('hanyeong.lee', 30, 'male')
{'name': 'hanyeong.lee', 'age': 30, 'gender': 'male'}
```

<br><br>

### 키워드 인자(Keyword arguments)

매개변수의 이름을 지정하여 인자로 전달하여 사용하는 경우

```
>>> student(age=30, name='hanyeong.lee', gender='male')
{'name': 'hanyeong.lee', 'age': 30, 'gender': 'male'}
```

위치인자와 키워드인자를 동시에 쓴다면, 위치인자가 먼저 와야 한다.

<br><br>

### 기본 매개변수값 지정

인자가 제공되지 않을 경우, 기본 매개변수로 사용할 값을 지정할 수 있다.

```
>>> def student(name, age, gender, cls='WPS'):
...   return {'name': name, 'age': age, 'gender': gender, 'class': cls}
...
>>> student('hanyeong.lee', 30, 'male')
{'name': 'hanyeong.lee', 'age': 30, 'gender': 'male', 'class': 'WPS'}
```

<br><br>

### 기본 매개변수값의 정의 시점

> 기본 매개변수 값은 함수가 실행될 때 마다 계산되지 않고, 함수가 정의되는 시점에 계산되어 계속해서 사용된다.

```
>>> def return_list(value, result=[]):
...   result.append(value)
...   return result
...
>>> return_list('apple')
['apple']
>>> return_list('banana')
['apple', 'banana']
```

함수가 실행되는 시점에 기본 매개변수 값을 계산하기 위해, 아래와 같이 바꿔준다.

```
>>> def return_list(value, result=None):
...   if result is None:
...     result = []
...   result.append(value)
...   return result
...
>>> return_list('apple')
['apple']
>>> return_list('banana')
['banana']
```

<br><br>

### 위치인자 묶음

함수에 위치인자로 주어진 변수들의 묶음은 `*매개변수명`으로 사용할 수 있다.
관용적으로 `*args`를 사용한다.

```
def print_args(*args) :
	print(args)
```

<br><br>

### 키워드인자 묶음

함수에 키워드인자로 주어진 변수들의 묶음은 `**매개변수명`으로 사용할 수 있다, 관용적으로 `**kwargs`를 사용한다.

```
def print_kwargs(**kwargs):
  print(kwargs)
```

<br><br>

### docstring

함수를 정의한 문서 역할을 한다.
함수 정의 후, 몸체의 시작부분을 문자열로 작성하며, 여러줄로도 작성 가능하다.

```
>>> def print_args(*args):
... 	`Print positional argments`
...		print(args)
...
>>> help(print_args)
```

<br><br>

### 함수를 인자로 전달

파이썬에서는 함수 역시 다른 객체와 동등하게 취급되므로, 함수에서 인자로 함수를 전달, 실행, 리턴하는 형태로 프로그래밍이 가가능하다.

<br><br>

### 내부 함수

함수 안에서 또 다른 함수를 정의해 사용할 수 있다.

<br><br>

### 스코프(영역)

파이썬에서는 코드 작성 시, 각 함수마다 독립적인 스코프(영역)을 가진다.<br>
메인 프로그램(현재 동작하는 프로그램의 최상위 위치)의 영역은 전역 영역(global scope)라고 하며, 전역 스코프 내부에서 독립적인 영역을 갖고 있는 경우에는 지역 영역(local scope)라고 부른다.<br><br>
아래 코드의 경우, `show_global_champion`함수 내부의 영역은 별개의 로컬 스코프를 가지며, `champion`변수는 전역 영역의 것을 가져와 출력하는 것을 볼 수 있다.

```
champion = 'Lux'
def show_global_champion():
	print('show_global_champion : {}'.format(champion))

show_global_champion()
print('print champion : {}'.format(champion))
```

위 코드를 아래와 같이 변경 후, 실행 해 본다.

```
champion = 'Lux'

def show_global_champion():
    print('show_global_champion : {}'.format(champion))

def change_global_champion():
    print('before change_global_champion : {}'.format(champion))
    champion = 'Ahri'
    print('after change_global_champion : {}'.format(champion))

show_global_champion()
change_global_champion()
```
