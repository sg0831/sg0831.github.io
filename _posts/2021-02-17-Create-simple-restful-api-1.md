---
title: "[django] 간단한 restful API 만들기(Create a simple restful API) - (1)"
excerpt: "가상환경 설정 및 필요한 프레임워크 설치"
categories:
- 미니 프로젝트(Mini project)
tags:
- [장고, 레스트풀, 백엔드, 메모, 앱, django, rest api, restful api, backend, memo, app]
toc: true
toc_sticky: true
date: 2021-02-17
last_modified_at: 2021-02-17
---

# [django] 간단한 restful API 만들기

## 프로젝트 시작 동기
이번에 대학 동기와 함께 코딩 공부를 진행하게 되었다.   
서로 아는 게 없어서 참 쉽지 않은 상황이지만, 아주 자그마한 프로젝트(?)를 만들어보면서 프로그래밍 능력을 향상시키려고 한다.   
동기는 프론트엔드를, 나는 백엔드를 맡았다.   
프론트 백을 굳이 나눌만큼 거창한 프로젝트는 절대로 아니지만, 우리는 굳이 나누기로 결정했다.   
내가 restful-API를 공부해야 하기 때문...   

## 사용할 기술
백엔드(Backend)는 그나마 사용해본 경험이 있는 python과 django 프레임워크를 사용하기로 결정했다.   
이번에는 웹페이지와 통신하는 게 아니라 모바일 앱으로 통신할 것이기에 restful API를 만들어볼 예정.   
___

## 프로젝트 시작
먼저 장고 프로젝트를 생성하기 전, 가상환경을 설정해줘야 한다.   

>python -m venv django-memo

맨 뒤에 오는 부분이 바로 이 **가상환경의 이름** 이다.   
보통 한 프로젝트당 하나의 가상환경을 사용한다.   
   
가상환경을 만들었으니 **가상환경을 실행** 해보자.   
방금 만들어진 가상환경 폴더(django-memo) 안으로 이동.   
**Scripts\activate** 라는 파일을 실행해주면 가상환경이 실행된다.   
   
>Scripts\activate

윈도우에서는 이와 같은 cmd 명령으로 실행할 수 있다.   
이렇게 가상환경이 실행되면, cmd창의 경로 앞부분에 해당 가상환경의 이름이 붙는다.   
이게 붙어있으면 현재 가상환경을 실행 중이라는 의미.   
그럼 이제 django 프레임워크를 설치해보자.   
   
>pip install django

장고 프레임워크를 설치한 다음에는 djangorestframwork도 추가로 설치해준다.   
이번에는 **restful API** 를 만들어볼 것이기 때문이다.   

>pip install djangorestframework

필요한 프레임워크가 모두 설치되었다.   
   
가상환경이 있는 폴더와 소스 코드가 섞이는 게 별로 보기 좋지 않아서 소스 코드를 넣을 'src'라는 폴더를 추가로 생성했다.   

## django 프로젝트 생성
이제 장고 프로젝트를 만들어보자.   

>django-admin startproject memoBackend

일단 django 프로젝트를 생성한 후에는 항상 runserver을 먼저 해준다.   
다른 사람들의 포스팅을 보니 모두 그렇게 하는 거 같은데, 프로젝트 생성이 정상적으로 됬는지 확인하려는 의도인 듯하다.   

>python manage.py runserver

이 명령을 통해 **로컬 서버** 를 열 수 있다.   
맨 앞에 있는 "python"은 "py"로 줄여서 사용 가능하다.   
나는 주로 "py"로 줄여서 사용한다.   

>http://localhost:8000/

웹브라우저를 실행하여 로컬 서버가 정상적으로 열리는지 확인.   
django는 기본적으로 8000포트를 사용한다.   

## git commit 진행
아무런 문제가 없다면, 현재 이 클린한 상태의 프로젝트를 커밋해준다.   
앞으로 작업을 하다가 무슨 일이 생길지 모르기 때문에 초기 상태로의 회귀를 위한 커밋이 필요하다.   
최상위 디렉터리로 이동한 후 git 저장소를 생성.   
가상환경까지 저장소에 포함해야 하기에 최상위에서 저장소를 생성했다.   

>git init

이어서 하위에 있는 모든 파일을 **git이 추적** 하게끔 한다.   

>git add .

그리고 바로 commit까지 진행.   

>git commit -m "start project"

이렇게 초기 상태의 commit를 완료했다.   

## 마치며
이번 포스팅에서는 본격적인 코딩을 들어가기 전에 필요한 설정들을 진행했다.   
django 프로젝트에서 새로운 앱을 만들고 코드를 작성하는 건 다음 포스팅에서 이어간다.   
