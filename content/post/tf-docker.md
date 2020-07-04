---
title: Tensorflow inside Docker
date: 2017-05-13 21:01:12
tags:
  - tensorflow
  - docker
---
Learn the easiest way to setup tensorflow
<!--more-->

Getting started with TensorFlow may sometimes give way to some errors and if you are not in the mood of debugging or want to get your hands on this library as fast as possible then I suggest that you run this inside a docker container.

Make sure that you have already installed docker. If you haven't checkout the docs. Installing docker on Windows is just as simple as installing any other program. Just download the Docker Toolbox, install it and run the start script inside the installation directory. Docker will take its time, fire up a Linux VM and there you go, thats it.

After this pull the [latest](https://hub.docker.com/r/tensorflow/tensorflow/) image from docker hub.

    $ docker run -it -p 8888:8888 tensorflow/tensorflow

The above command runs it in foreground and binds the container's 8888 port to your machine's 8888 port. Hit localhost:8888 in from your browser and you will see a Jupyter Notebook waiting for you. Copy and paste the token from your terminal.

Note: If you are running Windows then instead if localhost you need to type the VM's IP in your browser. This will be displayed when you run the start script default is 192.168.99.100.

If you wish to run your container in background then use the -d flag in the above command but you will not be able to see the token so for that you can check the logs of your container

    $ docker logs container_id --tail 10

This will display the last ten lines of your container log.

After doing all the above steps if you see this below then **Congrats!!**    you have taken your first big step into working with TensorFlow.

![](tf_docker_1.jpg)

The three notebooks that are present that are already present provide you a decent introduction and also a hands on experience with a simple neural network and Image recognition.

### Further reading

If you are probably wondering, how will I persist my changes then docker has got just the right solution for you. You can commit and push yourchanges just the way you do in Git. Read along to see how to.

* Create an account in [Docker Hub](https://hub.docker.com/)
* Create a new repository.
* Now from your terminal stop the container after you are done working.
* Commit the changes using docker commit

        $ docker commit -m " Optional commit message" container_id repo_name:tag

* The above command will save the instance of your container and construct a new image with the tag you specify above.

* Login inside your docker hub account and push the image.

        $ docker login
        $ docker push repo_name


The first push will take some considerable time so wait for a few minutes. Docker will compress the image and push it to its central repo.

You can now log into any machine with docker and pull this image. Your newly created files will be sitting right there.

**PS :** While this article will give you a great start with this amazing library it might not help very much when you have to scale it to train complex models with a huge dataset.
