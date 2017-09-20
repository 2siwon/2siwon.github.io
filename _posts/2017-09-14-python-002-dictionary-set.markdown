---
layout: post
title:  "Python : 02. 딕셔너리, 셋"
date:   2017-09-14 15:10:41 +0900
categories: Python
---

## 딕셔너리(dictionary)
Key-Value 형태로 항목을 가지는 자료구조.

#### 딕셔너리 생성

```
>>> empty_dict1 = {}
>>> empty_dict2 = dict()
>>> champion_dict = {
... 'Lux': 'the Lady of Luminosity',
... 'Ahri': 'the Nine-Tailed Fox',
... 'Ezreal': 'the Prodigal Explorer',
... 'Teemo': 'the Swift Scout',
... }
```

<br><br>

#### 형변환

dict 함수를 사용, 두 값의 시퀀스(리스트 또는 튜플)를 딕셔너리로 변환 한다.

```
>>> sample = [[1,2], [3,4], [5,6]]
>>> dict(sample)
{1: 2, 3: 4, 5: 6}
```
<br><br>
#### 항목 찾기/추가/변경 [key]

```
>>> champion_dict['Lux']
'the Lady of Luminosity'
>>> champion_dict['Sona'] = 'Maven of the Strings'
>>> champion_dict['Lux'] = 'Demacia'

```

<br><br>

#### 결합 (update)

```
>>> item_dict = {
... 'Doran\'s Ring': 400,
... 'Doran\'s Blade': 450,
... 'Doran\'s Shield': 450,
... }
>>> com_dict = {}
>>> com_dict.update(champion_dict)
>>> com_dict.update(item_dict)
>>> com_dict

```
서로 같은 키가 있을 경우, update에 주어진 딕셔너리의 값이 할당된다.

<br><br>

#### 삭제 (del)

```
>>> del com_dict['Doran\'s Blade']
>>> del com_dict['Doran\'s Ring']
>>> del com_dict['Doran\'s Shild']
```

<br><br>
#### 전체 삭제 (clear)
전체 항목을 삭제

<br><br>
#### in으로 키 검색
True/False를 반환한다.

<br><br>
####키 또는 값 얻기
**keys() :**
모든 key 얻기

**values() :**
모든 value 얻기

**items() :**
모든 key-value 얻기(튜플로 형변환)

**copy() :**
복사
<br><br>

## 셋(set)
셋은 키만 있는 딕셔너리와 같으며, 중복된 값이 존재할 수 없다.

#### 셋 생성

```
>>> empty_set = set()
>>> champions = {'lux', 'ahri', 'ezreal'}
```

<br><br>
#### 형변환
문자열, 리스트, 튜플, 딕셔너리를 셋으로 변환할 수 있으며, 중복된 값이 사라진다.

```
>>> set('ezreal')
{e, z, a, l, r}
```

```
>>> set(champion_dict)
{'Ahri', 'Lux', 'Ezreal', 'Sona', 'Teemo'}
```
딕셔너리를 셋으로 형변환하면 key만 남는다.

<br><br>

#### 집합 연산

| 연산자  | 설명  |
|---|---|
| \|  | 합집합(Union)  |
| &  | 교집합(Intersection)  |
|  - | 차집합(Difference)  |
|  ^ | 대칭차집합(Exclusive)  |
|  <= | 부분집합(Subset)  |
|  < |  진부분집합(Proper subset) |
|  >= | 상위집합(Superset)  |
|  > |  진상위집합(Proper superset) |

```
>>> A = {1,2,3,4,5}
>>> B = {4,5,6,7,8,9}
>>> C = {4,5,6}
>>> A|B
>>> A&B
>>> A-B
>>> B-A
>>> A^B
>>> A <= B
>>> C <= B
>>> C < B
>>> B <= B
>>> B < B
```
