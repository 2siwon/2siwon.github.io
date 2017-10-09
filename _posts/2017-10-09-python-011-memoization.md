---
layout: post
title:  "Python : 11. Memoization"
date:   2017-10-7 15:10:41 +0900
categories: Python
tag: [Python]
---

**memoization** 메모이제이션은 컴퓨터 프로그램이 동일한 계산을 반복해야 할 때, 이전에 계산한 값을 메모리에 저장함으로써 동일한 계산의 반복 수행을 제거하여 프로그램 실행 속도를 빠르게 하는 기술이다. 동적 계획법의 핵심이 되는 기술이다. 메모아이제이션이라고도 한다.

아래 코드는 단순한 함수에 메모이제이션을 적용한 코드이다.<br>
1. 전달받은 매개변수 x, y를 튜플로묶은 딕셔너리의 key를 생성한다.<br>
2. x, y가 처음보는 매개변수라면(cached_sum에 저장되어 있지 않은 키라면) (x + y) + 10의 결과를 해당 key를 index로 cached_sum에 저장한다.
3. x, y가 이미 cached_sum에 저장되어 있다면 cached_sum에 x, y를 튜플로 묶은 키를 index로 사용하여 이미 저장되어 있는 결과값을 리턴한다.
 
```python
import time

cached_sum = dict()
cached_mul = dict()


def sum(x, y):
    key = (x, y)
    if key not in cached_sum:
        cached_sum[key] = (x + y) + 10
    return cached_sum[key]


def mul(x, y):
    key = (x, y)
    if key not in cached_mul:
        cached_sum[key] = (x * y) + 10
    return cached_mul[key]
```

데코레이터를 사용해서 리팩토링을 해보자.

```python
def memoization(fn):
    cached = dict()
    def wrap(x, y):
        key = (x, y)
        if key not in cached:
            cached[key] = fn(x, y)
        return cached[key]
    return wrap

@memoization
def sum(x, y):
    return (x + y) + 10

@memoization
def mul(x, y):
    return (x * y) + 10
```

- 위의 함수처럼 간단한 함수가 몇번 호출되지 않을 경우에는  굳이 momoization을 사용하지 않아도 충분히 실행속도가 빠를것이다. 만약 엄청 대단한 함수를 짜서 한번 도는데 평균 1초가 걸린다고 생각해보자. 이 함수가 100000번 실행되어야 된다면? 단순하게 생각해도 엄청나게 오랜시간이 걸린다. 이럴경우 메모이제이션은 엄청난 속도 상의 성능 향상을 가져올 수 있는 기술이다.