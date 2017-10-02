---
layout: post
title:  "Python : 07. 모듈과 패키지"
date:   2017-09-17 15:10:41 +0900
categories: Python
---

모듈(module)에 대해서 알아보자.<br><br>


## 모듈

#### 모듈 불러오기 (import)

module/shop.py

```
def buy_item():
    print('Buy item!')

buy_item()
```

module/game.py

```
def play_game():
    print('Play game!')

play_game()
```

module/lol.py

```
import game
import shop

print('= Turn on game =')
game.play_game()
shop.buy_item()
```

같은 디렉토리 안에 있는 .py파일일 경우 또 다른 파일에서 **import** 로 불러올 수 있다.

<br><br>

#### \_\_name__ 변수

lol.py가 실행될 때, game과 shop가 import되는 순간 해당 코드가 실행되어 버리는 문제가 있다.

이 때, 파이썬 인터프리터를 이용해 실행한 코드인지를 확인하여 단순히 import한 모듈의 경우 실행을 막는 방식을 사용할 수 있다.

각 모듈은 자신의 이름을 가지며, 모듈 이름은 모듈의 전역변수 __name__에서 확인 할 수 있다.

```
print(__name__)
```

파이썬 인터프리터가 실행한 모듈의 경우, __main__이라는 이름을 가진다. 따라서 python <파일명>으로 실행한 경우에만 동작할 부분은 if문으로 감싸준다.

**module/shop.py**

```
def buy_item():
    print('Buy item!')

if __name__ == '__main__':
    buy_item()
```

**module/game.py**

```
def play_game():
    print('Play game!')

if __name__ == '__main__':
    play_game()
```

**lol.py 리팩토링**

```
import game
import shop

def turn_on():
    print('= Turn on game =')

    while True:
        choice = input('What would you like to do?\n  1: Go to Shop, 2: Play Game, 0: Exit\n    Input : ')
        if choice == '0':
            break
        elif choice == '1':
            shop.buy_item()
        elif choice == '2':
            game.play_game()
        else:
            print('Choice not exist')
        print('-----------------------')

    print('= Turn off game =')

if __name__ == '__main__':
    turn_on()
```

<br><br>

#### 모듈명을 안쓰고 함수를 호출하는 방법

**from 모듈명 import 모듈의 함수명**

```
from game import play_game
from shop import buy_item
```

**from 모듈명 import \***
<br>해당 모듈의 모든 함수를 모듈명을 안쓰고 호출 할 수 있다.

```
from game import *
from shop import *
```

<br><br>

#### 네임스페이스(Namespace)

각 모듈은 독립된 네임스페이스(이름공간)를 가진다. 메인으로 사용되고 있는 모듈이 아닌 import된 모듈의 경우, 해당 모듈의 네임스페이스를 사용해 모듈 내부의 데이터에 접근한다.

같은 함수명을 가진 모듈을 2개 만들고, 한 쪽에서 다른 한 쪽의 모듈을 import 한 뒤 각각의 모듈의 함수를 실행시켜보면 더 나중에 import한 모듈의 함수가 실행된다.

<br><br>

#### as로 별칭 할당

from 모듈명 import 또는 import 모듈명에서, 같은 모듈명이 존재하거나 혼동 될 수 있을 경우, 뒤에 as를 붙여 사용할 모듈명을 변경할 수 있다.

<br><br>

#### 커맨드라인에서 인자 전달

프로그램 실행 시 인자를 전달 할 수 있다. 파이썬의 내장sys모듈의 argv리스트에서 확인 할 수 있다.

리스트의 첫 번째 항목은 모듈명이 전달되므로, 2번째 항목부터 전달 된 값을 확인 할 수 있다.

<br><br>


## 패키지(package)

패키지는 모듈들을 모아 둔 특별한 폴더를 뜻한다. (일반적인 폴더와는 다르다)

폴더를 패키지로 만들면 계층 구조를 가질 수 있으며, 모듈들을 해당 패키지에 모을 수 있는 역할을 한다.

패키지를 만들 때는 패키지로 사용할 폴더에 \_\_init__.py파일을 넣어주면, 해당 폴더는 패키지로 취급된다. (비어있어도 무관하다)

shop.py와 game.py를 func패키지에 넣어본다.

```
├── func
│   ├── __init__.py
│   ├── game.py
│   └── shop.py
└── lol.py
```

패키지는 모듈과 동일하게 import할 수 있으며, 위와 같이 func패키지에 모듈들을 넣은 경우에는

from func import game, shop으로 기존 코드의 변경 없이 패키지에서 모듈을 가져오는 방식을 사용할 수 있다.

또는 단순히 import func후 func.game, func.shop을 사용하는 방식도 가능하다.

패키지에 포함된 하위 패키지 및 모듈을 불러올 때, **\*** 을 사용하면 해당 모듈의 모든 식별자들을 불러온다.
