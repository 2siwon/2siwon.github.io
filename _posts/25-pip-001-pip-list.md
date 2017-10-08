---
layout: post
title:  "pip : 01. pip list 출력 포맷 변경방법"
date:   2017-09-25 15:10:41 +0900
categories: pip
tag: [pip, Tip]
---

pip list 명령어를 적으면 출력 포맷이 변경되었다는 warning문구가 출력된다. 출력 포맷을 변경하여 해결해보자.

```
pip list
```

```
DEPRECATION: The default format will switch to columns in the future.
You can use --format=(legacy|columns) (or define a format=(legacy|col
umns) in your pip.conf under the [list] section) to disable this warn
ing.
```

<br><br>

홈 디렉토리에 .pip폴더를 만들고 .pip폴더 안에 pip.conf 파일을 생성한다.

```
mkdir ~/.pip
vi ~/.pip/pip.conf
```

<br><br>

#### pip.conf

```
[list]
format=columns
```

포맷에 columns 옵션을 준다.

<br><br>

이 후, pip list를 해보면,

```
pip list
```

```
Package    Version
---------- -------
Django     1.11.5
pip        9.0.1
pytz       2017.2
setuptools 28.8.0
```

컬럼형식으로 출력되는 걸 확인 할 수 있다.
