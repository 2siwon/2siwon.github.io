---
layout: post
title:  "Django : 09. Model field(작성중)"
date:   2017-10-14 15:10:41 +0900
categories: Django
tag: [Django]
---


**Model field reference** 기술적으로 모델은 django.db.models.fields에 정의되어 있다. 하지만 편의를 위해서 django.db.models에서 import해서 사용한다. 표준(standard convention) 또한 django.db.models에서 참조하도록 한다.

<br><br>

## Field options

아래의 field options는 모든 field type에서 사용할 수 있다.

**null**

###### field.null

True면 django는 빈 값은 NULL로 데이터베이스에 저장한다.(Default = False)<br>
**CharField** 나 **TextField** 에서는 null을 피한다. 불필요하게 데이터없음에 대한 두 가지 값(null값, empty값)을 동시에 가질 수 있기 떄문이다. django표준은 null대신 빈문자열을 갖는 것이다.

**예외!**

한 가지 예외는 CharField에 unique = True와 blank = True가 설정된 경우이다. 이 경우 빈 값으로 여러 오브젝트를 저장할 때 고유 제한 조건 위반을 피하려면 null = True가 필요하다.

> BooleanField 을 null로 처리하고 싶다면 NullBooleanField를 사용한다.

<br><br>

**blank**

###### Field.blank

blank = True 면 빈 값을 허용한다.(Default = False) <br>

**null과 blank의 차이점**

null은 오직 데이터베이스에만 관련이있는 옵션이다. 하지만 blank는 유효성 검사와 연관되 있다. <br>
만약 어떤 field에 blank = True가 선언되어 있다면 필수 필드가 되어 form validation 검사에서 빈 값을 입력 할 수 있다.

<br><br>

**Choices**

###### Field.Choices

각 튜플의 첫 번째 요소는 모델에 설정할 실제 값이고 두 번째 요소는 사람이 읽을 수 있는 이름이다.

```python
YEAR_IN_SCHOOL_CHOICES = (
    ('FR', 'Freshman'),
    ('SO', 'Sophomore'),
    ('JR', 'Junior'),
    ('SR', 'Senior'),
)
```

모델 클래스 외부에서 choices 목록을 정의 한 후 참조 할 수 있지만 모델 클래스 내에 정의하면 해당 정보를 사용하는 클래스와 모든 정보가 유지되고 선택 사항을 쉽게 참조 가능하다.

> Student.SOPHOMORE는 Student 모델을 가져온 곳이라면 어디에서나 사용할 수 있다.

```python
from django.db import models

class Student(models.Model):
    FRESHMAN = 'FR'
    SOPHOMORE = 'SO'
    JUNIOR = 'JR'
    SENIOR = 'SR'
    YEAR_IN_SCHOOL_CHOICES = (
        (FRESHMAN, 'Freshman'),
        (SOPHOMORE, 'Sophomore'),
        (JUNIOR, 'Junior'),
        (SENIOR, 'Senior'),
    )
    year_in_school = models.CharField(
        max_length=2,
        choices=YEAR_IN_SCHOOL_CHOICES,
        default=FRESHMAN,
    )

    def is_upperclass(self):
        return self.year_in_school in (self.JUNIOR, self.SENIOR)
```

<br><br>

**db_column**

###### field.db_column

데이터베이스의 column 이름이 된다. 주어지지 않으면 django는 field의 이름을 사용한다.

<br><br>

**db_index**

######field.db_index

True 면 필드에 대해 데이터베이스 인덱스가 생성된다.

<br><br>

**db_tablespace**

###### Field.db_tablespace

오라클에 필요한 것 같다..pass

<br><br>

**default**

###### Field.default

필드의 기본 값. 값 또는 호출 가능한 객체 일 수 있다. 호출 가능하다면 새로운 객체가 생성 될 때마다 호출된다.
