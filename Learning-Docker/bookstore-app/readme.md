# Dockerized Express application 

__To build the docker image__  
```
$ docker build . -t chucks/bookstore
```
The flat -t is used to tag the image so it's easier to find later using the `docker images` command.  
Here i passed the _<username>/<app-name>_ for the tag.   

__View all images__  
```
$ docker images 
```

__Run the image__  
```
$ docker run -p 9000:8080 -d chucks/bookstore
``` 
The -p flag maps the host port 9000 to the container port 8080.   
The -d flag runs the container in detached mode, leaving the container running in the background.  

__Test the application__  
Test the application on a browser or using a curl request 
```
$ curl -i http://localhost:9000
```

__Inspect your container__  
```bash
# view all containers
$ docker ps 
# check container logs using your container Id 
$ docker log 018ff4b9b5f1
``` 
To enter inside the container, use the exec command with the _container Id_  
```
$ docker exec -it 018ff4b9b5f1 /bin/bash 
```

__Clean up__ 
To stop the stop the runninng container, use the kil command with the _container Id_  
```
$ docker kill 018ff4b9b5f1
```

__Resources__ 
[Dockerizing a Node.js web app](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)