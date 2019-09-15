# spring-boot-docker-ecs
spring-boot-docker-ecs

## Objective

- Create a simple Spring Boot API & Dockerize it. 
- Use AWS code pipeline and code build to push the image to docker in ECR. 
- Deploy this application to an EC2 instance and test against POSTMAN
- Deploy the application using ECS and test the application. 
- Use code pipeline and code deploy to deploy the application to ECS. 


### Create a simple Spring Boot API & Dockerize it

```groovy
./mvnw install dockerfile:build
 docker run -p 8080:8080 -t springio/spring-boot-docker-ecs

```

Run `http://localhost:8080/` and verify that `Hello Docker World` is being shown.

### Use AWS code pipeline and code build to push the image to docker in ECR. 

#### Use code build to push the changes to a docker repo after reading from Github

_How to keep account number as environment variable which will then be refered inside buildpec.yml_

- Create an SSM parameter store.
- Refer the parameter store inside 'buildspec.yml'