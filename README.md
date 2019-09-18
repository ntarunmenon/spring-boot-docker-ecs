# spring-boot-docker-ecs
spring-boot-docker-ecs

## Objective

- Create a simple Spring Boot API & Dockerize it. 
- Use AWS code pipeline and code build to push the image to docker in ECR. 
- Deploy this application to an EC2 instance and test using CURL
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
- Make sure code build service role can do the operation ssm:GetParamaters. This can be configred from IAM.

_How to get the build to execute?_

- Make sure code build service role can do the operation ecr:GetAuthorizationToken. This can be configred from IAM.

2 Issues to solve

- Introduce code build cache. 

I need to cache maven repo. So where is the repo being created ?
The AWS example is saying the folder /root/m2 but then I had to over ride MAVEN_CONFIG to empty as this was causing ./mvnw to fail.

I need to redirect repo to a local folder and then cache that folder. 

- ./mvnw is not able to find docker. 


### Deploy this application to an EC2 instance and test using CURL

1. Create an ubuntu instance in EC2 console.
2. SSH into the instance.
3. Execute the following set of commands

```

sudo snap install docker
sudo groupadd docker
sudo usermod -aG docker $USER
sudo snap restart docker

```

4. Create an policy as follows in IAM 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage",
                "ecr:BatchCheckLayerAvailability"
            ],
            "Resource": "arn:aws:ecr:ap-southeast-2:#Your Account No#:repository/springio/spring-boot-docker-ecs"
        },
        {
            "Sid": "GrantECRAuthAccess",
            "Effect": "Allow",
            "Action": "ecr:GetAuthorizationToken",
            "Resource": "*"
        }
    ]
}
```
5. Attach the above role to the EC2 instance from console. 

6. Run the following set of commands to pull the ECR image from docker. 

```
 ## If the below command does not work then reload bash or log out and login again
 $(aws ecr get-login --no-include-email --region ap-southeast-2)

## The below command will pull the image from docker
 docker pull #account_no#.dkr.ecr.ap-southeast-2.amazonaws.com/springio/spring-boot-docker-ecs

## Get the image id and run it using the following commands

docker ps
docker run 2ad4ea4d2bdc &

```

7. Edit the inbound rule assosciated with your security group to allow requests to 8080.

8. Test the command using the following command

```
curl ec2-3-104-105-220.ap-southeast-2.compute.amazonaws.com:8080
```