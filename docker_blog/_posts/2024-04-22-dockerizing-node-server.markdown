---
layout: post
title:  "Dockerizing a Node.js Server and Pushing to Docker Hub "
date:   2024-04-22 13:47:45 +0530
categories: jekyll cat2
author: "Haard Shah"
---
## Introduction
In this tutorial, we'll explore how to Dockerize a Node.js server and push it to Docker Hub. Containerization has become a fundamental aspect of modern software development, offering benefits like consistency, scalability, and portability. Docker, a popular containerization platform, simplifies the process of packaging and deploying applications. We'll cover the following steps in this tutorial:

1. Creating a basic Node.js server.
2. Dockerizing the Node.js server using a Dockerfile.
3. Setting up Docker Hub for storing and sharing Docker images.
4. Creating a Docker container from the image and pushing it to Docker Hub.

Let's dive in!

## Prerequisites
Before getting started, ensure you have the following installed:
- Node.js: [Download and install Node.js](https://nodejs.org/) if you haven't already.
- Docker: [Download and install Docker](https://www.docker.com/get-started) for your operating system.


## Creating a Basic Node.js Server
First, let's create a simple Node.js server using Express framework.

1. Create a new directory for your project:
   ```bash
   mkdir IA2
   cd IA2
   ```
2. Initialize a new Node.js project:
  ```bash
  npm init -y
  ```
3. Install Express:
  ```bash
  npm install express
  ```
4. Create a file named `index.js` and add the following code:
  ```javascript
    var express=require('express')
    var app=express();

    app.get('/',(req,res)=>{
        res.send("Hello World")
    })

    app.listen(3000,function(req,res){
        console.log("listening");
    })
  ```
5. Save the file and start the server:
  ```bash
  node index.js
  ```

You should see "Server is running on port 3000" in the console. Visit http://localhost:3000 in your browser to see "Hello, Docker!".

## Dockerizing the Node.js Server
Now, let's Dockerize our Node.js server by creating a Dockerfile.

1. Create a file named `Dockerfile` in the root of your project directory.
2. Add the following content to the Dockerfile:
  ```bash
  ARG NODE_VERSION=16.18.0
      FROM node:${NODE_VERSION}-alpine
      ENV NODE_ENV production
      WORKDIR /usr/src/app
      RUN --mount=type=bind,source=package.json,target=package.json \
          --mount=type=bind,source=package-lock.json,target=package-lock.json \
          --mount=type=cache,target=/root/.npm \
          npm ci --omit=dev
      USER node
      COPY . .
      EXPOSE 3000
      CMD npm start
  ```
    

## Setting Up Docker Hub
Docker Hub is a cloud-based repository for storing and sharing Docker images.

1. Create a Docker Hub account at hub.docker.com.
2. Once logged in, navigate to the "Repositories" tab and click "Create Repository".
3. Choose a name for your repository and click "Create".


## Creating a Container and Pushing to Docker Hub
Now, let's build a Docker image from our Dockerfile, create a container, and push the image to Docker Hub.

1. Build the Docker image:
  ```bash
  docker build -t your_username/image-name .
  ```
  here I have used docker build -t haardshah04/IA2 .

2. Run the Docker container locally:
  ```bash
  docker run -p 3000:3000 your_username/node-server
  ```
  Visit http://localhost:3000 in your browser to verify that the server is running.
3. Log in to Docker Hub from the terminal:
  ```bash
  docker login
  ```
4. Push the Docker image to Docker Hub:
  ```bash
  docker push your_username/image-name:tag
  ```
  here i entered docker push haardshah04/IA2:1.0.0
5. Once the image is pushed successfully, you can view it on Docker Hub's website under your repository.

## Conclusion

In this tutorial, we learned how to Dockerize a Node.js server and push it to Docker Hub. Containerization provides a consistent environment for running applications across different platforms, and Docker Hub serves as a central repository for sharing Docker images. By following these steps, you can easily package and distribute your Node.js applications using Docker.

Happy containerizing!

