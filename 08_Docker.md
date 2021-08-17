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
### a. Starting container
### b. Running container
### c. Stopped container
### d. Commited Container (images/ status)

## 4. Dockerfile basic concept

## 5. Example
