# Docker Docs
[Guides](https://docs.docker.com/guides/)  
[Dockerfile Reference](https://docs.docker.com/reference/dockerfile/)  
[Docker CLI Reference](https://docs.docker.com/reference/cli/docker/)  
[Build with Docker Guide](https://docs.docker.com/build/guide/)  
[Docker Hub](https://docs.docker.com/docker-hub/)  
[Multi Platform Images](https://docs.docker.com/build/building/multi-platform/)  
[Docker Volumnes](https://docs.docker.com/storage/volumes/)  
[Docker Networking](https://docs.docker.com/network/)  
[Docker Compose](https://docs.docker.com/compose/)  
[Compose File Reference](https://docs.docker.com/compose/compose-file/)  
[Compose CLI Referenece](https://docs.docker.com/compose/reference/)   
[Language Specific Guide](https://docs.docker.com/language/)  

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
$ cd getting-started-app
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
$ cd getting-started-app
$ docker build -t getting-started .
```  
Remove the existing container
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

First, you need to create a repository
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
# If you were already logged in to a different account, use the logout command
$ docker logout
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
In my case, the _Mountpoint_ was set to `/var/lib/docker/volumes/todo-db/_data` on the host machine.

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
$ cd getting-started-app
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
In general, each container should do one thing and do it well.

__Container networking__  
 Containers, by default, run in isolation and don't know anything about other processes or containers on the same machine.  
To make two containers talk to each other, you place the two containers on the same network.  
There are two ways to put a container on a network:
1. Assign the network when starting the container.
2. Connect an already running container to a network.

Lets create a network first and then attach an MySQL container to the network
1. Create the network
```bash
$ docker network create todo-app
```  
2. Start a MySQL container and attach it to the network.
A few environment variables used to initialize the database will also be defined.
```bash
$ docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0
```
  *  a volume named _todo-mysql-data_ is mounted at `/var/lib/mysql` which is where MySQL stores its data.
  * you never ran a `docker volume create` command so Docker  will create the named volume automatically for you.

3. Connect to the database to verify that it is running.
```bash
# First, get the container id
$ docker ps
$ docker exec -it <mysql-container-id> mysql -u root -p
```
Enter the password (secret) that was used when starting the container.  
Run a simple MySQL command to show all the database
```sql
> SHOW DATABASES;
# exit the shell
> exit;
```

__Connect to MySQL__  
We can use of the `nicolaka/netshoot` container, which ships with a lot of tools that are useful for  troubleshooting or debugging networking issues.
```bash
$ docker run -it --network todo-app nicolaka/netshoot
```
Use the `dig` command, inside the container to look up the IP address for the hostname `mysql`.
```bash  
$ dig mysql
```
The dig command will produce an answer section that looks like this
```
;; ANSWER SECTION:
mysql.			600	IN	A	172.21.0.2
```
This means the the `A` record for `mysql` resolves to 172.23.0.2 even though `mysql` is not a valid hostname.
Remember that this _mysql_ is the `network-alias` that we used when we started the MySQL container and attached it to a network.   
This implies that we can simply use the hostname `mysql` to connect to the MySQL container.  

__Run you app with MySQL__  
This starts the container and connect it to the Docker network created earlier.
```bash
$ cd getting-started-app
$ docker run -dp 127.0.0.1:3000:3000 \
  -w /app -v "$(pwd):/app" \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:18-alpine \
  sh -c "yarn install && yarn run dev"
```
Note that we used the `mysql` network alias as the MYSQL_HOST environment variable value.  
To check the logs coming out of the container
```bash
$ docker logs -f <container-id>
```
Launch the application http://localhost:3000 using your browser, add a couple of to do Items.
Check the database to confirm the items created.
```bash
# First, get the MySQL container id
$ docker ps
$ docker exec -it <mysql-container-id> mysql -u root -p
```
You will be prompted to enter the password, type the password we used in launching the container - secret.  
The MySQL shell will be launched.  
Run some MySQL commands.
```sql
mysql> show databases;
mysql> use todos;
mysql> show tables;
mysql> select * from todo_items;
```

When working with multiple container, you have to create a network, start containers, specify all of the environment variables, expose ports, and more.
With _Docker Compose_, you can share your application stacks in a much easier way and let others spin them up with a single, simple command.

__Use Docker Compose__  
A `compose.yaml` file must is used to describe the services (containers), networks and columns. The name of each service is automatically made an alias to the services network address.  
To start up the application stack using compose
```bash
$ docker compose up -d
```
Docker Compose will create the volume as well as a network specifically for the application stack which is why you didn't define one in the Compose file.  
To check the logs
```bash
$  docker compose logs -f
```
The logs from each of the services are interleaved into a single stream.

To tear down the application stack
```bash
$ docker compose down
```
The containers will be stop and the network will be removed.  
By default, named volumes in your compose file are not removed when you run `docker compose down`. If you want to remove the volumes, you need to add the `--volumes` flag.
```bash
$ docker compose down --volumes
```
#### Image-building best practices  
__Image Layering__  
Using the `image history` command, you can see the command that was used to create each layer within an image
```bash
$ docker image history <image-name>
```
Each of the lines in the output represents a layer in the image withe the base at the bottom and the newest layer at the top.   
Using this, you can also quickly see the size of each layer, helping diagnose large images.  

__Layer caching__  
 Once a layer changes, all downstream layers have to be recreated as well. You can use this knowledge to decrease build times for your container images. This is done by putting step that are likely not to produce a change before step that are likely to. For example, you may want to do the "dependency installation" step before "source code copy" step as shown below:
 ```
...
COPY package.json yarn.lock ./
RUN yarn install --production
COPY . .
...
 ```
 Now the dependency will be cached and not need to be shipped around each time we do a build.  Subsequently, pushing and pulling the image and updates to it will be much faster.

__Multi-stage builds__  
There are several advantages for using multi-stage build:
1. Separate build-time dependencies from runtime dependencies
2. Reduce overall image size by shipping only what your app needs to run

__Multi-stage builds: React example__
When building React applications, you need a Node environment to compile the JSX code int HTML, JS, and CSS. If you are not doing server-side rendering, you don't need a Node environment for your production build. You can ship the static resources in a static _nginx_ container.
See the _Dockerfile_ in `chp1/react-app`.  

__Docker Scan__    
Use `docker scan` to run _Snyk_ tests against images to find vulnerabilities and learn how to fix them  
