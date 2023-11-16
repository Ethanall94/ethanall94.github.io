---
layout: posts
title:  "Docker & Docker Hub"
date:   2023-11-03 15:45:00 +0900
categories: Docker, Shell
---
<aside>
💡 Python Django 프로젝트를 Docker Hub 에 올려봅시다!

</aside>

### 사전준비

---

[Django Project 시작하기](https://www.notion.so/Django-Project-de9203a50b264cafbc52aa8a84bfd6a8?pvs=21)

### 과정

---

* 사전 준비에서 사용한 `project` 를 그대로 사용한다고 가정합니다.

1. Docker를 설치합니다.
2. [Docker Hub](https://hub.docker.com)에 가입합니다.
3. `Create repository` 를 선택해 repository를 생성합니다.
4. Docker 프로그램을 실행시켜 로그인해줍니다.

1. 가상환경이 켜진 상태의 터미널에서
    
    프로젝트 파일 최상위 경로 아래 requirements.txt 를 생성합니다.
    
    ```bash
    (django_env)프로젝트 파일 최상위 경로$ pip freeze > requirements.txt
    ```
    

1. `Dockerfile` 을 다음과 같이 작성합니다.
    
    ```docker
    FROM python:3.11    # version
    ENV PYTHONUNBUFFERED 1
    WORKDIR /app
    COPY requirements.txt requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt
    COPY . .
    EXPOSE 80    # port
    CMD ["python", "omg_project/manage.py", "runserver", "0.0.0.0:8000", "--noreload"]
    ```
    
2. 터미널에서 다음과 같이 입력해 `project` 이미지 파일을 생성합니다.
    
    ```bash
    (django_env)프로젝트 파일 최상위 경로$ docker build --tag [docker id]/[repository] .
    ```
    
    * docker 프로그램이 실행된 상태에서 동작하는 코드입니다.
    
    * 또는 docker build --platform linux/amd64 -t project .
    
    * 최상위 경로 아래 `django_env`, `project`, `requirement.txt`, `Dockerfile` 이 있습니다.
    
3. 이미지 파일을 Docker Hub에 올립니다.
    
    ```bash
    docker push [docker id]/[repository]
    ```
    

1. Docker Hub repository에서 잘 올라왔는지 확인합니다.

---