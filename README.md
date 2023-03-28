# Deploying a FastAPI project on AWS Lambda

## Introduction
This guide outlines the steps required to deploy a FastAPI project on AWS Lambda using a separate requirements package and code-only zip file.

## Prerequisites:

Before you begin, you will need the following:

1. A sample FastAPI project
2. An AWS account with access to AWS Lambda and S3 services to deploy code

For this example, we will use a FastAPI project named "sample-fastapi-project" and an S3 bucket named "sample-fastapi-s3". We will also create an AWS Lambda function named "sample-fastapi-lambda".

## Step 1 — Create a virtual environment to isolate our app dependencies. 
```
  $  python3 -m venv venv/
```

## Step 2 - Activate the environment:
```
  $  source venv/bin/activate
```

## Step 3 - Install dependencies that are required for this project:
```
  $  pip install -r requiements.txt
  
  $ pip freeze > requiements.txt
```

## Step 4 - Add Mangum to the FastAPI code:
```
  $ pip install mangum
```
Import the Mangum module in your FastAPI code:
```
  from mangum import Mangum
```

Wrap the FastAPI app with Mangum:
```
  handler = Mangum(app)
```

Mangum allows us to wrap the API with a handler that we will package and deploy as a Lambda function in AWS.


## Step 5 - Create a separate requirements package zip file
```
  $ pip3 install -r requirements.txt -t ./packages
  $ cd packages
  $ zip -r9 ../requirements.zip .
```
This will create a seperate requirements.zip file

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
