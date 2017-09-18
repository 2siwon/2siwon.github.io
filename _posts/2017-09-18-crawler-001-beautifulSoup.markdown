---
layout: post
title:  "Crawler : BeautifulSoup 활용하기"
date:   2017-09-18 15:10:41 +0900
categories: Crawler
---

## Crawler

>공식문서  - https://cryptosan.github.io/pythondocuments/documents/beautifulsoup4/

### BeautifulSoup를 이용하여 네이버 웹툰 타이틀 가져오기  

BeautifulSoup은 웹 크롤링을 위한 파이썬 라이브러리 중 하나이다. 설치 및 사용방법을 알아보자. 설치는 pip를 이용 할 것이다.

<br>
### beaultifulsoup4 설치

```
$ pip install beautifulsoup4
```

<br>
### 파서(parser) 설치

```
$ pip install lxml
```

<br>
### soup 만들기
라이브러리를 import 해준다.

```
from bs4 import BeautifulSoup
```


### 코드
크롤링 할 웹 페이지 - http://comic.naver.com/webtoon/list.nhn?titleId=651673&weekday=wed<br>
이 페이지에서 개발자 도구로 구조를 파악한 후 title만 list로 받아 출력한다.

```
  1 from bs4 import BeautifulSoup
  2
  3 title_list = list()
  4
  5 f = open('sample.txt', 'rt')
  6 source = f.read()
  7 soup = BeautifulSoup(source)
  8
  9
 10 pkg_list = soup.find_all("td", "title")
 11
 12 for i in pkg_list :
 13     title_list.append(i.find('a').text)
 14
 15 for i in title_list :
 16     print(i)
```
1. sample.txt 파일을 읽어 soup객체를 만든다.
2. 모든 td 와 title 태그를 list형태로 pkg_list에 저장한다.
3. 제목이 속해 있는 a태그의 text를 title_list에 하나씩 append 해준다.
