---
layout: post
title:  "Dockerizing a Node.js Server and Pushing to Docker Hub "
date:   2024-04-22 13:47:45 +0530
categories: jekyll cat2
author: "Haard Shah"
---
## Introduction
In this tutorial, we'll explore how to Dockerize a MongoDB, Node.js server, and a React app, and push them to Docker Hub. Containerization has become a fundamental aspect of modern software development, offering benefits like consistency, scalability, and portability. Docker, a popular containerization platform, simplifies the process of packaging and deploying applications. We'll cover the following steps in this tutorial:

1. Creating a MongoDB instance.
2. Creating a basic Node.js server.
3. Creating a React app.
4. Dockerizing each component using Dockerfiles.
5. Setting up Docker Hub for storing and sharing Docker images.
6. Creating Docker containers from the images and pushing them to Docker Hub.

Let's dive in!

## Prerequisites
Before getting started, ensure you have the following installed:
- Node.js: [Download and install Node.js](https://nodejs.org/) if you haven't already.
- Docker: [Download and install Docker](https://www.docker.com/get-started) for your operating system.

## Creating a MongoDB Instance
First, let's create a MongoDB instance using Docker.
1. Pull the MongoDB image from Docker Hub:
```bash
  docker pull mongo:latest
```
2. Run the MongoDB container:
```bash
  docker run -d -p 32000:27017 --name ia2mongo  -e MONGO_INITDB_ROOT_USERNAME=admin  -e MONGO_INITDB_ROOT_PASSWORD=password   mongo:latest --auth 

```
here we have used 32000 port to avoid any conflicts

This will create a MongoDB container named `ia2mongo` running on port `32000`.\


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
    const express = require('express');
  const { MongoClient } = require('mongodb');

  const uri = 'mongodb://admin:password@localhost:32000';
  const dbName = 'mydatabase';
  const app = express();

  const client = new MongoClient(uri, { useNewUrlParser: true, useUnifiedTopology: true });

  client.connect()
    .then(() => {
      console.log("Connected successfully to Mongo");
      app.get('/timestamp', async (req, res) => {
        try {
          const timestamp = new Date().toISOString();
          res.json({ timestamp });
        } catch (error) {
          console.error('Error generating timestamp:', error);
          res.status(500).json({ error: 'Internal server error' });
        }
      });

      const port = 3000;
      app.listen(port, () => {
        console.log(`Server running on port ${port}`);
      });
    })
    .catch(error => {
      console.error('Error connecting to MongoDB server:', error);
      client.close();
    });

```
5. Save the file and start the server:
  ```bash
  node index.js
  ```

You should see "Server is running on port 3000" in the console. Visit http://localhost:3000 in your browser.

## Creating a React App
Now, let's create a React app using Create React App.

1. Install Create React App globally (if not already installed):
```bash
  npm install -g create-react-app
```
2. Create a new React app:
```bash
  npx create-react-app ia2-frontend
```
3. Navigate into your React app directory:
```bash
  cd ia2-frontend
```
4. Build the production version of your React app:
```bash
  npm run build
```
## Dockerizing Each Component
Next, let's Dockerize each component using Dockerfiles.

### MongoDB Dockerfile
Create a file named `Dockerfile.mongodb` in the root of your project directory with the following content:
```bash
  FROM mongo:latest

```
### Node.js Dockerfile

Create a file named Dockerfile.node in the root of your project directory with the following content:


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
### React Dockerfile
Create a file named Dockerfile.react in the root of your React app directory with the following content:

```bash
  FROM nginx:alpine

  COPY build/ /usr/share/nginx/html

  EXPOSE 80

  CMD ["nginx", "-g", "daemon off;"]

```

## Setting Up Docker Hub
Docker Hub is a cloud-based repository for storing and sharing Docker images.

1. Create a Docker Hub account at hub.docker.com.
2. Once logged in, navigate to the "Repositories" tab and click "Create Repository".
3. Choose a name for your repository and click "Create".


## CCreating Docker Containers and Pushing to Docker Hub
Now, let's build Docker images from our Dockerfiles, create containers, and push the images to Docker Hub.

1. Build the MongoDB Docker image:
  ```bash
    docker build -t your_username/mongodb -f Dockerfile.mongodb .
  ```

2. Build the Node.js Docker image:
  ```bash
  docker build -t your_username/node-server -f Dockerfile.node .
  ```
3. Build the React Docker image:
  ```bash
  docker build -t your_username/react-app -f Dockerfile.react .
  ```
4. Log in to Docker Hub from the terminal:
  ```bash
  docker login
  ```
  
5. Push the Docker images to Docker Hub:
```bash
  docker push your_username/mongodb
  docker push your_username/node-server
  docker push your_username/react-app

```
Once the images are pushed successfully, you can view them on Docker Hub's website under your repository.
## Conclusion

In this tutorial, we learned how to Dockerize a MongoDB instance, Node.js server, and React app, and push them to Docker Hub. Containerization provides a consistent environment for running applications across different platforms, and Docker Hub serves as a central repository for sharing Docker images. By following these steps, you can easily package and distribute your MongoDB, Node.js, and React applications using Docker.

Happy containerizing!

