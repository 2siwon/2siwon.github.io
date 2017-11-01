---
layout: post
title:  "AWS : RDS 접근 방법"
date:   2017-10-21 15:10:41 +0900
categories: AWS
tag: [AWS]
---

## RDS 접근 방법

### 인바운드 규칙 편집

1. RDS에 접근하기 위해선 RDS에서 사용하고 있는 포트를 열여야 한다.
2. DB엔진이 postgresql이라면 인바운드 규칙을 그에 맞게 편집한다.
3. 만약 자신의 ip로 등록했다면 ip가 바뀔때마다 규칙편집을 수정해줘야 한다.(로컬)
4. 개발환경에 적용하고 싶다면 RDS에 보안 그룹을 설정한다.

### 접근 명령어

1. RDS 인스턴스의 상세정보를 보면 엔드포인트 주소를 알 수 있다.
2. psql 명령어의 --host옵션으로 엔드포인트 주소를 접속한다.
3. 여기서 user와 db이름은 RDS 인스턴스를 생성할 때 user, db이름 값이다.

- 엔드포인트 : mydbinstance.cmidikiyw7zk.ap-northeast-2.rds.amazonaws.com
- 포트번호 : 5432
- user: siwon
- db이름: deploy

#### 예시

`psql --host=<포트번호를 제외한 엔드포인트 주소> --user=<사용자명> --port=5432 <db 이름>`

#### 적용

`psql --host=mydbinstance.cmidikiyw7zk.ap-northeast-2.rds.amazonaws.com --user=siwon --port=5432 deploy`

> postgresql은 postgres라는 default db가 생성되어있는데 서버 전체에 대한 작업이 가능한 db이므로 삭제하지 않는다.
