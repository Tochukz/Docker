# Docker Docs
[Guides](https://docs.docker.com/guides/)  
[Dockerfile Reference](https://docs.docker.com/reference/dockerfile/)  
[Docker CLI Reference](https://docs.docker.com/reference/cli/docker/)  
[Build with Docker Guide](https://docs.docker.com/build/guide/)  
[Docker Hub](https://docs.docker.com/docker-hub/)  
[Multi Platform Images](https://docs.docker.com/build/building/multi-platform/)  
[Docker Volumnes](https://docs.docker.com/storage/volumes/)  

### Docker architecture
Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of _building_, _running_, and _distributing_ your Docker containers. The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon.  
The Docker client and daemon communicate using a _REST API_, over _UNIX sockets_ or a _network interface_.  
Another Docker client is _Docker Compose_, that lets you work with applications consisting of a set of containers.

__The Docker daemon__  
The Docker daemon _(dockerd)_ listens for Docker API requests and manages Docker objects such as _images_, _containers_, _networks_, and _volumes_. A daemon can also communicate with other daemons to manage Docker services.

__The Docker client__   
The Docker client _(docker)_ is the primary way that many Docker users interact with Docker. When you use commands such as `docker run`, the client sends these commands to _dockerd_, which carries them out.  
The Docker client can communicate with more than one daemon.

__Docker Desktop__  
Docker Desktop is an application that enables you to build and share containerized applications and microservices. Docker Desktop includes
* the Docker daemon (dockerd),
* the Docker client (docker),
* Docker Compose,
* Docker Content Trust,
* Kubernetes, and
* Credential Helper.

__Docker registries__  
A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use, and Docker looks for images on Docker Hub by default.  
When you use the `docker pull` or `docker run` commands, Docker pulls the required images from your configured registry.   
When you use the `docker push` command, Docker pushes your image to your configured registry.  

__Docker objects__  
When you use Docker, you are creating and using images, containers, networks, volumes, plugins, and other objects.  
__Images__: An image is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization.  
__Containers__: A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI.  
You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

## Chapter 1: Getting Started
__Building an image__  
```bash
$ cd getting-started
$ docker build -t getting-started .
```
The docker build command uses the Dockerfile to build a new image. The image will be named _getting-started_ according to the tag specified.

__Start an app container__  
```bash
$ docker run -dp 127.0.0.1:3000:3000 getting-started
```  
The `-d` flag (`--detached`) runs the container in the background.   
The `-p` flag (`--publish`) creates a port mapping between the host and the container. Without the port mapping, you wouldn't be able to access the application from the host.  
After a few seconds, open your web browser to http://localhost:3000 to see the app

To see your containers, you can use the  Docker Desktop's GUI or Docker CLI
```bash
$ docker ps
```

__Update the application__   
After you must have updated your code, you can rebuild the image
```bash
$ cd getting-started
$ docker build -t getting-started .
```  
Remote the existing container
```bash
# Get the container id
$ docker ps
$ docker stop <container-id>
$ dokcer rm <container-id>
```
Start a new container
```bash
$ docker run -dp 127.0.0.1:3000:3000 getting-started
```  

__Sharing the application__  
Now that you've built an image, you can share it. To share Docker images, you have to use a Docker registry. The default registry is [Docker Hub](https://hub.docker.com/).     

First you need to create a repository
1. Login to [Docker Hub](https://hub.docker.com)
2. Click on _Create a Repository_
3. Enter a name for the repositotry - _getting-started_
4. Make sure the _visibility_ is set to _public_.
5. Click Create

Now you can push your docker image to the repository
1. Login to Docker Hub
```bash
$ docker login
# When prompted for your username, supply your docker id
# If you were already logged in to a different account, use the docker logout command
```
2. Use the docker tag command to give the getting-started image a new name.
```bash
$ docker tag getting-started <docker-id>/getting-started
# Your docker id can be found on the repository page on Docker hub
```
3. Push the image
```bash
$ docker push <docker-id>/getting-started
```
Because we did not specify a tag, Docker will use a tag called latest.  

__Container volumes__  
Volumes provide the ability to connect specific filesystem paths of the container back to the host machine.  
If you mount a directory in the container, changes in that directory are also seen on the host machine.  
In our case we will use a directory on the host to persist the SQLite database file from the container using _volume mount_.  
1. Create a volume
```bash
$  docker volume create todo-db
```
2. Stop and remove the existing container
```bash
$ docker stop <container-id>
$ docker rm <container-id>
```
3. Start the todo app container, but add the `--mount` option to specify a volume mount.
```bash
$ docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```
If you are using GitBash, the target should be prefixed with `/`
```bash
$ docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=//etc/todos getting-started
```

__Volume types__  
There are two types of volumes
1. __Volume mount__: A volume mount is a great choice when you need somewhere persistent to store your application data.
2. __Bind mount__: A bind mount is another type of mount, which lets you share a directory from the host's filesystem into the container.  

In addition to _volume mounts_ and _bind mounts_, Docker also supports other mount types and storage drivers for handling more complex and specialized use cases.

__Verify that the data persists__
To verify the that the data persist.
1. Launch the app and create some todo items.
2. Stop the container and remove it.
3. Start a new container and launch the app again.
4. Check to see if your previously create todo items is displayed on the app.

__Dive into the volume__  
To find out where is Docker stores the data for the mounted volume
```bash
$ docker volume inspect todo-db
# Check the path specified by the Mountpoint property
```

__Bind Mount__  
When working on an application, you can use a bind mount to mount source code into the container. The container sees the changes you make to the code immediately, as soon as you save a file.

__Run your app in a development container__  
1. Stop and remove any existing container
```bash
# Get the containter id
$ docker ps
$ docker stop <container-id>
$ docker rm <container-id>
```
2. Start a new container
```bash
$ cd getting-started
$ docker run -dp 127.0.0.1:3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev"
```
* If you are using Git bash, replace `$(pwd)` with `/$(pwd)`
* `-w /app` - sets the "working directory" or the current directory that the command will run from
* `--mount type=bind,src="$(pwd)",target=/app` - bind mount the current directory from the host into the /app directory in the container
* `node:18-alpine` - the image used.
3. To watch the logs on the container
```bash
$ docker logs -f <container-id>
```

__Multi Container app__  


## Docker Compose
[Getting Started](https://docs.docker.com/compose/gettingstarted/)


Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them    
