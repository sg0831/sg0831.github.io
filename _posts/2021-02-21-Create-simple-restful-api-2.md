---
title: "[django] 간단한 restful API 만들기(Create a simple restful API) - (2)"
excerpt: "기본적인 setting과 url 맵핑 및 views 작성"
categories: [Mini project]
tags: [장고, 레스트풀, 백엔드, 메모, 앱, django, rest api, restful api, backend, memo, app]
toc: true
toc_sticky: true
date: 2021-02-21
last_modified_at: 2021-02-21
---


# [django] 간단한 restful API 만들기(Create a simple restful API) - (2)

## 지난 내용
지난 [ \[django\] 간단한 restful API 만들기 (1) ](https://sg0831.github.io/mini%20project/Create-simple-restful-api-1/) 포스팅에서는 파이썬 가상환경을 만들고 프로젝트에 필요한 ** django, djangorestframework** 를 설치했다.   
이번에는 django프로젝트 안에서 restful API를 위한 url 맵핑부터 view작성까지 진행할 것이다.   
   
   
## 필요한 App 구상 및 생성
지금 만들려고 하는 Restful API는 말만 좀 거창할 뿐이지 아주 간단한 예제이다.   
1. 생성(Create)
동기가 만들어 올 메모 애플리케이션에서 데이터를 전송하면 그걸 받아서 db에 저장하는 기능.
2. 조회(Read)
메모 애플리케이션에서 서버 DB에 저장되어 있는 데이터를 요청하면 바로 전달해주는 기능.
3. 수정(Update)
메모 애플리케이션의 요청에 따라 서버 db에 있는 특정 데이터를 수정하는 기능.
4. 삭제(Delete)
메모 애플리케이션의 요청에 따라 서버 DB에 있는 특정 데이터를 지우는 기능.

이상이다.   
보통은 한 프로젝트 안에 여러 개의 App을 만들어 각 기능별로 역할을 세분화시키지만,   
본 프로젝트에서 구현할 기능이 많지 않기에 **"connect"** 라는 이름의 App 하나만으로 모든 기능을 구현하려고 한다.   
그럼, 저번 시간에 만든 django프로젝트 안에 앱을 만들어보자.   

* py manage.py startapp connect

django프로젝트에서 **manage.py** 파일이 위치한 경로로 이동한 후, 터미널을 열어 위의 명령을 입력.   
가장 뒤에는 생성할 앱의 이름이 들어간다. (여기서는 ** connect ** 가 된다.)   
여기서 주의할 점, django의 프로젝트나 App를 생성할 때 사용할 수 없는 키워드가 몇 가지 있다.   
프레임워크에서 사용하고 있는 기능들과 충돌할 가능성이 있는 몇몇 키워드들은 django에서 사용을 금지한다.   
restframework, framework, django 등등, 이런 유형의 키워드가 들어가는 건 가급적 피하는 게 좋다.   
   
django에서는 새로운 앱을 생성할 때마다 프로젝트에 등록을 해주어야 한다   .
** manage.py ** 파일이 있는 경로에서 프로젝트 이름과 동일한 폴더를 연다. (여기서는 **memoBackend** )   
그 안에 있는 **settings.py** 파일을 열고 방금 만든 앱을 등록해준다.   
   
>memoBackend/settings.py
~~~{.python}
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',  # 추가
    'connect',  # 추가
]
~~~
   
방금 만든 connect앱과 Rest API사용을 위한 rest_framework앱을 추가한다.   
앱 등록을 마쳤으면, 하는 김에 필요한 설정들을 몇 개 진행해보자.   
   
>memoBackend/settings.py
~~~{.python}
LANGUAGE_CODE = 'ko-kr'  # 언어: 한국어

TIME_ZONE = 'Asia/Seoul'  # 시간대: 아시아/한국
~~~
언어와 시간대 설정을 ** 한국 ** 에 맞게 수정해준다.   
이 설정을 안 해주고 넘어가면 나중에 DB에서 시간과 관련된 데이터를 다룰 때 문제가 생길 수 있다.   
   
## URL 맵핑
앱 등록을 마치면, 이젠 이 프로젝트에 url 맵핑을 진행해 주어야 한다.   
방금 수정한 **settings.py** 와 동일한 경로에 위치한 **urls.py** 를 열어 url 맵핑을 진행해보자.   
   
>memoBackend/urls.py
~~~{.python}
from django.contrib import admin
from django.urls import path, include  # 맵핑을 위한 include

urlpatterns = [
    path('admin/', admin.site.urls),
    path("connect/", include("connect.urls") ),  # connect/로 들어오는 요청은 모두 connect/urls.py로 보내 처리하라는 의미
]
~~~
이제 **connect/** 로 시작되는 url을 통해 들어온 요청은 모두 **connect.urls.py** 파일에서 처리하게 된다.   
하지만 현재 connect폴더 안에는 **urls.py** 라는 파일이 존재하지 않는다.   
우리가 직접 생성해 주어야 한다.   
   
>connect/urls.py
~~~{.python}
\# url 맵핑을 위한 path, include
from django.urls import path, include
\# rest_framework에서 제공하는 url patterns format
from rest_framework.urlpatterns import format_suffix_patterns
\# views.py에 맵핑할 클래스 혹은 메소드
from .views import MemoView

memo_list = MemoView.as_view({
	'post': 'create',
	'get': 'list'
})
memo_detail = MemoView.as_view({
	'get': 'retrieve',
	'put': 'update',
	'patch': 'partial_update',
	'delete': 'destroy'
})

urlpatterns = format_suffix_patterns([
	path('auth/', include('rest_framework.urls', namespace='rest_framework')),
	path('', memo_list, name='memo_list'),
	path('<int:pk>/', memo_detail, name='memo_detail'),
])
~~~
일반적인 django의 urlpatterns와는 그 형태가 많이 다르다.   
django와 함께 설치했던 rest_framework에서 제공하는 기능을 이용했기 때문이다.   
원래는 **Create** , **Read** , **Update** , **Delete** 의 각각의 케이스마다 url을 따로 맵핑해주고, 각각의 views 메소드를 작성해 주어야 하는데...   
그렇게 하면 비슷한 코드를 반복적으로 작성하게 되는 일이 발생한다.   
djangorestframework는 이러한 반복적인 작업을 줄여주는 기능을 제공한다.   

* 이러한 기능이 매우 편리하고 신속한 개발을 가능하게 해주지만, Backend를 공부하는 입장에서는 과할 정도로 친절한 기능이라고 느껴진다. 다음에 기회가 되면 이러한 편의 기능을 쓰지 않고 직접 하나하나 맵핑해 보면서 코드를 작성해 보는 것도 좋을 것 같다.
   

## Model 작성
백엔드(Backend)는 기본적으로 MVC패턴에서 M(Model)과 C(Controller)를 담당하게 된다.   
그중 **M(Model)** 를 지금 생성해 보겠다.   
모델은 각 앱의 **models.py** 파일 안에서 작성한다.   
django에서는 모델을 클래스 형태로 선언한다.   
   
>connect/models.py
~~~{.python}
from django.db import models

\# Memo라는 이름의 테이블 생성
class Memo(models.Model):
	\# 글자수의 제한이 없는 텍스트 필드
	content = models.TextField()
	\# 데이터가 생성된 날짜를 DateTime형식으로 갖는 필드
	created = models.DateTimeField( auto_now_add = True )
	\# 데이터가 마지막으로 수정된 날짜를 DateTime형식으로 갖는 필드
	updated = models.DateTimeField( auto_now = True )
~~~
모델 생성이 완료되었다.   
데이터베이스에서 **'테이블'** 라고 부르는 것과 동일한 의미이다.   
   

## Serializers 작성
여기서 일반적인 django 개발과 다른 점이 하나 더 등장한다.   
지금 만들고 있는 API는 기본적으로 HTML 등의 템플릿(Template)이 필요하지 않다.   
View 기능을 책임지고 있는 모바일 애플리케이션이 존재하기 때문.   
이 API에서는 그저 JSON형식으로 데이터를 주고받을 수 있는 기능만 제공해주면 되는 것이다.   
그래서 파이썬 형식의 데이터를 JSON형식으로 변환해주는 코드가 필요한데, 지금 작성하는 **Serializers** 가 그것이다.   
   
>connect/serializers.py
~~~{.python}
\# JSON 변환 기능을 제공해주는 serializers
from rest_framework import serializers
\# JSON으로 변환할 model 정보 가져오기
from .models import Memo

class MemoSerializer( serializers.ModelSerializer ):
	class Meta:
		model = Memo
		fields = ( 'content', 'created', 'updated' )
		read_only_fields = ( 'created', 'updated' )
~~~
   
생각보다 별거 없다.   
이건 어차피 rest_framework가 제공해주는 기능이기 때문에 우리는 그저 가져다 쓰기만 하면 된다.   
   

## views.py에 create 메소드 작성
드디어 rest API의 메인 기능을 작성한다.   
이 단계가 MVC패턴에서 **C(Controller)** 에 해당한다.   
django에서는 **C(Controller)** 를 **V(View)** 라고 얘기한다.   
이유는 모르겠다. 왜 굳이 일반적인 이름과 다른 형식을 쓰는 건지...   
어쨌든 부르는 이름만 다를 뿐 그 의미는 동일하다.   
   
**views.py** 파일 안에 아래 코드를 작성.

>connect/views.py
~~~{.python}
\# 귀찮은 View 작성을 생략해주는 기능
from rest_framework import viewsets
\# 좀 전에 작성했던 JSON변환을 위한 serializers
from .serializers import MemoSerializer
\# Model에 접근
from .models import Memo
\# 접근 권한 관련
from rest_framework import permissions

\# 반복적인 View 작성을 대신 해주는 일종의 클래스 뷰
class MemoView( viewsets.ModelViewSet ):
	\# API에서 사용할 Memo의 db 셋팅
	queryset = Memo.objects.all()
	\# 작성한 serializer 명시
	serializer_class = MemoSerializer
	\# API 접근 권한 지정: 누구든지 접근 가능
	permission_classes = (permissions.AllowAny,)
~~~
   
rest_framework에서는 반복적인 View 작성 과정을 생략해주는 기능을 제공한다.   
어차피 API가 제공하는 **crud** 는 다 거기서 거기이기에 이런 단순반복작업(?)을 프레임워크 측에서 대신 진행해 주는 것이다.   
이렇게 강력한 기능을 제공해주기 때문에 django가 많은 사람들에게 사랑을 받고 있는 것 같다.   

하지만 위에서도 말했다시피 공부하는 입장에서는 과할 정도로 친절한 기능이다. 나중에 시간이 날 때 이런 편의 기능 없이 스스로 모든 기능을 구현해보는 것도 좋은 공부가 될 것 같다.
   
   

## Run server
기본적인 **crud** 기능 구현이 완료되었다.   
이제 로컬 서버를 돌려 모든 기능이 정상적으로 작동하는지 확인해봐야 하는데, 그 전에 한 가지 할 일이 더 남았다.   
우리가 작성한 모델(Model)을 django 프로젝트에 기록해 주어야 한다.   

* py manage.py makemigrations

그리고 이어서 db에 적용하는 과정까지 거쳐야 한다.   

* py manage.py migrate

모든 과정이 끝났다면, 드디어 Run server를 통해 테스트를 진행할 수 있다.   

* py manage.py runserver

로컬 서버가 정상적으로 열렸다면, 웹브라우저에 http://localhost:8000/를 입력한다.   
django가 제공하는 Default 페이지가 나타날 건데, 지금 중요한 건 이게 아니고 우리가 만든 connect 앱이 잘 작동하는지 확인을 해준다.   

* http://localhost:8000/connect/

**connect/urls.py** 에서 작성했던 urlpatterns에 맞게 url을 입력.   
   
그러면 django_rest_framework가 제공해주는 API 테스트 페이지가 나타난다.   
전문적인 API 테스트 툴이 없어도 간단하게 API 기능을 테스트할 수 있는 페이지이다.   
또한, Python 프로그램을 새로 작성해 직접 request 테스트를 진행해보는 것도 좋은 방법이다.   
   
   

## 마치며
오늘 작성한 코드의 내용을 전부 이해하지는 못했다.   
특히 viewset과 관련된 내용은 아직도 정확히 모르겠다.   
언젠가 viewset을 사용하지 않고 직접 맵핑을 진행해보면서 rest API의 원리를 보다 자세히 깨달을 수 있는 시간을 가져야 되겠다.   
   
지금까지는 로컬 서버에서만 테스트를 진행했다.   
다음 포스팅에서는 외부에서도 이 API를 이용할 수 있도록 실제 **서버 호스팅** 을 진행해보겠다.