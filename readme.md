# Docker

* Docker is a container technology, tool for creating and managing containers
* Container is unit packing code, required libraries and runtime 
* Always give same results (No chance for code working on my machine)

![](containers.png)

* Docker simplifies the creation and management of such containers

![](vms.png)

* VM though virtual but its like spinning a new vm on underlying OS and thus will eat CPU , memory from underlying OS.

![](containers_on_os.png)

* With containers
	* low impact on OS, fast, minimal disk space usage
	* sharing, re-building and distribution is easy
	* encapsulated apps having everything instead of machine
	
* Download Docker Desktop on Windows

* Playground (if you can't install) - https://labs.play-with-docker.com/
* From here you can ssh
* You should have docker account on docker hub

* On VSC , can have Docker extension

## Hands on

```
C:\PERSONAL_211122\DevOps\Docker and K8\code\first-demo-starting-setup
Also on WSL (on explorer \\wsl$)
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-code\first-demo-starting-setup

docker build -t cbagade/cl-first-prog:v1 . (can do docker build .)
docker images
docker run -p 3000:3000 cbagade/cl-first-prog:v1
(this will be in attached mode)
on browser hit http://localhost:3000

docker ps -a
now to stop on other terminal
docker stop <container_id>
docker rm <container_id>
```

* See the power, no nodejs was installed , no library was installed and still you able to run node js program

* This image if shared with other developer or environment, it will run exactly in same manner as it run now

### Images and Containers

* Container is unit of software
* Images will be blueprints of container
* Contains code + tool
* From one image , multiple containers can be build

![](1_image_m_cont.png)

* container can be created on multiple machine, on multiple environments.
* containers are running instances for images

* Lot of pre-built, standard images are available on docker hub
* node, java , dot-net all sort of images are available on docker hub
* try running
```
docker run node
```

* this will fetch node from docker hub and will run container based on this image
* this container will have node run time available (no application is running, yet)

* Nothing is running there so this container is exited

* -it flag opens interactive session from container to hosting machine

```
docker run -it node
this will open a interactive command inside container and now you can run node commands, like
1+1
```

* Mostly we build our images on base images like node js, php ,go , java, So the base image will provide you required runtime and tooling to run your project

```
C:\PERSONAL_211122\DevOps\Docker and K8\code\nodejs-app-starting-setup
All instructions in Docker file
Also (\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-code\nodejs-app-starting-setup)
Dockerfile is Dockerfile_1
This are checkedin from Ubuntu machine
```

* Any change to code would attract rebuilding images
* Demo with creating v2 image



#### Image Layers

* When you rebuild image , the instructions which are changes and everything after that is evaluated and built

* Try to re-build image without changing anything, it will be super fast and you can see things are used from cache

* So When you build docker will cache all instructions, when you rebuild then instructions are reevaluation against cached ones

* Every instruction is represented as layer in image

![](image_layers.png)

* The layers are readonly, means once built , you can't change

* When a layer change , all layers beyond that re executed

```
Dockerfile_2
# changed sequence of instructions here for better caching
# try rebuilding and see caching
# effective performance while building image
```

#### Managing containers and images

```
docker ps -> lists all running containers
docker ps -a -> lists all containers
docker run -> new container
docker stop <container_id> -> stops container
docker start <container_id> -> starts container
docker start -a <container_id> -> starts container in attached mode
docker logs <container_id> -> print logs with console.log statements
docker logs -f <container_id> -> shell is open to print ongoing logs
docker rmi <image_id> -> delete image
docker rm <container_id> -> delete container
docker run -d -p 3000:80 --rm <image_name> -> will run container and when it is stopped, will remove automatically
docker exec -it 3c848b7e7230 /bin/bash -> enter inside container
docker cp <file> <container_id>:/path -> copies file into running container (to debug something inside container)
docker cp <container_id>:/path /host_folder -> copies file from running container to host ( to inspect some file)
docker run -d -p 3000:80 --name chandra_container --rm cbagade/cl-node-app:v1 -> this will name the container 
docker image prune -a -> will remove all images, if are not having associated containers
docker pull <image_name> -> will pull image from repository

```

## Managing Data

* Like User data , user signing , user account , may be stored in files or database
* That data should be preserved even if container is stopped or restarted or shutdown
* Such data is stored in containers with *volumes*

```
code folder is data-volumes-01-starting-setup
temp folder will contain file having temporary data, the presence of file will be checked in feedback folder, if not available then will be copied (this will happen inside container and not host)
instructions are available inside Dockerfile
changes are not reflected on to host
```

### Volumes

* Helps persisting data
* Volumes are folder on your host machine, mounted on containers
* Like a connection between folder outside container to inside container
* Change at any place , will be reflected at other place
* So it allows to persist data and is available in case container is stop or shutdown
* Volume is not affected by container shutdown

#### Anonymous volumes (managed by docker)

```
code base is data-volumes-0101-starting-setup
Dockerfile_1
Things are written in Dockerfile, where Volume is mounted but it failed
The volume is gone here when container is stopped and removed.
This volume is called 'Anonymous Volume'

```


#### Named volumes (managed by docker)

* will survive container shutdown
* named volume are not attached to containers

```
code base is data-volumes-0101-starting-setup
Dockerfile_2

```

#### Bind volumes (managed by user)

* For bind volume , we know where on host volume is mapped
* Great for persistent and editable data
* Named volume is great for persistent data
* In out example, we need to build image everytime when we change code
* But if that code is uploaded to bind volumes, then container is aware of changes/edits and no need to rebuild image. Just example, not to be tried for real application images
* 

```
code base is data-volumes-0101-starting-setup
server.js is changed to add line
app.use("/bindvoldir", express.static("bindvoldir"));
Dockerfile_3

```

* Bind volumes are mostly for development, for changing something on fly and then not required to build images. 
* On production its not used

#### Read only volumes

------------------- NOT TO DEMO STARTS--------------------------------

* By default volumes are read/write. Containers are able to read and write data to volumes
* In docker run command , provide :ro option in bind volume syntax

```
docker run -p 3000:80 --name feedback-app -v feedback:/app/feedback -v "/home/chandra/docker-learnings/data-volumes-0101-starting-setup:/app:ro"  --rm cbagade/cl-data-vol:v3
```

------------------- NOT TO DEMO ENDS--------------------------------

#### Managing volumes

* Till now we created volume in docker run command
* One can create volume on fly and then use it in run command

```
docker volume create feedback-files
docker volume ls
docker volume inspect feedback-files (MountPath is not path on your host machine, but a little VM which is set by docker runtime)
docker volume rm feedback-files (if volume is in used, then can't be removed)
```



#### .dockerignore

* Exclude files from being copied
* say ignoring copying node_modules


## ARGuments and ENVironment variables

* ARG available inside of Dockerfile, but not available to application
* ENV available inside of Dockerfile and also to application code
* ENV avoids hardcoding of values inside containers. Like say Database username (not passwords)

```
Code base data-volumes-0102-starting-setup
Dockerfile_1 got instructions
```

* ARG is something which can be used during build time, not available on  code (server.js). 
* It is also not available for CMD instructions, which is a runtime instructions

```
Code base data-volumes-0102-starting-setup
Dockerfile_2 got instructions
```

## Networking

* say to call external REST API from withing container
* say to call database on our local machine
* container to container communication (cross container communication)
* best practice is every container should be main thing like app container , database container (both shouldn't be 1 container)


* Outof box containers can communicate with WWW
* You can send request to outside container on WWW

```
networks-starting-setup
observe app_1.js the mongo code is commented here
```


* Cross container communication

```
networks-starting-setup
observe app_2.js for mongo code but prior to that , lets run a mongo container
docker run -d --name mongodb mongo
see mongo container running
docker ps -a
docker inspect mongodb
look for "NetworkSettings" -> "IPAddress"
change app_2.js for IP
docker build -t cbagade/cl-networking:v2 .
docker run -p 3000:3000 -d --name network-app --rm cbagade/cl-networking:v2
```

* But we hardcoded IP Address of Mongo, this is not good
* We need to rebuild image when Mongo IP change
* Having --network demo-net in docker run command will ensure containers are in same network and can talk to each other by container name

```
observe app_3.js for changing IPAddress to mongo container name
```


```
remove images to clean up system
docker image prune -a
```

### Persisting mongodb data

* Look at https://hub.docker.com/_/mongo
* Refer to section WARNING (Windows & OS X): , which talks about path , where mongo stores data

```
same code base
Instructions in docker file
```


### Authentication mongodb

------------ NOT WORKING , NEED TO DEBUG STARTS ----------------------

* Refer section authentication
```
Dockerfile_2
app_4.js

```

------------ NOT WORKING , NEED TO DEBUG ENDS ---------------------- 

