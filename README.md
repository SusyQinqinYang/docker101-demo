# Docker 101 - Demo

A simple educational tutorial to demonstrate the building-blocks Docker. This repo includes a "hello world" node app that you should clone down (you don't need node.js on your local machine if you don't want to try running the app locally). You'll go through the steps of building a Docker image for this app, pushing this image to Docker Hub, pulling the image down from Docker Hub, and finally running the container to run the app. 

## Demo steps:

##### 0. Install Docker. Start Docker. Create a Docker Hub account. Login on the Docker client running on your local machine. Clone this repo. Navigate to the root directory of this app on your local machine. Take a quick look at the Dockerfile and .dockerignore file. 

A `Dockerfile` is just instructions for Docker on how to spin up your application inside a Docker container. If you look closely below, at some point Docker will run `npm install` and `npm start`. These are commands you would normally run manually if you wanted to run this Node app locally. `.dockerignore` is like `.gitignore`, which tells Docker what directories to not include in the image. Refer to the [Docker official docs](https://docs.docker.com/get-started/) for way more information. 

##### Dockerfile

```
FROM node:8

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm install --only=production

# Bundle app source
COPY . .
EXPOSE 3001

CMD [ "npm", "start" ]
```

##### .dockerignore

```
node_modules
Npm-debug.log
```

##### 1. Build a Docker image
We are going to first build a Docker image for this app. A Docker image is sort of like a "class", and a Docker container is sort of like an instance of that class. Make sure you are CD'ed into the root directory of the app and run: 
```
docker build -t [your Docker Hub username]/dockerdemo .
```

-t sets the image tag. Remember to precede the tag with your Docker Hub username. 
. this period is not a typo! It tells docker to include everything in this directory in the image. 

##### 2. Run the Docker image
Once you have a Docker image built, you can run the Docker container, (though really, we run the "image"). 
```
docker run -p 80:3030 -d kapolyak/dockerdemo
```

-p sets the relationship between a port on the host machine (where docker is installed) and a port on the docker container. 
The port mapping here is important to understand. Docker is a runtime that is running on a host machine - its basically another host running in your computer. 
The number to the left of the colon is the port you want Docker to listen to. The number to the right is the port in Docker that your app is listening to. 
-d runs the image in detached head mode, so it will continue running when you exit the terminal window.

##### 3. Push the Docker image to Docker Hub.
We are pushing the image to dockerhub, not the container. However, we must build a container before pushing the image.
```
docker push kapolyak/dockerdemo
```

##### 4. Pull the Docker image to the host machine.
```
docker pull kapolyak/dockerdemo
```

##### 5. View all Docker images 
```
docker images
```

##### 6. Run a Docker image (create a container)
```
docker run -p 80:3030 -d kapolyak/dockerdemo
```

##### 7. Show running Docker containers
```
docker ps
```

##### 8. Show logs generated by code running in a container
```
docker logs [container id]
```

Pro-tip: you only need to type the first three characters of a container ID to reference that container. 

## Other important things:

Below are examples of important files to include within the root directory of the repo you plan to Dockerize:


### Important Docker commands:

```
Docker system prune --all
```

Docker is non-destructive and won’t delete any containers you have previously built. This can quickly hog your machine’s storage as these containers tend to be large. There are a few different ways to clean the system, but this is the most useful one during development. Here is Docker’s own warning regarding this command:

	WARNING! This will remove:
		- all stopped containers
		- all networks not used by at least one container
		- all dangling images
		- all build cache
