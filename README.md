CI-CD in AWS

- To see the code sample you can see this project - [simple react app](https://github.com/itzrahulyadav/myportfolio.git)
### CodeBuild
- Create a codebuild project
- use the following buildspecfile
```
version: 0.2    
phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...          
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG      
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG


```

- Add the environment variables in the environment option of the project
- click on edit --> click on environment --> add env variables
- To allow to run docker commands follow the steps:
-  click on edit --> click on environment --> add env variables -->click on override image and select the checkbox called privilaged.

### Pushing the code to s3
- To push the artifacts to s3
- use the following buildspec file

```
version: 0.1

phases:
  install:
    runtime-version:
      nodejs: 10
    commands:
      - echo Getting Started
  pre_build:
    commands:
    - echo Installing source NPM dependencies
    - npm install
    - aws --version
  build:
    commands:
      - echo Build started on 'date'
      - echo first test codes
      - echo compiling the codes
      - npm run build
      - echo Build finished, now moving to s3

  post_build:
    commands:
      - echo Build completed on 'date'
      - aws s3 sync build/ s3://rahul-demo-site --acl public-read


artifacts:
  files:
    - 'build/*'
  name: demo-portfolioartifact

```

- Note:There should be a bucket created as shown in post_build command.

### CodeDeploy

- launch an instance
- give permission to s3readonlyaccess
- install codedeploy agent using the following steps

```
sudo yum update -y
sudo yum install ruby 
sudo yum install wget
wget https://bucket-name.s3.region-identifier.amazonaws.com/latest/install
# replace bucket-name with aws-codedeploy-ap-south-1 and region-identifier with ap-south-1
# the code for ap-south-1 region will be
# wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent status

```



