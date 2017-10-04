---
layout: post
title:  "Git : 10. gitignore로 버전관리에서 특정 파일 제외하기
"
date:   2017-09-15 15:10:41 +0900
categories: Git
---



git으로 관리되는 프로젝트에서 프로젝트에 관련없는 파일이나 제외하고 싶은 파일들이 있을 경우 .gitignore 파일을 생성해서 관리한다.
macOS에서 파일앞의 `.`의 의미는 숨김파일이란 의미이므로 shell에서 `$ ls -al` 과 같은 명령어를 이용해야 파일을 볼 수 있다.<br>

.gitignore에 들어가야 할 파일들은 이미 github저장소인 [https://github.com/github/gitignore](https://github.com/github/gitignore) 에서 제공하고 있다. 또한 [https://www.gitignore.io/](https://www.gitignore.io/)에서 원하는 스택을 몇가지 고르면 .gitignore파일을 자동으로 generate해주며 그저 cmd+c, cmd+v하면 된다. <br><br>

ex) 자신이 mac유저이고 python django framework를 사용하는 웹개발자이며 IDE로 pycharm을 사용한다면 **macOS, django, pycharm, python** 을 고르고 create를 누르면 자동으로 파일이 생성되므로 편하게 파일 생성을 할 수 있다.
