# 🐋Docker 🐍Flask 🐘PostgreSQL

**Guide to connecting Docker, Flask and PgAdmin:** <br>  
**Creating a Local Development Environment for Web Applications with a PostgreSQL Database.**

## Setup Docker Compose
Docker Compose starts two containers

### Docker Compose: docker-compose.yml
In this file, we define two services: the **web service**, which is your Flask service, and the **db service**, which is your PostgreSQL database.

- **web Service**

    In the **web service**, we define a `build context` for your **Flask** backend, specifying that we want to mount the ./back_end folder as /app in the container. This allows us to develop the backend code outside the container and execute it inside. In addition, we map port 5000 of the container to port 5000 of the host system to allow access to your Flask service.

- **db service**

    In the **db service**, we simply define the postgres:16 image as the PostgreSQL database service. It exposes port 5432 inside the container and maps port 5433 of the host system to this port.<br>
    We also set `environment variables` for the user name (POSTGRES_USER), password (POSTGRES_PASSWORD) and database name (POSTGRES_DB) to configure the PostgreSQL database.

### Dockerfile

This file defines how to create the image for your Flask service:<br>
 1) We start with the python:3.7 base image,
 ```
 FROM python:3.7
 ```
 2) we set the working directory as /app,`
 ```
 WORKDIR /app
 ```
 3) We set the environment variables for the Flask application (FLASK_APP) and the execution host (FLASK_RUN_HOST).
 ```
 ENV FLASK_APP main.py
 ENV FLASK_RUN_HOST 0.0.0.0
 ```
 4) We copy all files in the current directory (.) into the working directory of the container.
 ```
 COPY . .
 ```
 5) Next, we install the dependencies listed in the requirements.txt file.
 ```
 RUN pip install -r requirements.txt
 ``` 
 6) Finally, we specify the command to run when the container is started, which is python main.py to start the Flask server. 
 ```
 CMD ["python", "main.py"]
 ``` 

### requirements.txt
This file contains the list of Python dependencies needed to run the Flask application. It includes [flask](https://flask.palletsprojects.com/en/3.0.x/), [flask_cors(https://flask-cors.readthedocs.io/en/latest/)] for cross-origin communication support, and [flask_sqlalchemy](https://flask-sqlalchemy.palletsprojects.com/en/3.1.x/) for interfacing with the database.

> [!NOTE]
> With this setup, Docker Compose starts two containers: one for the Flask service and one for the PostgreSQL database. The Flask service is able to communicate with the PostgreSQL database using the hostname of the db service defined in Docker Compose (db) and the port exposed by the PostgreSQL service (5432).

## Creating the Postgres server in pgAdmin

### 🐋🐍 BUILD of the Docker image for the Flask (web) service:
1) First of all, it is necessary to **build the Docker image** for the Flask service.
    ```
    docker-compose build web
    ```
    This step prepares the execution environment for the Flask application by installing the dependencies defined in the requirements.txt file and configuring the Flask server.
    > [!IMPORTANT]
    > It is important to note that the Flask service depends on the PostgreSQL (db) database service, so Docker Compose ensures that the database service is started before the Flask service.

2) Once you have built the Docker image for the Flask service, you can start the **Flask service**.
    ```
    docker-compose up web
    ```
    This command **starts the Flask service container** and connects it to the **PostgreSQL database**, allowing the application to communicate with the database.





