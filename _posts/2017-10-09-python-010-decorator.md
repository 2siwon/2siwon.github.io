---
layout: post
title:  "Python : 10. Decorator"
date:   2017-10-7 15:10:41 +0900
categories: Python
tag: [Python]
---

**Decorator** 데코레이터를 알기 위해서는 우선 1급 함수에 대한 이해가 필요하다. 1급 함수는 함수를 동적으로 생성하고 생성한 함수를 반환값으로 전달이 가능 할 때 1급 함수라고 한다. 파이썬은 이러한 1급 함수를 지원한다. 아래의 예제들을 보자.

```python
>>> fn1 = lambda x,y : x + y
>>> f1(1, 2)
>>> 3
``` 

```python
>>> fn2 = lambda x,y : x + y + 10
>>> f2(1, 2)
>>> 13
```

```python
>>> base = 10
>>> f3 = lambda x,y : x + y + base
>>> f3(1, 2)
>>> 13
```

아래 예제와 같이 람다도 함수처럼 묶어서 사용 가능하다.

```python
>>> def mysum():
...	 	fn = lambda x,y : x + y
...		return = fn
>>>
>>> myfn = mysum()
>>> myfn(1, 2)
>>> 3
>>> 
>>> mysum()(1, 2)
>>> 3 
```

```
>>> lambda x,y : x + y
>>> <function __main__.<lambda>>
```

```
>>> (lambda x,y : x + y)(1, 2)
>>> 3
```

- 이렇듯 1급 함수는 함수를 변수처럼 취급해서 사용 할 수 있다. 그러므로 함수도 변수처럼 동적으로 인자로 넘길 수도 있고 리턴값을 넘길 수도 있다.

<br><br>

base\_10 함수는 매개변수로 함수를 받아서 그 함수의 리턴값에 10을 더해주는 함수이다.<br>
base\_10 함수가 호출되면 그 즉시 wrap이라는 함수가 정의되고 정의된 wrap함수가 리턴이 된다. <br>
mysum 함수는 매개변수를 2개 받아 더해주는 함수이다. 이 함수 자체를 base\_10함수에 넘겨주고 반환 값을 함수변수(?)에 담아 사용이 가능한 것이다.

```python
def base_10(fn):
	def wrap(x, y):
		return fn(x, y) + 10
	return wrap
	
def mysum(x, y):
	return x + y
	
mywrap = base_10(my_sum)
```

```python
>>> mywrap(1, 2)
>>> 13
```

<br><br>

이제 데코레이터를 사용해보자! 간단하다 위의 코드를 아래 코드로 바꿔주면 된다.

```python
def base_10(fn):
	def wrap(x, y):
		return fn(x, y) + 10
	return wrap
	
@base_10
def mysum(x, y):
	return x + y
```

```
>>> mysum(1, 2)
>>> 13
```

<br><br>

즉, 장식자는 특정 함수를 객체로 받는 함수로써 객체지향적인 개념에서 중요한 재사용성에 부합하는 기능이다.

<br><br><br><br>

**인자를 받는 데코레이터**

데코레이터 자체에 인자를 받아서 사용 할 수도 있다.

```python
def base(base_i):
    def outer(fn):
        def wrap(x, y):
            return fn(x, y) + base_i

        return wrap

    return outer


@base(30)
def mysum(x, y):
    return x + y
```

```python
>>> mysum(1, 2)
>>> 33
```

