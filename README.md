# Udagram Image Filtering Application

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.

The project is split into two parts:
1. Frontend - Angular web application built with Ionic Framework
2. Backend RESTful API - Node-Express application

## Getting Started
> _tip_: it's recommended that you start with getting the backend API running since the frontend web application depends on the API.

### Prerequisite
1. The depends on the Node Package Manager (NPM). You will need to download and install Node from [https://nodejs.com/en/download](https://nodejs.org/en/download/). This will allow you to be able to run `npm` commands.
2. Environment variables will need to be set. These environment variables include database connection details that should not be hard-coded into the application code.
#### Environment Script
A file named `set_env.sh` has been prepared as an optional tool to help you configure these variables on your local development environment.
 
We do _not_ want your credentials to be stored in git. After pulling this `starter` project, run the following command to tell git to stop tracking the script in git but keep it stored locally. This way, you can use the script for your convenience and reduce risk of exposing your credentials.
`git rm --cached set_env.sh`

Afterwards, we can prevent the file from being included in your solution by adding the file to our `.gitignore` file.

### Database
Create a PostgreSQL database either locally or on AWS RDS. Set the config values for environment variables prefixed with `POSTGRES_` in `set_env.sh`.

### S3
Create an AWS S3 bucket. Set the config values for environment variables prefixed with `AWS_` in `set_env.sh`.

### Backend API
* To download all the package dependencies, run the command from the directory `udagram-api/`:
    ```bash
    npm install .
    ```
* To run the application locally, run:
    ```bash
    npm run dev
    ```
* You can visit `http://localhost:8080/api/v0/feed` in your web browser to verify that the application is running. You should see a JSON payload. Feel free to play around with Postman to test the API's.

### Frontend App
* To download all the package dependencies, run the command from the directory `udagram-frontend/`:
    ```bash
    npm install .
    ```
* Install Ionic Framework's Command Line tools for us to build and run the application:
    ```bash
    npm install -g ionic
    ```
* Prepare your application by compiling them into static files.
    ```bash
    ionic build
    ```
* Run the application locally using files created from the `ionic build` command.
    ```bash
    ionic serve
    ```
* You can visit `http://localhost:8100` in your web browser to verify that the application is running. You should see a web interface.

## Tips
1. Take a look at `udagram-api` -- does it look like we can divide it into two modules to be deployed as separate microservices?
2. The `.dockerignore` file is included for your convenience to not copy `node_modules`. Copying this over into a Docker container might cause issues if your local environment is a different operating system than the Docker image (ex. Windows or MacOS vs. Linux).
3. It's useful to "lint" your code so that changes in the codebase adhere to a coding standard. This helps alleviate issues when developers use different styles of coding. `eslint` has been set up for TypeScript in the codebase for you. To lint your code, run the following:
    ```bash
    npx eslint --ext .js,.ts src/
    ```
    To have your code fixed automatically, run
    ```bash
    npx eslint --ext .js,.ts src/ --fix
    ```
4. Over time, our code will become outdated and inevitably run into security vulnerabilities. To address them, you can run:
    ```bash
    npm audit fix
    ```
5. In `set_env.sh`, environment variables are set with `export $VAR=value`. Setting it this way is not permanent; every time you open a new terminal, you will have to run `set_env.sh` to reconfigure your environment variables. To verify if your environment variable is set, you can check the variable with a command like `echo $POSTGRES_USERNAME`.

# Udagram Image Filtering Microservice

Udagram is a simple cloud application developed alongside the Udacity Cloud Engineering Nanodegree. It allows users to register and log into a web client, post photos to the feed, and process photos using an image filtering microservice.
Within this 3rd exercise of the [cloud developer nanodegree](https://www.udacity.com/course/cloud-developer-nanodegree--nd9990)

- an existing app is divided into smaller services (api-feed, api-user, fe-app).
- The project uses continuous integration and continuous delivery using [Travis CI](https://travis-ci.com/).
[Docker](https://www.docker.com/) is used to containerize the project and its deployed to a K8s cluster.
Rolling-updates and rollbacks can be done.

## Getting started

### Prerequisites

### Accounts

[AWS](aws.amazon.com), [DockerHub](https://hub.docker.com/)

### Tools

In order to run the project you need the following apps on your computer:
AWS commandline tool: [AWS CLI](https://aws.amazon.com/cli/)

K8s commandline tool: [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

Kubernetes helper for AWS: [terraform](https://www.terraform.io/), [kubeone](https://github.com/kubermatic/kubeone/blob/master/docs/quickstart-aws.md)

### Bucket on AWS

- Create an [S3 Bucket](https://aws.amazon.com/s3/) with the following permissions:

```
{ "Version": "TODO",
 "Id": TODO"",
 "Statement": [{
    "Sid": "TODO",
    "Effect": "Allow",
    "Principal": {"AWS": "__YOUR_USER_ARN__"},
    "Action": ["s3:GetObject","s3:PutObject"],
    "Resource": "__YOUR_BUCKET_ARN__/*"
    }]
}

```
- Save this CORS configuration on AWS for your bucket:
```
<?xml version="1.0" encoding="UTF-8"?>
 <CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
 <CORSRule>
 <AllowedOrigin>*</AllowedOrigin>
 <AllowedMethod>GET</AllowedMethod>
 <AllowedMethod>POST</AllowedMethod>
 <AllowedMethod>DELETE</AllowedMethod>
 <AllowedMethod>PUT</AllowedMethod>
 <MaxAgeSeconds>3000</MaxAgeSeconds>
 <AllowedHeader>Authorization</AllowedHeader>
 <AllowedHeader>Content-Type</AllowedHeader>
 </CORSRule>
</CORSConfiguration>
```

- Save those variables to your environment:
```
POSTGRESS_USERNAME=udagram
POSTGRESS_PASSWORD=local
POSTGRESS_DB=udagram
POSTGRESS_HOST=db
WT_SECRET=TODO
AWS_BUCKET=__YOUR_AWS_BUCKET_NAME__
AWS_REGION=__YOUR_AWS_BUCKET_REGION__
AWS_PROFILE=__YOUR_AWS_PROFILE__
```

## Deploy locally
1. all deployment files can be found in `/udacity-c3-deployment/docker`
1. Build the images: `docker-compose -f docker-compose-build.yaml build --parallel`
2. Push the images: `docker-compose -f docker-compose-build.yaml push`
3. Run the container: `docker-compose up`. The application will run on `localhost:8100`

## Deploy on AWS

### Prerequiste: Install K8s

### Run K8s cluster with EKS CLI

1. Create the cluster: `eksctl create cluster --name {{name}} --region {{region}}`

### Running CI/CD
- set environment variables for travis in order to push images to docker registry.
- when pushing to branch `master` the app is deployed to the K8s cluster. If needed change the app name in `deploy.sh`