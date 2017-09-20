---
layout: post
title:  "Crawler : 02. 네이버 웹툰 크롤러 만들기(2)"
date:   2017-09-19 15:10:41 +0900
categories: Crawler
---

## 크롤링 한 episode_list를 파일 저장

네이버 웹툰 크롤러 만들기(1)에서 특정 웹툰의 id값과 page값으로 에피소드 리스트 크롤링을 해와서 출력까지 해봤다. namedtuple로 만든 Episode의 인스턴스인 episode를 가독성을 생각하여 파일로 저장해보자.

```
def save_episode_list_to_file(webtoon_id, episode_list):
```

파일이름을 생성하기 위해 해당 웹툰의 고유 id인 webtoon\_id가 필요하다.<br>
파일이름을 생성하기 위해 episode\_list가 필요하다.(first\_episod\_no, last\_episode\_no)

```
    filename = '{webtoon_id}_{first_episode_no}_{last_episode_no}.txt'.format(
        webtoon_id = webtoon_id,
        first_episode_no = episode_list[0].no,
        last_episode_no = episode_list[-1].no,
    )
```

매개변수를 이용하여 파일이름을 할당한다.

```
    with open(filename, 'wt') as f:
        for episode in episode_list:
            f.write('|'.join(episode) + '\n')

save_episode_list_to_file(webtoon_yumi, episode_list)
```

'|'로 각 episode의 index를 나눠서 써준다. 하나의 episode가 끝나면 '\n'개행 한다.


<br><br>

## 파일에서 다시 episode_list로 load하기

```
def load_episode_list_from_file(path):
    with open(path, 'rt') as f:

        episode_list = list()
        for line in f:
            episode_list.append(Episode._make(line.strip().split('|')))
        return episode_list
```

1. 같은 디렉토리에 있다면 episode_list를 저장한 파일 이름을 인자로 한다.
2. 다시 episode_list를 만들기 위한 list객체를 초기화한다.
3. 한 줄 읽을 때마다 episode_list에 한 줄에 대한 정보를 넣는다.
4. 한 줄에 대한 정보는 colledctions.namedtuple에 메서드인 \_make()를 이용한다. (\_make()는 기존에 생성된 namedtuple(class라고 생각하면 쉽다.)에 새로운 인스턴스를 생성하는 메소드이다.
5. String객체인 line 메소드인 strip()으로 양 쪽 공백을 없애고  '|'로 나누어 다시 저장한다.
6. 다시 load한 episode_list를 리턴한다.
