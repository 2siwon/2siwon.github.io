---
layout: post
title:  "Git : 소개"
date:   2017-09-11 15:10:41 +0900
categories: Git
---
]<link rel="stylesheet" href="./css/style.css"></link>


###### Mac을 기준으로 작성된 포스팅입니다.



`Git`은 주로 파일에 대한 히스토리 관리가 필요한 개발자나 디자이너들이 사용하는 [버전관리시스템(VCS - Version Control System)](https://ko.wikipedia.org/wiki/%EB%B2%84%EC%A0%84_%EA%B4%80%EB%A6%AC)이다. 파일변화를 시간에 따라 기록했다가 이 후 특정 시점의 버전을 다시 꺼내올 수 있는 시스템이다.


#### Git의 역사

`Linux 커널`은 굉장히 대규모의 `오픈소스 프로젝트`이다. [Linux 커널](https://ko.wikipedia.org/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EC%BB%A4%EB%84%90)의 삶 대부분은(1991-2002) Patch와 단순 압축 파일로만 관리했다. 2002년에 `Linux 커널`은 Bitkeeper라고 불리는 상용 DVCS(분산버전관리시스템)을 사용하기 시작했다.

이후 2005년 오픈소스 프로젝트인 Linux커널과 이익을 추구하는 BitKeeper의 이해관계가 틀어져 BitKeeper의 무료 사용이 제고되고 Linux의 창시자인 [리누스 토발즈(Linus Torvalds)](https://namu.wiki/w/%EB%A6%AC%EB%88%84%EC%8A%A4%20%ED%86%A0%EB%A5%B4%EB%B0%9C%EC%8A%A4)를 필두로 자체 도구를 만들게 되었다. 그것이 `Git`이다.
