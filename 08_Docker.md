# Beginning Guide for Docker
This training is intended to help you as a trainee to go through some of the basic concept of Docker - an important tool we are going to use a lot when you become a cluster manager. This training does not intended to be exhausted, instead, we want you through this training have some basic knowledge about how the work are containerizing application right now.
Although this training is long, feel free to ask questions to any of cluster managers

## 1. Introduction to Docker
### a. What is Docker?
Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers
### b. What is a container?
A container is a “bag” of code, configuration, processes, networking, dependencies, and operating systems that is enough to execute an application
## 2. Installation
* Remove old version of docker engine that was installed as default with Ubuntu 20.04 - LTS and 18.04 - LTS.

`$ apt-get remove docker docker-engine docker.io containerd runc`
* Updating apt-get to be ready for installation. 

`$ apt-get update`
* Checking the following package apt-transport-https ca-certificates  curl  gnupg  lsb-release using the following command (replace <Package> with the name of the package):

`$ apt-cache show <Package>`
* Add docker keys for authentication and trust

`$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`
* Setting up a stable repository. The idea is to give the machine the latest release of general availability

`$ echo  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`
* Finally install the latest version of docker-ce

`$ apt-get update`

`$ apt-get install docker-ce docker-ce-cli containerd.io`
* Running the hello-world container (build-in container to test installation)

`$ docker run hello-world`
If the result includes a string “Hello from Docker!”, it means you have already successfully downloaded docker.

## 3. Docker workflow explanation
Typically, a life cycle of a container start when you initialize it using the command `docker run`. After everything get setting up, the container status changing to the running status. Then the container will try to finish the task that was specified in either the image or the command line. When finishing executing the computation or request, the container then will be terminated and going to stopped container. In this status, you cannot access the container anymore. You either need to regenerate another container with the same configuration or commit the current status of the container. By committing the container, we create an image that catch the latest status of our desired container. This is the end of the life cycle of a container.
### a. Starting container
To initialize a container, we will use `$ docker run` command. The information below will help you have a glance at this command.

docker run [OPTION1] [CONTAINER_IMAGE_NAME:VERSION] [OPTION2]

The following command is going to execute Container image with the version specified using the option provided in OPTION2 with special flags being called by OPTION1

Some of the common and important OPTION1:

-ti : terminal interactive

--rm: remove the container after exit application

-name: for naming the container. If you do not specify a name, docker will give it a random name

-d : detached, leave the container running in the background

-p: public port in and out. For instance -p 34567:34567 means we publish our port 34567 to connect to port 34567

For more information, enter the following command:

		docker run --help
### b. Running container
The container will try to execute the command that was specified in the either the container image or on the command line. This call the running container. However, usually it only took less than 10 seconds for every containers to finish executing and terminating. You can type `$ docker ps` for more information about the status of the container.
However, if we want to keep a container "alive" (running). We need to use detach and attach mode of a container. Let take a look at the below step.
If you enter, for instance:

		docker run -d -ti ubuntu:latest

And then, type:

		docker ps -l	              (ps stands for processing status, -l means you want to see the last container that was run )

If you look at the STATUS column, you will see something like this “UP 3 seconds” which means that the container is still running. To test this hypothesis, you can run the same docker ps command above again, the time that it ran will change again.

To access (or attach) the running container, enter the following command: 

		docker attach NAME_OF_CONTAINER/ID
		
Press CTRL + D to exit.
### c. Stopped container
After finish executing the request, the container will go to stopped status. At this point, you cannot access the container anymore. 
### d. Commited Container (images/ status)
In case, you want to save the progress you have made with the container, we can create an image based on a stopped container (commiting the image to the registry).

Whenever you modify something inside the image after the docker run command and exit, the change will still be there, however if you run it again, you will not see that change anymore since the previous change was put inside a stopped image. To make those changes visible and editable. We need to commit the change into a new container image by running the following command.

		docker commit NAME_OF_CONTAINER/ID NEW_CONTAINER_IMAGE_NAME

This will allow you to use docker run on the new image and see your changes.

## 4. Dockerfile basic concept
In this section, we will show you some fundamentals knowledge about Dockerfile. Dockerfile is a way to create an images from scratch or from a base images like ubuntu, alpine, etc. Follow the steps below to have a look at a Dockerfile
Create a file call Dockerfile (word-by-word) inside a terminal

Then start editing it. These are the 5 most common command syntax:

Always start the Dockerfile with 

	FROM <BASED_IMAGE>:<VERSION>

Replace the <BASED_IMAGE> and <VERSION> with your own based image. For example: FROM ubuntu:latest  means that I want to start from ubuntu image with the latest version

	RUN <COMMAND> : execute the command COMMAND and save that to an intermediate container image then using that intermediate image to start the next steps of building our container image. Finally remove the intermediate image.

	COPY <CONTAINER_DESIRED_DIR> <HOST_DIR> to copy a directory from host to container image

	WORKDIR <DIRECTORY> to change the directory that will be used for the rest of the building process

	CMD (only 1 per Dockerfile).

Syntax: CMD “COMMAND1” “COMMAND2” …

Or CMD [OPTION]


## 5. Example



[**PREVIOUS: Important Terms**](00_terms.md). 

[**NEXT: Configuring the Head Node**](02_configuring-the-headnode.md)
