---
layout: post
title:  "Python : 09. 네임드튜플"
date:   2017-09-17 15:10:41 +0900
categories: Python
---

namedtuple은 클래스를 명시할 때 인스턴스 변수만 있는 것 처럼 사용할 수 있는 tuple이다.
namedtuple을 사용하면 튜플에서 각 위치의 의미를 명시적으로 작성할 수 있다.


```python
>>> from collections import namedtuple
>>> Station = namedtuple("Station", "id, city, state, lat, long")
>>> denver = Station(44, "Denver", "CO", 40, 105)
>>> denver
Station(id=44, city='Denver', state='CO', lat=40, long=105)
>>> denver.city
'Denver'
>>> denver[1]
'Denver'
```
