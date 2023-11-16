---
layout: posts
title:  "Django Project 시작하기"
date:   2023-11-12 12:26:00 +0900
categories: Backend, Django, Python
---
<aside>
💡 Python Django로 프로젝트를 시작해봅시다!

</aside>

### 과정

---

1. 폴더를 하나 만들어 줍니다.

1. `django_env` 라는 이름의 가상 환경을 만들어 줍니다.
    
    ```bash
    $ python3 -m venv django_env
    ```
    

1. 가상환경을 켜줍니다. (Mac os 기준)
    
    ```bash
    $ source django_env/bin/activate
    ```
    
    터미널에 이렇게 뜨면 성공!
    
    ```bash
    (django_env) ~ $
    ```
    

1. django를 설치해줍니다.
    
    ```bash
    pip install django
    ```
    

1.  `startproject` 로 `project` 라는 이름의 프로젝트를 생성해줍니다.
    
    ```bash
    django-admin startproject project
    ```
    

1. `startapp` 으로 `main` 이라는 이름의 app을 생성해줍니다.
    
    ```bash
    python manage.py startapp main
    ```
    

1. 프로젝트 `project` 로 이동합니다.
    
    ```bash
    cd project
    ```
    

1. `runserver` 를 통해 웹페이지의 `127.0.0.1:8000` 에서 프로젝트를 확인합니다.
    
    ```bash
    project$ python manage.py runserver
    ```
    
    * 시작 전 `migrate`
    
    ```bash
    project$ python manage.py migrate
    ```
    

---