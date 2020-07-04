---
title: Docker Up!
date: 2017-04-27 21:36:06
tags:
  - docker
  - spring
  - spring boot
---
Ship your Spring boot app in a docker container
<!--more-->

As the **Docker** [website](https://www.docker.com/) says you can build and run your application anywhere. One of the best containers - Docker is versatile, easy to use and configure.

This post will manly concentrate on running a sample Java based web applicaiton inside a docker container. However, installation is not covered in detail in this post.

In order to run any application on a docker container there are 3 major steps:
    1. Write a Dockerfile.
    2. Build the Docker image.
    3. Run the image.

We will look through each of these steps.

### 1. Writing a Dockerfile

This is the first and the most important step in setting up a container. The dockerfile contains everything that your app needs. It is analogous to a script that will achieve certain tasks. For our example below is the dockerfile.

    $ cd your-app
    $ create a file named Dockerfile

Inside the file paste the following lines.

    From tomcat:8-jre8
    ADD target/app.war /usr/local/tomcat/webapps/

This file is the simplest of dockerfiles one can imagine. It essentially does two things - Pull up an already existing image of tomcat8 from the Docker Hub and packaging your war file inside docker's tomcat. Now let us segue into the next section.

### 2. Build the docker image

Just jump into the directory where the dockerfile sits and type the following command

    $ sudo docker build -t image_name:tag_name

The -t flag is to attach a tag with this image. It can be the verison of the image.

This was pretty simple. Lets move on to the final step.

### 3. Run the image

    $ sudo docker run -d -p 8080:8080 --name your_app name_of_the_image:tag

Now lets break down this cryptic command.

The run command is the primary task that we are executing ie, running the image. -d means detached ie, it runs the container in the background. -p stands for port and it binds your computer port 8080 to docker's 8080 port. --name tag is self explanatory and the tag is the name of the tag that we used while building our image.

#### Few handy docker commands

| Command                        | Function                                                                                              |
|--------------------------------|-------------------------------------------------------------------------------------------------------|
| sudo docker images             | lists all the images                                                                                  |
| sudo docker ps                 | Lists all the running containers                                                                      |
| sudo docker ps -a              | Lists all the com=ntainers                                                                            |
| sudo docker rm container_id    | Deletes the stopped container                                                                         |
| sudo docker rmi image_id       | To delete an image (The container referring to this image must be deleted before deleting this image) |
| sudo docker start container_id | Start an already existing container                                                                   |
| sudo docker stop container_id  | Stop an already running container                                                                     |
