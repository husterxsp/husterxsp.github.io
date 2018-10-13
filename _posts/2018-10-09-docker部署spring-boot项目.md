---
layout: post
title: docker部署spring-boot项目
date: 2018-10-09
author: "Xsp"
catalog: true
tags:
    - Java
    - Docker
---



参考官网：https://spring.io/guides/gs/spring-boot-docker/



`Dockerfile`

```dockerfile
FROM openjdk:8-jdk-alpine
VOLUME /tmp
COPY ./target/controlserver-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```



```
$ docker build . -t check-control-server
$ docker run check-control-server
```

