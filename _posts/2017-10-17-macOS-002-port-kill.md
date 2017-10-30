---
layout: post
title:  "MacOS : 사용중인 포트 확인/죽이기"
date:   2017-10-17 15:10:41 +0900
categories: macOS
tag: [macOS, Tip]
---

서버를 내렸는데 해당 포트가 사용중이라는 메시지가 출력된다면 포트를 찾아 kill 해보자.<br>
8000번 포트가 사용중이라고 뜬다면?

**확인**

```
lsof -i tcp:8000
```

**죽이기**

```
kill $(lsof -t -i:8000)
```
