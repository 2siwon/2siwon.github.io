---
layout: post
title:  "Django : 06. Model - Relations 추가내용"
date:   2017-10-11 15:10:41 +0900
categories: Django
tag: [Django]
---


**ForeignKey.on_delete 옵션** 은 1측의 Row가 삭제될 경우, N측의 Row의 처리에 대한 동작을 지정한다.

- CASECADE : 연결된 Row를 일괄 삭제 (디폴트 동작)
- PROTECT : ProtectedError 예외를 발생시키며, 삭제 방지
- SET_NULL : null=True 설정이 되어있을 때, 삭제되면 해당 필드를 null 설정
- SET_DEFAULT : 필드에 지정된 디폴트값으로 설정
- SET : 값이나 함수를 지정. 함수의 경우 호출결과값을 지정

보통 CASECADE를 DEFAULT값으로 사용한다.

<br><br>

**ForeignKey에서 Related_name 지정의 필요성**

Many-to-One 관계에서 1측에서 N측으로 접근 시의 속성명 : 모델명소문자_set

```python
>>> post = Post.objects.first()
>>> print(Comments.objects.filter(post=post))	# 방법1
>>> porint(post.comment_set.alL())				# 방법2
<Query set [<Comment: Comment object>, <Comment: Comment object>]>
<Query set [<Comment: Comment object>, <Comment: Comment object>]>
```

**만약 2개의 앱이 동일한 모델을 가지고 있다면?**

blog앱과 shop앱이 있다고 해보자. 이 둘의 앱은 각각 Post모델을 가지고 있고 ForeignKey로 User모델과 연결되어있다. 어떡해야 할까?

방법1. 각각 다른 related_name을 설정한다.<br>

두 모델 모두 **related_name='post\_set'** 으로 동일한 이름을 가지게된다. 이 때 중복을 발생하지 않게하기 위해서 <br>
blog앱의 속한 Post모델 ForeignKey의 related\_name옵션은 related_name = 'blog\_post\_set'<br>
shop앱의 속한 Post모델 ForeignKey의 related\_name옵션은 related\_name = 'blog\_post\_set'<br>
혹은<br>
related\_name="%(app\_label)s\_%(class)s\_related" 로 통일하는 방법도 있다.


방법2. related_name을 포기한다.<br>

**related_name='+'** 옵션값으로 '+'을 주면

```python
user.post_set.all() 							# 이렇게 접근하는 건 안된다. 대신
shop.models.Post.objects.filter(user=user) 		# 이런식으로 접근해야 한다.
```

<br><br>

**Many-to-many 에서 원하는 쿼리셋을 관계에 추가하기**

사전작업 : 관련 Object 획득

```python
post = Post.objects.first()
tag1 = post.tag_set.all()[0]
tag2 = post.tag_set.all()[1]
tag3 = post.tag_set.all()[2]
tag_qs = post.tag_set.all()[:3]
```

관계에 추가

```python
post.tag_set.add(tag1)
post.tag_set.add(tag1, tag2)
post.tag_set.add(tag1, tag2, tag3)
post.tag_set.add(*tag_qs)
```

관계에서 제거

```python
post.tag_set.remove(tag1)
post.tag_set.remove(*tag_qs)
```
