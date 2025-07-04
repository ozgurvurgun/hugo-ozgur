---
title: Deploy streamlit app to aws with elastic beanstalk
description:
date: 2023-03-30 
draft: false
tags:  ecr #aws #ebcli #elasticbeanstalk #docker #dockercompose #streamlit #tutorial
---


[Streamlit](https://streamlit.io/) is a framework for creating data apps with python.

I've built a sandbox application for testing the amazon personalization engine. I use streamlit as a framework and python as a language. 

After completing the job, I had to deploy to AWS. This is the way I followed. 
<!--more-->
My stack
- [python 3.9.2](https://www.python.org/)
- [pipenv](https://pipenv.pypa.io/en/latest/)
- [direnv](https://direnv.net/)
- [gnu-make](https://www.gnu.org/software/make/)
- [docker](https://www.docker.com/)
- [docker-compose](https://docs.docker.com/compose/)
- [aws cli](https://aws.amazon.com/cli/)
- [eb cli](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html)
- [sed](https://www.gnu.org/software/sed/manual/sed.html)
- [git](https://git-scm.com/)

Services
- [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)
- [AWS Elastic Container Registry](https://aws.amazon.com/ecr/)
- [AWS IAM Role Manager](https://aws.amazon.com/iam/)
 
The first task is creating a docker image for streamlit application. This is the example;

```
FROM python:3.9-slim

WORKDIR /app

RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    software-properties-common \
    git \
    && rm -rf /var/lib/apt/lists/*

COPY . /app

RUN /usr/local/bin/python -m pip install --upgrade pip
RUN pip3 install -r requirements.txt

ARG AWS_ACCESS_KEY_ID
ARG AWS_SECRET_ACCESS_KEY
ARG AWS_DEFAULT_REGION=us-west-2

ENV AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
ENV AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
ENV AWS_DEFAULT_REGION=$AWS_DEFAULT_REGION

EXPOSE 8501

HEALTHCHECK CMD curl --fail http://localhost:8501/_stcore/health

ENTRYPOINT ["streamlit", "run", "app.py", "--server.port=8501", "--server.address=0.0.0.0"]
```

The only difference from the original streamlit docker image is AWS tokens. I used that tokens in my app, so I had to import them into the image. 

Then I configured docker-compose.yaml file. I will use that file with elastic beanstalk. 

```
version: "3.4"
services:
  streamlit:
    image: ACCOUNTID.dkr.ecr.REGION.amazonaws.com/IMAGENAME:TAG
    ports:
      - 80:8501
```

It will fetch the image from ECR with the image name. We will build our docker image and then push it to the ECR. 

And the time is creating Makefile, which contains building the docker image, pushing to ECR, and updating docker-compose file shortcuts.

```
AWS_REGION ?= us-west-1 # its for pushing image to ecr
ECR_ENDPOINT ?= ""
IMAGE_NAME ?= "delirehberisandbox"
TS=$(shell date +'%Y%m%d%H%M%S')
TAG=${IMAGE_NAME}:v${TS}

deploy: ## Rebuild docker image with a new tag and push to ECR
	docker build --build-arg AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID} --build-arg AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY} --build-arg AWS_DEFAULT_REGION=${AWS_DEFAULT_REGION} -t ${TAG} .
	aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_ENDPOINT}
	docker tag ${TAG} ${ECR_ENDPOINT}/${TAG}
	docker push ${ECR_ENDPOINT}/${TAG}
	$(shell sed -i 's/\(^ *image: *\).*/\1${ECR_ENDPOINT}\/${TAG}/' docker-compose.yaml)
	git add docker-compose.yaml
	git commit -m "autocommit: image version updated"
	eb deploy
.PHONY: rebuild


help: ## Show this help
	@echo ${TAG}
	@echo "\nSpecify a command. The choices are:\n"
	@grep -E '^[0-9a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "  \033[0;36m%-12s\033[m %s\n", $$1, $$2}'
	@echo ""

.PHONY: help
```

We can use the `make deploy` command after that. But wait, we didn't ready yet. 

Let's go to the AWS console with a web browser and follow these steps;

- Open the `Elastic Container Registry` -> `Repositories` -> `Create a new repository` page and give the name to the repository. It must be the same `IMAGE_NAME` variable inside Makefile. For me, its `delirehberisandbox` 
- Open the `Elastic Beanstalk` -> `Environments` page and click `Create new environment` button. Select `Web Server Environment` and click `Next`. Give an application name, i gave `delirehberi` as application name. On the platform section, choose `Managed Platform` and choose `Docker` as the platform. Then click the `Create Environment` button. It will start to create environment with example code. 
- Open the `IAM`->`Roles` page and give `AmazonEC2ContainerRegistryReadOnly` permission to `aws-elasticbeanstalk-ec2-role` and `aws-elasticbeanstalk-service-role` roles. 

And we are closing the final steps. You have to configure AWS credentials in your local. I am using direnv to handle environment variables by folder. Create a `.envrc` file in the project root. 

```
dotenv .env
```

Then create a .env file and fill in the credentials;

```
AWS_ACCESS_KEY_ID=CHANGE
AWS_SECRET_ACCESS_KEY=CHANGE
AWS_REGION=us-west-2
ECR_ENDPOINT=ACCOUNTID.dkr.ecr.REGION.amazonaws.com
IMAGE_NAME=delirehberi
```

And allow to export with `direnv allow` 

So, now we will init elasticbeanstalk in root directory. Just run `eb init -p docker YOUR_APPLICATION_NAME` 

It will create a config.yaml file inside .elasticbeanstalk folder like this. 

```
branch-defaults:
  master:
    environment: Delirehberisandbox-env
    group_suffix: null
global:
  application_name: delirehberi
  branch: master
  default_ec2_keyname: null
  default_platform: Docker
  default_region: us-east-2
  include_git_submodules: true
  instance_profile: null
  platform_name: null
  platform_version: null
  profile: null
  repository: null
  sc: git
  workspace_type: Application
```
You can configure that file for your needs. 

Finally, we can run `make deploy` command. It will build an image, then push it to ecr, then update the docker-compose file, and then it will add changes to git history and will deploy to your elasticbeanstalk environment. 

