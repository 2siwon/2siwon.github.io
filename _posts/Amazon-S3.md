## django-storages로 Amazon S3 서비스 활용하기

##### 참고 및 인용
- [Django File storage API](https://docs.djangoproject.com/ko/1.11/ref/files/storage/)
- [django-storages](http://django-storages.readthedocs.io/en/latest)
- [django-storage를 통한 다양한 스토리지 서비스  활용하기](https://nomade.kr/doc/django/django-storages%EB%A5%BC-%ED%86%B5%ED%95%9C-%EB%8B%A4%EC%96%91%ED%95%9C-%EC%8A%A4%ED%86%A0%EB%A6%AC%EC%A7%80-%EC%84%9C%EB%B9%84%EC%8A%A4-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0/)


<hr>

`Django` 에서는 "File Storage API"를 이용해 파일관리를 지원한다. [Django File storage API](https://docs.djangoproject.com/ko/1.11/ref/files/storage/)

하지만 FileSystemStorage를 통해 로컬 파일시스템으로의 저장만을 지원하고 있으므로, Azure Storage나 AWS S3 등의 스토리지는 지원하지 않는다.

`Awesome Django` 에서는 4가지 라이브러리를 지원한다.

- django-storages : 다양한 스토리지 백엔드 지원
- django-selectel-storage : 특정 서비스 Selectel 클라우드 스토리지 백엔드 지원
- django-queued-storage : Celery를 통한 스토리지 백엔드 지원
- django-webdav-storage : WebDAV 스토리지 백엔드 지원

이 중에서 `django-storages` 는 아래와 같은 여러 스토리지 서비스를 지원하고있다.

- Azure Storage
- `AWS S3`
- Google Cloud Storage
- Dropbox
- FTP
- SFTP
- Apache Libcloud



상세 정보는 [django-storages](http://django-storages.readthedocs.io/en/latest)를 통해 확인 할 수 있다.

<br><br>

## django-storages 설치

#### 필요한 패키지 설치

```python
pip3 install django-storages
```

#### settings.py 앱 추가

```python
INSTALLED_APPS = [
    # 중략
    'storages',
]
```

위 과정만으로 기본설치가 끝났으며, 지원할 스토리지 서비스에 따라 추가 셋업이 필요하다. 추가로 스토리지 셋업을 완료하시면, FileField/ImageField를 통해 업로드된 파일은 즉시 지정 스토리지에 복사된다. 그리고  model_instance.field.url 로 접근 시에, 해당 스토리지에 대한 URL을 획득하실 수 있다.

<br><br>

## boto3

`boto3`는 Python 개발자가 Python 용 AWS SDK를 사용하여 ec2, S3등 아마존 서비스의 업로드/다운로드 API를 포팅한 라이브러리이다.


#### To use boto3 set

```python
DEFAULT_FILE_STORAGE = 'storages.backends.s3goto3.S3Boto3Storage'
```

<br><br>

#### DEFAULT\_FILE\_STORAGE

기본적으로 정적파일 중 유저 업로드 `미디어`에 대해서만 동작하는 저장소

`DEFAULT_FILE_STORAGE` 는 파일을 저장핧 때 실제로 장고 로컬 시스템에 있는 파일 구조를 사용한다. 이 내용을 boto3에서 재정의하여 제공한다.

<br><br>

#### STATICFILES_STORAGE

만약 `static`파일도 S3를 통해 관리하고 싶다면 STATICFILES_STORAGE 라는 속성을 재정의 해야 한다.

```python
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
```

<br><br>

#### bucket

s3자체는 한개밖에없다 rds서버 자체는 여러개 존재할수있는데 s3는 사용자s3서비스1개 프로젝트단위가 버킷