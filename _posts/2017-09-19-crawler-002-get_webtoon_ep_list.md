---
layout: post
title:  "Crawler : 02. 네이버 웹툰 크롤러 만들기(1)"
date:   2017-09-19 15:10:41 +0900
categories: Crawler
---

크롤링을 위해 필요한 라이브러리(Requests, BeautifulSoup4, urllib.parse)들과 특정 웹툰의 id값과 page값으로 에피소드 리스트를 크롤링해보자.

## 크롤링을 위해 필요한 라이브러리

### Requests

python으로 웹에서 정보를 가져오기 위해서  http request 라이브러리인 Requests를 설치해준다. pip를 이용하여 설치한다.

```
pip install requests
```

### BeautifulSoup4

뷰티플수프는 HTML과 XML 파일로부터 데이터를 뽑아내기 위한 파이썬 라이브러리이다. 해석 트리를 항해, 검색, 변경 가능하다.

### urllib.parse

url 파싱을 위한 python 표준 모듈


<br><br>

## 특정 웹툰의 id값과 page값으로 에피소드 리스트 크롤링



NamedTuple(Episode) 을 사용해서 이미지 주소(img_url), 에피소드 제목(title), 에피소드 별점 (rating)
에피소드 등록일(created_date)을 가지는 NamedTuple의 리스트를 생성. webtoon id를 입력받아 해당 웹툰의 첫 번째 페이지에 있는 episode리스트를 리턴하는 함수를 구현한다.

```python
# url 파싱 라이브러리
from urllib.parse import urlparse, parse_qs

from bs4 import BeautifulSoup
from collections import namedtuple
```

크롤링을 위한 라이브러리를 import 해준다.


```python
Episode = namedtuple('Episode', ['no', 'img_url', 'title', 'rating', 'created_date'])
```

에피소드 리스트를 받을 인스턴스 생성을 위한 namedtuple 명시

```python
webtoon_yumi = 651673
webtoon_denma = 119874

def get_webtoon_episode_list(webtoon_id, page=1):
```

테스트를 위한 webtoon_id, 페이지 기본값 1

```
    webtoon_list_url = 'http://comic.naver.com/webtoon/list.nhn'
```

webtoon_url을 기반으로 특정 웹툰의 리스트페이지 내용을 가져와 soup객체에 할당

```python
    params = {
        'titleId': webtoon_id,
        'page': page
    }

    response = requests.get(webtoon_list_url, params=params)

    soup = BeautifulSoup(response.text, 'lxml')
```

request.get()의 두번째 인자로 변수를 적으면 알아서 URL에 대입해준다.<br>
response의 소스코드를 BeautifulSoup을 이용해 가공 후 soup에 할당

```python
    episode_list = list()
```

최종적으로 리턴할 episode_list 초기화

```python
    webtoon_table = soup.select_one('table.viewList')
```

클래스 id가 viewList인 table 태그 안에 에피소드 리스트들의 정보가 있으므로 범위 줄임


```python
    tr_list = webtoon_table.find_all('tr', recursive=False)
```

에피소드 리스트가 있는 웹툰 테이블의 tr태그를 모두 찾는다.<br>
recursive=False 옵션을 주면 직계 자손만 검색.


```python
    for tr in tr_list:
        td_list = tr.find_all('td')
```

필요한 데이터들 (img_url, title, rating, created_data) 추출

```python
        if len(td_list) < 4:
            continue
        td_thumbnail = td_list[0]
        td_title = td_list[1]
        td_rating = td_list[2]
        td_created_date = td_list[3]
```

소스코드 분석 시 에피소드는 td를 4개 가지고있음.
배너 및 광고는 colspan이 4인 td 1개 이므로 이를 이용해 예외처리를 해준다.
해당 에피소드의 raw td 데이터 저장

```python
		url_episode = td_thumbnail.a.get('href')
```

Episode고유의 no<br>
a태그의 속성값 href를 가져온다

```python  
		parse_result = urlparse(url_episode)
```

urlparse를 이용해 query에 접근 가능하도록 만듬

```python
		queryset = parse_qs(parse_result.query)
```

query를 쪼개서 딕셔너리로 queryset에 저장

```python
		no = queryset['no'][0]
```

key가 no인 value 리턴

```python
		img_url = td_thumbnail.a.img.get('src')
```

td_thumbnail에 해당하는 Tag의 첫 번째 a tag의 첫 번째 img태그의 'src'속성값

```python
		title = td_title.get_text(strip=True)
		rating = td_rating.strong.get_text(strip=True)
		created_date = td_created_date.get_text(strip=True)
```

td_title tag의 내용을 좌우여백 잘라냄. 이하 동일

```python
		episode = Episode(
			no=no,
			img_url=img_url,
			title=title,
			rating=rating,
			created_date=created_date
		)		
		episode_list.append(episode)

	return episode_list
```

Episode형 nametuple객체 생성. episode_list에 추가
