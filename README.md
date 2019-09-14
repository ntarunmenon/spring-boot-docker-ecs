# spring-boot-docker-ecs
spring-boot-docker-ecs

## Objective

- Create a simple Spring Boot API & Dockerize it. 
- Deploy this application to an EC2 instance and test against POSTMAN
- Use AWS code pipeline and code build to push the image to docker in ECR. 
- Deploy the application using ECS and test the application. 
- Use code pipeline and code deploy to deploy the application to ECS. 


### Create a simple Spring Boot API & Dockerize it

```groovy
./mvnw install dockerfile:build
 docker run -p 8080:8080 -t springio/spring-boot-docker-ecs

```

Run `http://localhost:8080/` and verify that `Hello Docker World` is being shown.