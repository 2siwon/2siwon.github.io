---
layout: post
title:  "Python : 05. 파일입출력"
date:   2017-09-16 15:10:41 +0900
categories: Python
---

파일 입출력에 대해 알아보자.

## 파일 생성하기(w)

```
f = open("새파일.txt", 'w')
f.close()
```

파일 객체 = open(파일 이름, 파일 열기 모드)

| 파일열기모드  | 설명  |
|---|---|
| r  | 읽기  |
|  w |  쓰기 |
|  a |  추가모드 - 파일의 마지막에 새로운 내용 추가 |

**f.close()** 는 열려있는 파일 객체를 닫아주는 역할을 한다. 항상 닫아주는 게 좋다. 쓰기모드로 열었던 파일을 닫지 않고 다시 사용하려고 하면 오류가 발생하기 때문이다.

<br><br>

## 파일을 쓰기 모드로 열어 출력값 적기(write)

```
f = open("C:/Python/새파일.txt", 'w')
for i in range(1, 11):
    data = "%d번째 줄입니다.\n" % i
    f.write(data)
f.close()
```

파일객체를 쓰기모드로 열고 write함수를 이용해 파일에 결과값을 적어준다.
<br><br>

## 프로그램의 외부에 저장된 파일을 읽는 여러 가지 방법(r)

#### readline() 함수 이용하기

```
f = open("C:/Python/새파일.txt", 'r')
line = f.readline()
print(line)
f.close()
```

이전에 생성한 **새파일.txt** 을 읽어 readline() 함수로 한 줄을 읽어와 출력하면

```
1번째 줄입니다.
```

가 출력된다.
<br><br>

만약 모든 라인을 출력하고 싶다면?

```
f = open("C:/Python/새파일.txt", 'r')
while True:
    line = f.readline()
    if not line: break
    print(line)
f.close()
```

while문을 사용한다.

<br><br>

#### readlines() 함수 이용하기

```
f = open("C:/Python/새파일.txt", 'r')
lines = f.readlines()
for line in lines:
    print(line)
f.close()
```

**readlines()** 함수는 모든 line을 읽어서 각각 line을 요소로 갖는 list객체를 리턴해준다.<br>
["1 번째 줄입니다.\n","2 번째 줄입니다.\n",..., "10 번째 줄입니다.\n"]

<br><br>

#### read() 함수 이용하기

```
f = open("C:/Python/새파일.txt", 'r')
data = f.read()
print(data)
f.close()
```

f.read()는 파일의 내용 전체를 문자열로 리턴한다. 따라서 위 예의 data는 파일의 전체 내용이다.

<br><br>

## 파일에 새로운 내용 추가하기(a)

쓰기모드('w')로 원래 있던 파일의 객체를 열면 그 안의 내용은 모두 **덮어쓰기** 된다. 원래 있던 값을 유지하면서 새로운 내용을 추가하는 파일 추가 모드('a')를 알아보자.

```
f = open("C:/Python/새파일.txt",'a')
for i in range(11, 20):
    data = "%d번째 줄입니다.\n" % i
    f.write(data)
f.close()
```

원래 가지고 있던 내용 다음부터 결과값을 적기 시작한다.

<br><br>

## with문과 함께 사용하기

```
with open("foo.txt", "w") as f:
    f.write("Life is too short, you need python")
```

파일을 열면 위와 같이 항상 close해 주는 것이 좋다. 하지만 with문을 사용하면 이렇게 파일 객체를 열고 닫는 것을 자동으로 처리할 수 있다.
