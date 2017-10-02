---
layout: post
title:  "Python : 03. 제어문"
date:   2017-09-15 15:10:41 +0900
categories: Python
---

for문을 사용하면 시퀀스형 데이터(String, List, Tuple)등을 순회하면서 접근하여 사용 할 수 있다.

## for문 (조건에 따른 순회)
<br>
### 기본형태

시퀀스형 데이터(String, List, Tuple)를 순회하고자 할 때 사용한다.

```
for 항목 in 순회가능(iterable)객체 :
	<항목을 사용한 코드>
```

iterable한 객체에는 String, Tuple, Dictionary, Set 등이 있다.

```
>>> champion_list = ['lux', 'ahri', 'ezreal', 'zed']
>>> for champion in champion_list:
...   print(champion)
...
lux
ahri
ezreal
zed
```

딕셔너리에서 키나 값을 순회할 때는, iterable한 객체의 위치에 dict.keys()나 dict.values()를 사용한다.
키, 값을 모두 순회할 때는 dict.items()를 사용한다.

<br><br>

### 중첩

```
for 항목1 in iterable객체1:
  iterable객체1을 순회하며 실행할 코드
  for 항목2 in iterable객체2:
    iterable객체1 내부에서 새로운 iterable객체2를 순회하며 실행할 코드
```

<br><br>

### 중단하기 (break)
데이터를 순회하던 중, 특정 조건에서 순회를 멈추고 반복문을 빠져나갈 때 사용한다.

```
for 항목 in iterable객체:
  (반복문을 중단하고 싶을때)break
```

<br><br>
### 건너뛰기 (continue)
데이터를 순회하던 중, 반복문을 중단하지 않고 다음 반복으로 건너뛸 때 사용한다.

```
for 항목 in iterable객체:
  (현재의 반복을 중간에 그만두고 다음 반복으로 건너뛰고 싶을 때)continue
```

<br><br>
### break확인 (else)
for문으로 데이터를 순회하던 중, break문이 호출되지 않고 반복문이 종료되면 else문의 실행된다.

```
for 항목 in iterable객체 :
	pass
else :
	break가 한 번도 호출되지 않았을 경우의 코드
```

<br>br>
### 여러 시퀀스 동시순회 (zip)

```
>>> fruits = ['apple', 'banana', 'melon']
>>> colors = ['red', 'yellow', 'green', 'purple']
>>> for fruit, color in zip(fruits, colors):
...   print('fruit:', fruit, ' color:', color)
...
fruit: apple  color: red
fruit: banana  color: yellow
fruit: melon  color: green
```
zip으로 묶은 시퀀스들 중, 가장 짧은 시퀀스가 완료되면 순회가 종료된다.<br>
zip을 사용하면 여러 시퀀스로부터 튜플을 만들 수 있다.

```
>>> list(zip(fruits, colors))
```
zip으로 변환되는 것은 리스트가 아닌 zip클래스 형태의 iterable객체이기 때문에, 리스트 형태로 사용하려면 list()함수를 사용해준다. <br>
dict()함수를 사용할 경우 딕셔너리 객체가 만들어진다.<br>

### 숫자 시퀀스 생성 (range)

range()함수는 특정 범위의 숫자 스트림 데이터를 변환한다.

```
range(start, stop, step)
```

zip과 마찬가지로, iterable한 객체를 변환하며, 따라서 for문을 순회할 수 있다.

```
>>> for x in range(0, 10):
...   print(x)
...
0
1
2
3
4
5
6
7
8
9
```

## while문 (반복문)
for문과 유사하나, while뒤의 조건이 참일 경우에 계속해서 반복한다.

```
while 조건 :
	조건이 참일경우 실행
	조건이 거짓이 될 경우까지 계속해서 반복
```

```
>>> count = 0
>>> while count < 10:
...   print(count)
...   count += 1
...
0
1
2
3
4
5
6
7
8
9
```


<br><br>

## 컴프리헨션 (Comprehension)

> 함축 또는 내포

iterable한 객체로부터 파이썬의 자료구조를 만드는 방법. 가독성과 사용성에서 이득을 얻을 수 있을 경우 항상 사용해준다.


### 리스트 컴프리헨션

```
[표현식 for 항목 in iterable객체]
```

[1,2,3,4,5]를 만드는 방법
<br>
#### range와 for 문을 사용할 경우

```
>>> numbers = []
>>> for item in range(1, 6):
...   numbers.append(item)
...
>>> numbers
[1, 2, 3, 4, 5]
```

#### 리스트 컴프리헨션을 사용할 경우

```
>>> [item for item in range(1, 6)]
[1, 2, 3, 4, 5]
```

#### 리스트 컴프리헨션의 중첩

```
for color in colors:
	for fruit in fruits:

```

```
[(color,fruit) for color in colors for fruits in fruits]
```

#### 셋 컴프리헨션

```
{표현식 for 표현식 in iterable객체}
```
