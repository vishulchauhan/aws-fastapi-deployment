# How to Dockerize a Django Application

## Introduction
In this tutorial, you will dockerize a django application using Docker container. Docker is an open platform for developing, shipping, and running applications. Docker provides the ability to package and run an application in a isolated environment called a container.
Containers are lightweight and contain everything needed to run your application, so you do not need to rely on what is currently installed on the host.

## Prerequisites:

You need following things to start work:
1. Python3 installed
2. Docker installed
3. Postgresql installed

## Step 1 — Create a virtual environment to isolate our app dependencies. On windows machine, you can install virtualenv module to create virtual environment and on linux, you can use venv module. I am using venv here to create a virtual environment on my linux machine.
```
  $  python -m venv venv/
```

## Step 2 - A folder named venv will be ceated on the location where you ran above command. Let's activate the environment, type:
```
  $  source venv/bin/activate
```

## Step 3 - Now that you are inside your virtual environment, let’s install dependencies that are required for this project:
```
  $  pip install django psycopg2-binary
```

    Django is the framework that you will be using to develop your web app using python and psycopg2-binary is the python module which works as a adapter between postgresql and django.

## Step 4 - In this step, you will start a new django project by using following command.
```
  $ django-admin startproject myproject
```
    Now your project directory should look like this:

![create_A_record](../images/django-project.png)

## Step 5 - To test your application locally, First enter your project directory and run the following command.
```
  $ python manage.py runserver
```
## Step 6 - Navigate to http://localhost:8000/ to view the Django welcome screen. Kill the server once done by pressing ctrl+z.

## Step 7 - Let's edit 'ALLOWED_HOST' in settings.py file as shown in image below:

![create_A_record](../images/allowed_host.png)

## Step 8 - In this step, you will edit database connection settings for postgresql in settings.py file present into your django project. First, import os module and change the following lines in settings.py file as shown in image below:

![create_A_record](../images/database-settings.png)
    

## Step 9 - Now, you will create a requirements.txt file in your project's root directory. Use below command to automatically create a requirements.txt and copy all dependencies in it.
```
  $ pip freeze > requirements.txt
```

## Step 10 - In this step, you will create a Dockerfile in your project's root directory and add the following lines as shown in image below.

![create_A_record](../images/dockerfile.png)

## Step 11 - Let's create a docker-compose.yml file in project's root directory and add the following lines in it:
```
  services:
  postgres:
    image: postgres:latest
    ports:
      - "5432:5432"
    environment:
      - DB_USERNAME=postgres
      - DB_PASSWORD=password
      - DB_NAME=myproject_db
      
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - ./myproject:/code
    ports:
      - "8000:8000"
    environment:
      - DB_NAME=myproject_db
      - DB_HOST=postgresql
      - DB_PORT=5432
      - DB_USERNAME=postgres
      - DB_PASSWORD=password
    depends_on:
      - postgres
  
  migration:
    build: .
    command: python manage.py migrate --noinput
    volumes:
      - ./myproject:/code
    environment:
      - DB_NAME=myproject_db
      - DB_HOST=postgresql
      - DB_PORT=5432
      - DB_USERNAME=postgres
      - DB_PASSWORD=password
    depends_on:
      - postgres
```
	In docker-compose.yml file, you define your services to be served inside your docker container with specific syntax which docker will use to read and manage execution of defined services. In above file, following services have been defined:

## Step 12 - Finally, Run your django app using below command:
```
docker compose up --build
```
