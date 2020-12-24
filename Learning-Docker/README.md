# Learning Docker (2015)
__By Pethuru Raj, et. al.__   

[Sample Code, 2nd Edition](https://github.com/PacktPublishing/Learning-Docker-Second-Edition)    

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
`busybox` is an image representing a tiny version of Linux.   
See all downloaded images using the `docker images` subcommand  
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
AWS provide at least two option for running docker contianers:  
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
