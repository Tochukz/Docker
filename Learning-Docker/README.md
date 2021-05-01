# Learning Docker (2015)
__By Pethuru Raj, et. al.__   

[Sample Code, for 2nd Edition](https://github.com/PacktPublishing/Learning-Docker-Second-Edition)    

__Preface__  
A container generally contains an application and all of the application's libraries, binaries, and other dependencies are stuffed together to be presented as a comprehensive, yet compact entity for the outsisde world.  

It is recommended that every contianer hosts one application service.

## Chapter 1: Getting Started with Docker  
Docker is a containerizationn engine, which automates the packaging, shipping, and deployment of any software applications that are presented as lightweight, protable, and self-sufficent containers, that will run virtually anywhere.
A Docket comainter is completelty sisolated from other containers as well as the host machine. It also has its own process space and network interface.  

__Virtualization versus Containerization__  

| Virtual Machined(VMs)                    | Containers                                    |
|------------------------------------------|-----------------------------------------------|
| Represents hardware-level virtualization | Represents OS virtualization                  |
| Heavy wieght                             | Leigth weight                                 |
| Slow provisioning                        | Real-time provisioning and scalability        |
| Resource hungry, Limited perforance      | Native/bare metal performance                 |
| Fully isolated and hance more secured    | Process-level isolation and hence less secure |
| SSH and TTY support                      | No SSH, TTY Support                           |



A hybrid model(system container), having features from both the virual machine and that of the containers is being developed.

__Installing the Docker engine__   
You can install the docker engine using one of 2 methods:   
__Method 1:__  Install docker from the Ubuntu package repository (may not be the latest version)  
First update the package repository
```
$ sudo apt-get update
```
Install docker.io
```
$ sudo apt-get install -y docker.io
```
Create a softlink from docker to docker.io
```
$ sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker
```
This will allow you to use the `docker` command instead of `docker.io`  
You may also use the _snap_ package manager to install docker instead of _apt-get_  
```
$ sudo snap install docker
```

__Method 2:__. Install the latest version of docker using docker.io script
You can either use _curl_  
```
$ sudo curl -sSL https://get.docker.io/ | sh
```
Or you can use _wget_   
```
$ sudo wget -qO- https://get.docker.io/ | sh  
```

__Understanding the Docker setup__  
To view details of your installed docker engine
```
$ sudo docker version
```  
To view the activity of the docker engine including containers
```
$ sudo docker info
```  

__Client server communication__  
Docker is usuallly programmed for carring out server-client communication by using the Unix socket(`/var/run/docker.sock`). Docker also has an IANA registered port, which is 2375. However, for security reason, this port is not enabled by default.  

__Downloading a Docker Image__  
To download a docker image use the `docker pull` subcommand  
```
$ sudo docker pull busybox
```
This will download the latest version of the
`busybox` image which represents a tiny version of Linux.  
To download all the version of the image, use the `-a` flag
```
$ sudo docker pull -a busybox
```   
To download a specific image version using the images tags
```
$ sudo docker pull busybox:unbuntu-14.04
```
Here `busybox` is the repository and `unbuntu-14.04` is the image tag.  
To see all downloaded images using the `docker images` subcommand  
```
$ sudo docker images  
```  
__Creating a Docker container__  
Use the `docker run` subcommand to create a docker container  from an image
```
$ sudo docker run busybox
```
`busybox` is the image name.  

__Running a Docker container on AWS__   
AWS provide at least two option for running docker containers:  
* The Amazon EC2 container service  
* Docker deployment by using the Amazon Elastic Beans service.  

You can access this services and more using the AWS CLI tool.

__Troubleshooting__  
To check the status of docker  
```
$ sudo service docker status
```  
If you installed docker from Ubuntu package repository, use `docker.io` instead of `docker`.

## Chapter 2: Handling Docker Containers  
__Docker images and containers__  
A _Docker image_ is a collection of all the files that make up a software application.  An image may originate from a base image. A base image may represent an operating system for example a Linux distribution like Debian. Each time an image is modified, for example, a module is added, a new image is formed with a new name. This is because a Docker images are typically of the read-only type.
Docker images are the building blocks of docker containers.   

__Docker Registry__  
A Docker image is stored within a repository in the Docker Registry. Each repository is unique for each user account.  

__Docker Repository__  
A Docker Repository is a namespace that is used for storing a Docker image.

__Working with Docker images__  
An specific version of an image can be identified by appending it repository name and to it's tags name with an colon like this `<repository>:<tag>`. For example to launch a container using the image with tag `unbuntu-14.04` and repository `busybox`, do
```
$ sudo docker run -t -i busybox:unbuntu-14.04  

```
The `-t` and `-i` flag makes the container interactive.
__Ducker Hub Registry__    
To download an image from a third party user on Dockerhub registry do:
```
$ sudo docker pull thedockerbook/helloword
```
Here, _thedockerbook_ is the third party user ID  and _helloword_ is the image repository name.   

To download an image from a third party repository, specify the repository url:  
```
$ sudo docker pull registry.example.com/myapp
```

To remove a docker image:
```
sudo docker rmi tagnameOrID
```
To force remove all images:
```
sudo docker rmi -f $(sudo docker images -a -q)
```

__Searching Docker images__  
You can search for DOcker images in the Docker Hub Registry by using the `docker search` subcommand:  
```
$ sudo docker search mysql
```

__Working with an interactive container__   
You launch a container using the `-i` and `-t` flag, to make interactive. The `-i` flag grabs the standard input while the `-t` flag allocated a pseudo terminal emulator to the container.  
First lest pull the 14.04 tag image of ubuntu:
```
$ sudo docker pull ubuntu:14.04
```
Next we launch an interactive container using the image and the `/bin/bash` command:
```
sudo docker run -i -t ubuntu:14.04 /bin/bash
```    
Note that we can launch a container without having the image and the docker host will download the image automatically.  Also you can use the `--name` flag to specify a name for your container.  

After launching the container it will land use in a bash prompt inside the container. We can detach from the interactive container while it keeps running by press `Ctrl+P` and `Ctrl+Q` escape sequence. This will take use back to the docker host prompt. To see of list of all the running container, use the `docker ps` subcommand:   
```
$ sudo docker ps
```  
To get back into the container's interactive shell use the `docker attach` subcommand  
```
sudo docker attach container_id_or_name
```   
__Tracking changes inside containers__   
The `docker diff` subcommand can be used to track changes made to our container.    
For example, assuming some new file was created inside the container we will see a list of the files by doing
```
$ sudo docker diff container_id
```  
When looking at the list of file you will find the modified files will be denoted by the letter `D`, newly added file will be denoted by `A` and deleted file denoted by `D`.  

__Controlling Docker containers__  
To stop a container   
```
$ sudo docker stop container_id
```  
The `docker ps` command list only containers that are running. To list all containers irrespective of their status, use the `-a` flag.  
```
$ sudo docker ps -a
```  
Use the `docker start` subcommand to start a stopped container  
```
$ sudo docker start container_id
```  
If you want to have the container attached to the terminal when you start it, use the `-a` flag  
```
$ sudo docker start container_id -a
```
Use the `docker restart` subcommand to stop and start a container again  
```
$ sudo docker restart container_id
```  
Use the `docker pause` and `docker unpause` subcommand to freeze and unfreeze respectively, the execution of all the processes with the container.  
We can see the in action by running an infinite loop inside of a container for example  
```
root@403a4e3df259:/# while true; do date; sleep 5; done
```  
This will print the date every 5 second.   
In another terminal, we can pause the execution  
```
$ sudo docker pause 403a4e3df259  
```  
And to resume the execution we can unpause if  
```
$ sudo docker unpause 403a4e3df259
```  
Also if we look at the list of container using `docker ps` it will indicate when a container is paused under the `status` column .  

__Housekeeping containers__  
Unused container takes up the disk space in the filesystem of the Docker host.  
We can use the `--rm` option to tell the Docker engine to cleanup the container as soon as it reaches the stopped state.  
```
$ sudo docker rum -i -t --rm ubuntu:14.04 /bin/bash  
```
Alternatively, you can remove the `docker rm` subcommand to manually remove  a stopped container  
```
$ sudo docker rm container_id
```  
Note that in most the command where `container id` is used, `container name` can be used instead.  
You can combine the `docker rm` and the `docker ps` subcommands to automatically delete all the containers that are not currently running.  
```
$ sudo docker rm $(sudo docker ps -aq --no-trunc -f "status=exited")
```
__Building images from containers__   
Launch the container using the interactive mode  
```
$ sudo docker run -i -t ubuntu:14.04 /bin/bash
```  
Lets install `wget` in the container as an example.   
First we use the `which` command to check if `wget` is already installed
```
root@4273fc6feeed:/# which wget
```  
Now we install `wget`  
```
root@4273fc6feeed:/# apt-get update
root@4273fc6feeed:/# apt-get install -y wget
```   
You may run `which wget` again to confirm the successful installation of `wget`.  
Use `docker diff` on a separate terminal to see the changes made to the container
```
$ sudo docker diff 4273fc6feeed
```
Finally, use the `docker commit` subcommand to commit the container to an image.   
You may want to stop the container before you commit  it.
```
$ sudo docker stop 4273fc6feeed
$ sudo docker commit 4273fc6feeed chucks_repo/ubuntu.wget
```
Here I have chosen to name my new image  `chucks_repo/ubuntu.wget`.   
The `docker images` subcommand can be used to see the newly created image in the list of images.  

Images may be created from container for testing purposes but the recommended way to create images is to use the `Dockerfile` method.  

__Launching a container as a daemon__   
Use the `-d` flag to launch a container in the detached mode or as a daemon.  
```
$ sudo docker run -d ubuntu:14.04 /bin/bash
```  
Let launch a container in the detached mode and also run an infinite loop inside the container at the same time
```
$ sudo docker run -d ubuntu:14.04 /bin/bash -c "while true; do date; sleep 5; done"
```  
Now we can use the  `docker logs` subcommand to view the output generated by the container daemon
```
$ sudo docker logs 5d3b2de2c03b
```  

## Chapter 3: Building Images  
__Docker's integrated image building system__  
We can craft an image manually by committing the container as an image after modifying it.  A better alternative will be to automatically craft the image using `Dockerfle`.   
After you have made a `Dockerfile`, you use the  `docker build` subcommand to build the image.  
```
$ sudo docker build -t image_name:tag_name path_to_docker_file
```  
Assuming the docker file is in the current directory you can use a period as as the path parameter
```
$ sudo docker build -t mybuntu .
```  
You can rename the repo and tag of an existing image by using the `docker tag` subcommand and the image id.  
```
$ sudo docker tag 295418dc502 mybuntu:10.01
```
