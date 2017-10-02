---
layout: post
title:  "Python : 01. 시퀀스 타입"
date:   2017-09-15 15:10:41 +0900
categories: Python
---

파이썬에 내장된 **Sequence-type** 에는 `문자열`, `리스트`, `튜플`이 있다.
문자열은 인용부호(작은따옴표, 큰따옴표)를 사용하며, 리스트는 대괄호[], 튜플은 괄호()를 사용하여 나타낸다.
시퀀스 타입의 객체는 인덱스 연산을 통해 내부 항목에 접근 가능하다.

### 리스트(List)
<hr><br>
리스트는 순차적인 데이터를 나타내는 데 유용하며, 문자열과는 달리 내부 항목 변경이 가능하다.
<br><br>
**리스트의 생성**

```
>>> empty_list1 = []
>>> empty_list2 = list()
>>> sample_list1 = ['a','b','c','d']
>>> sample_list2 = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
```

<br><br>

#### 다른 데이터를 리스트로 변환
#### list 함수를 사용

```
>>> list('League of legends')
['L', 'e', 'a', 'g', 'u', 'e', ' ', 'o', 'f', ' ', 'l', 'e', 'g', 'e', 'n', 'd', 's']
```

이 외에도 리스트로 변환 가능한 타입에서 사용가능하다.

#### 리스트 항목 추가 (append)

```
>>> sample_list1.append('e')
>>> print(sample_list1)
['a','b','c','d','e']
```

#### 리스트 병합(extend, +=)

```
>>> fruits = ['apple', 'banana', 'melon']
>>> colors = ['red' ,'green', 'blue']
>>> fruits.extend(colors)
>>> fruits
['apple', 'banana', 'melon', 'red', 'green',' blue']
```

```
>>> fruits = ['apple', 'banana', 'melon']
>>> colors = ['red' ,'green', 'blue']
>>>fruits += colors
>>>fruits
['apple', 'banana', 'melon', 'red', 'green',' blue']
```
<br>
#### 특정 위치에 리스트 항목 추가 (insert)

```
list1 = list()
list1.insert(1, 'mango')
list1.insert(100, 'pineapple')
```
<br>
#### 특정위치에 리스트 항목 삭제 (del)
파이썬 구문 del을 사용
>del은 리스트 함수가 아닌, 파이썬 구문이므로 `del <list>[offset]` 형식을 사용한다.

```
>>> del fruits[0]
```

<br>
#### 값으로 리스트 항목 삭제 (remove)
```
>>> fruits.remove('mango')
```

<br>
#### 리스트 항목 추출 후 삭제 (pop)

```
>>> fruits.pop()
>>> fruits.pop(-3)
```

<br>
#### 값으로 리스트 항목 오프셋 찾기 (index)
```
>>> fruits.index('red')
```

<br>
#### 존재여부 확인 (in)

```
>>> 'red' in fruits
True
```

<br>
값 세기(count)

```
>>> fruits.append('red')
>>> fruits.append('red')
>>> fruits.count('red')
3
```

정렬하기 (sort, sorted)

* sort는 리스트 자체를 정렬
* sorted는 리스트의 정렬 복사본을 반환

리스트 복사 (copy)

* copy 함수
* list 함수
* 슬라이스 연산[:]

### 튜플(tuple)

<hr><br>

튜플은 리스트와 비슷하나, 정의 후 내부 항목의 삭제나 수정이 불가능하다.

<br><br>

#### 튜플 생성

```
>>> empty_tuple = ()
```

```
>>> colors = 'red',
>>> fruits = 'apple', 'banana'
```

튜플을 정의할 때는 괄호가 없어도 무관하나, 괄호로 묶는것이 좀 더 튜플임을 구분하기 좋다.
또한, 튜플의 요소가 1개일 때는 요소의 뒤에 **쉼표(,)**를 붙여야 한다.

<br><br>

#### 튜플 언패킹 (tuple unpacking)
좌측에 여러 개의 변수를 선언하며 언패킹한다.

```
>>> f1, f2 = fruits
>>> f1
apple
>>> f2
banana
```
<br><br>

#### 형 변환
tuple 함수를 사용(튜플 생성에는 사용 불가능)
리스트를 튜플로 변환

#### 튜플을 사용하는 이유
* 리스트보다 적은 메모리 사용
* 정의 후 변하지 않는 내부 값
