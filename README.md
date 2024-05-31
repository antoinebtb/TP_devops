# DevOps Project: Docker Configuration for 3-Tier Application

This README outlines the necessary steps and configurations for setting up a robust 3-tier application using Docker. This setup includes a PostgreSQL database, a backend API, and an HTTP server.

## Why Attach a Volume to PostgreSQL Container?

Attaching a volume to your PostgreSQL container ensures that your data is secure, accessible, and independent of the container lifecycle. It's a standard practice for database management in Docker environments to ensure data durability and reliability.

## Commands List

### Network Configuration
First, create a Docker network to enable inter-container communication:

```bash
docker network create app-network
```

### Build the Docker Image

```bash
docker build -t <image_name> .
```

```bash
docker build -t <image_name> .
```

```bash
docker build -t <image_name> .
```

### Run the Docker Container

```bash
docker run --name <nom_du_conteneur_postgres> -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd --network app-network -d <Tag_name_docker_image_bdd>
```

```bash
docker run --name <nom_du_conteneur_backend> -p 8090:8080 --network app-network <Tag_name_docker_image_backend>
```

```bash
docker run -dit --name <nom_du_conteneur_http> -p 8080:80 <Tag_name_docker_image_http>
```

The best alternative is to use Docker-compose. We created a docker-compose file to build all the images and run all the docker container. You can use it by using in the right folder. This will setup the project with the container and images name that i created.

```bash
docker-compose up
```


### Docker Login and Image pushing 

```bash
docker login
docker tag <Tag_name_docker_image_bdd> <your_dockerhub_username>/postgres_image:<tag>
docker push <your_dockerhub_username>/postgres_image:<tag>
docker tag <Tag_name_docker_image_backend> <your_dockerhub_username>/backend_api_image:<tag>
docker push <your_dockerhub_username>/backend_api_image:<tag>
docker tag <Tag_name_docker_image_http> <your_dockerhub_username>/http_server_image:<tag>
docker push <your_dockerhub_username>/http_server_image:<tag>
```
## Why do we need a multistage build? And explain each step of this dockerfile.

By handling the build and packaging within the Dockerfile, we encapsulate the entire build process in one script. 

### Explanation of Each Step in the Dockerfile

## Build Stage:

```dockerfile
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests
```
##### FROM maven:3.8.6-amazoncorretto-17 AS myapp-build:

This line starts the first stage of the build using a Maven image with Amazon Corretto 17. The image includes both the JDK and Maven, which are required to build Java applications. AS myapp-build names this stage so that it can be referenced later.

##### ENV MYAPP_HOME /opt/myapp: 

Sets an environment variable MYAPP_HOME which specifies the directory where the application will be built.

##### WORKDIR $MYAPP_HOME:

 Sets the working directory for the subsequent instructions. In this case, it's set to /opt/myapp, where the source code and build artifacts will reside.

##### COPY pom.xml .:

 Copies the Maven project descriptor file (pom.xml) to the current working directory in the image (which is /opt/myapp).

##### COPY src ./src:

 Copies the application's source code into the src directory of the working directory in the image.

##### RUN mvn package -DskipTests:

 Runs Maven to build the project and package it into a JAR file, skipping tests to speed up the build process.