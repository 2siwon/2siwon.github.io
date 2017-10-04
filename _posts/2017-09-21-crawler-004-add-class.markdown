---
layout: post
title:  "Crawler : 04. 네이버 웹툰 크롤러 만들기(3)"
date:   2017-09-21 15:10:41 +0900
categories: Crawler
---

네이버 웹툰 크롤러 만들기(2)까지 작성한 코드는 utils 모듈로 리팩토링. 이하 코드는 utils를 import하여 작성.


### 네이버 웹툰 크롤러 만들기

#### crawler2.py

```python
import os
import requests
import utils
import pickle


class NaverWebtoonCrawler:
    def __init__(self, webtoon_id):
        self.webtoon_id = webtoon_id
        self.episode_list = list()
        self.load(init=True)

```

* 객체 생성 시, 'db/{webtoon_id}.txt'파일이 존재하면 바로 load() 해오도록 작성
<br><br>


```python
    @property
    def total_episode_count(self):

        el = utils.get_webtoon_episode_list(self.webtoon_id)
        return int(el[0].no)
```

* 모든 에피소드의 수를 가져오는 메소드. 가장 마지막 에피소드는 index[0]에 저장 되어있다.


<br><br>

```python
    @property
    def up_to_date(self):

        cur_episode_count = len(self.episode_list)
        print(cur_episode_count)
        total_episode_count = self.total_episode_count

        # return cur_episode_count == total_episode_count
        return len(self.episode_list) == self.total_episode_count
```

* 현재 가지고있는 episode_list가 웹상의 최신 episode까지 가지고 있는지 여부를 변환하는 메소드
* return : boolean
<br><br>


```python
    def update_episode_list(self, force_update=False):

        recent_episode_no = self.episode_list[0].no if self.episode_list else 0

        print('- Update episode list atart ( Recent episode no : %s) - ' % recent_episode_no)
        page = 1
        new_list = list()
        while True:
            print('Get webtoon episode list (Loop %s)' % page)
            # 계속해서 증가하는 'page'를 이용해 다음 episode리스트를 가져옴
            el = utils.get_webtoon_episode_list(self.webtoon_id, page)
            # 가져온 episode list를 순회
            for episode in el:
                # 각 episode의 no가 recent_episode_no보다 클 경우
                # self.episode_list에 추가
                if int(episode.no) > int(recent_episode_no):
                    new_list.append(episode)
                    if int(episode.no) == 1:
                        break
                else:
                    break
            # break가 호출되지 않았을 때
            else:
                # 계속해서 진행해야 하므로 page값을 증가시키고 continue로 처음으로 돌아감
                page += 1
                continue
            # el의 for문에서 break가 호출될 경우(더 이상 추가할 episode없음)
            # while문을 빠져나가기위해 breakt실행

            break

        self.episode_list = new_list + self.episode_list
        # 총 업데이트 된 에피소드 수 리턴
        return len(new_list)
```

* 객체 생성 후 업데이트부터 해줘야한다. 하지 않으면 self.episode_list는 초기화상태(빈 리스트)이다.
* self.episode_list에 존재하지 않는 episode들을 self.episode_list에 추가
* return : 추가된 episode의 수

<br><br>



```python
    def get_first_page_episode_list(self):
        el = utils.get_webtoon_episode_list(self.webtoon_id, 99999)
        self.episode_list = el
        return len(self.episode_list)
```


* 가장 마지막 페이지, 즉 맨 처음 에피소드 리스트의 개수를 리턴하는 함수
<br><br>



```python
    def save(self, path=None):
        if not os.path.isdir('db'):
            os.mkdir('db')

        obj = self.episode_list
        path = 'db/%s.txt' % self.webtoon_id
        pickle.dump(obj, open(path, 'wb'))
```

* 현재폴더를 기준으로 db/<webtoon_id>.txt 파일에 pickle로 self.episode_list를 저장

<br><br>



```python
    def load(self, path=None, init=False):
        try:
            path = f'db/{self.webtoon_id}.txt'
            # path = 'db/%s.txt' % self.webtoon_id
            self.episode_list = pickle.load(open(path, 'rb'))

        except FileNotFoundError:
            if not init:
                print('파일이 없습니다')
```


* 현재폴더를 기준으로 db/<webtoon_id>.txt 파일의 내용을 불러와 pickle로 self.episode_list를 복원
<br><br>



```python
    def save_list_thumbnail(self):
        thumbnail_dir = f'webtoon/{self.webtoon_id}_thumbnail'
        os.makedirs(thumbnail_dir, exist_ok=True)

        # 각 episode의 img_url속성에 해당하는 이미지를 다운로드
        for episode in self.episode_list:
            response = requests.get(episode.img_url)
            filepath = f'{thumbnail_dir}/{episode.no}.jpg'

            if not os.path.exists(filepath):
                with open(filepath, 'wb') as f:
                    f.write(response.content)
```

* webtoon/{webtoon_id}_thumbnail이라는 폴더가 존재하는지 확인 후 생성
* self.episode_list를 순회하며 각 episode의 img_url경로의 파일을 저장
* return: 저장한 thumbnail 개수
<br><br>


```python
    def make_list_html(self):
        if not os.path.isdir('webtoon'):
            os.mkdir('webtoon')

        filename = f'webtoon/{self.webtoon_id}.html'
        with open(filename, 'wt') as f:
            # HTML 앞부분 작성
            f.write(utils.LIST_HTML_HEAD)

            # episode_list 순회하며 나머지 코드 작성
            for e in self.episode_list:
                f.write(utils.LIST_HTML_TR.format(
                    img_url=f'./{self.webtoon_id}_thumbnail/{e.no}.jpg',
                    title=e.title,
                    rating=e.rating,
                    created_date=e.created_date))

            f.write(utils.LIST_HTML_TAIL)
        return filename
```

* self.episode_list를 HTML파일로 만들어준다.
<br><br>



```python
if __name__ == '__main__':

    crawler = NaverWebtoonCrawler(696617)
    crawler.update_episode_list()

    if crawler.up_to_date:
        print('아 업로드 왤케 느린거야 ㅡㅡ')
    else:
        print('최신화 업로드됐다! 업데이트 ㄱㄱ!')
        crawler.update_episode_list()

    crawler.save()
    crawler.save_list_thumbnail()
    crawler.make_list_html()
```

* 객체 생성 및 운용
