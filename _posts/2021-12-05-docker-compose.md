---
title: Docker compose file
author: ianpowell
date: 2021-12-05 14:20:00 +0000
categories: [docker]
tags: [c#]
---
At the root of the project create a file: `docker-compose.yml`
```dockerfile
version: '3'
services:
  api:
    image: conference/api
    container_name: conference_api
    build:
      context: .
    ports:
      - 5000:80
    environment:
      ASPNETCORE_ENVIRONMENT: Production
    depends_on:
      - postgres
  postgres:
    image: postgres:9.6.3
    container_name: conference_db
    environment:
      POSTGRES_DB: conference
      POSTGRES_USER: conf_app
      POSTGRES_PASSWORD: docker
    ports:
      - 5432:5432
    volumes:
      - ./db:/docker-entrypoint-initdb.d
```
## To run the docker compose file
``` bash
docker-compose up
```
## To push your container to docker hub
``` bash
docker tag {imageId} {username name}/{container name}:{tag name}
```
## To log into your docker account
```bash
docker login
```

``` bash 
docker push {username}/{container name}:{tag name}
```
