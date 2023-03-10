# Docker

* On WSL if docker is stopped - 

```
sudo service docker start
sudo service docker status
```

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


## docker compose

* till now , we created pods , we created networks and we run pods 
* the commands were having volumes, environment variables, publish ports
* while stopping, we need to stop containers, remove volumes and other work
* Imagine your application got more containers
* all these are discrete set of commands

* docker-compose is a tool which makes managing multi container setup easier

* tearing down is also easier
* setting up and tearing down can be done with simple command

* docker-compose helps to replace docker build and docker run command with one configuration file.
* docker compose does not replaces Dockerfile, Images or Containers

```
folder compose-02-finished
```

* explain yaml and syntax
* No need for network in docker compose , because by default all services in single docker-compose files will be placed on single network

```
folder compose-02-finished
docker-compose-mongo.yaml
```

After that

```
folder compose-02-finished
docker-compose-all.yaml
```

## Utility containers

* Nothing to Demo

* nothing official like Utility Containers

* Like environment containers , like php or node js, they don't have application inside it

* discuss about docker exec 

* diff bet CMD and ENTRYPOINT

https://devtron.ai/blog/cmd-and-entrypoint-differences/#:~:text=CMD%3A%20Sets%20default%20parameters%20that,Docker%20containers%20with%20CLI%20parameters

## Dev to Prod

* Bind vol shouldn't be use in production


# Kubernetes

* Independent container Orchestration , a framwork , a tool , collection of standards, helps large scale deployment independent of cloud providers

* https://kubernetes.io/ , official documentation

* Opensource, for automating, deployment, scaling and management of containerized application

* Without k8

![](without_k8.png)

* Its NOT a cloud service provider
* Its NOT a service by cloud service provider
* Its NOT restricted to a specific cloud provider
* Its NOT software but collection of concept and tools
* Its NOT replacement to docker, but it works with docker to deploy and manage containers at large scale
* Its NOT a paid service , but free opensource project (But if you use cloud provider k8 service , you need to pay)
* Its like Docker-Compose, for multiple machines


## Architecture

* In K8 , container is encapsulated in pod. Pod is smalled unit in K8
* Pod might have multiple containers
* Pod runs on worker node
* Worker nodes runs containers, so its like you machines or vms
* Worker nodes have finite memory, cpus
* To control network traffic to all the pods, Proxy/Config is deployed on worker node, like how the containers can be reached from outside world
* Typically you must have 1 worker node, but in real time, you will have more worker nodes with enough computing power
* K8 is responsible for distributing work load across worker nodes
* These worker nodes should be control by someone, someone need to deploy start stop shutdown containers on these worker nodes
* Thats done my Master node, specifically by conteol plane. It is control center
* Its another machine or remote machine
* You can have one machine which acts both master and worker node but for bigger deployments these are separate and deployed on multiple machines
* All together this forms a cluster

![](k8_arch.png)


![](worker_node.png)


![](master_node.png)


## Installation

* done with KinD
* Can be done with minikube for dev setup
* Playground is available at - https://www.katacoda.com/ 
(cbagade@yahoo.com/chand0617)
* But katacode is closed now

* Alternative is https://killercoda.com/playgrounds/scenario/kubernetes
* I signed with Gmail


## K8 Objects

### Pod

* Pod is smallest unit holding container or containers
* Contains shared resources (for ex.) volumes for all containers
* Has cluster internal IP by default
* If pod has multiple containers, then they can communicate with each other by localhost
* Pods are ephemeral in nature, nothin is statefull there. K8 will start, stop, replace them as needed. That what k8 is doing for us.
* IP changes when, pod is restarted

### Impervative approach

#### Deployment

* Instead pod, you always create deployment object
* It will control one or multiple pods
* We set desired and K8 will ensure to meet that
* If we mess , then we can rollback
* Deployment can scale dynamically with autoscaling with metrics

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-action-01-starting-setup

Instructions in Dockerfile
```

#### Service

* to reach pod
* expose pod to other pods in cluster or visitor outside cluster
* pod got internal IP address, but we can't use it outside cluster and IP will change when pod is replaced (like in scaling situation)
* kubectl describe pod <pod_name> | grep -i ip
* try demo , delete pod and when it is recreated, pod got diff IP
* So we can't use IP
* Service give pod a IP, which doesn't change
* It clubs Pod under a name
* Service can be exposed outside the cluster , so that pod is reachable outside pod

##### Type of Service

* ClusterIP -> reachable only inside cluster
* NodePort -> Service should be exposed with IP of worker node
* LoadBalancer -> This should exist in our infra and will generate unique address to access pod. It will distribute load (when it is scaled)


* on kinD cluster services don't work as stated
* cluster need to be created with a cluster-config.yml which defined extraport mappings , which allows traffic to flow inside kindD cluster

```
kind create cluster --config=cluster-config.yml --name=chandra-learnings
```

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-action-01-starting-setup

Instructions in Dockerfile
```



### Declarative approach

* lot of commands to write
* problem is same like docker commands vs docker compose
* tough to write commands for larger deployment
* kubernetes allows us to create resource file, having configuration objects in yaml file
* this file will have objects (Kind), which k8 understands

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-action-02-declarative-approach-basics

deployment.yaml and service.yaml got explaination
```

No commands and hence easy

* Refer for specs

https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/


* to remove everything

```
docker system prune -a
```


#### Reapply

* change yaml file and reapply
* Like changing replicas or image. Easy to make changes


```
gives current namespace
kubectl config view | grep namespace
kubectl config view --minify | grep namespace:

create new namespace
kubectl create namespace prep


switch namespace
kubectl config set-context --current --namespace=prep

```

#### Delete

kubectl delete -f deployment.yaml (or service.yaml)

#### Club related yamls

* can create something like master-deployment.yaml, which clubs related deployment , services

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-action-02-declarative-approach-basics\master-deployment.yaml
```

* In this file service definition is on top , which is good practice
*  It will keep adding pods dynamically to that service



#### Liveliness probe

```
file is \\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-action-02-declarative-approach-basics\deployment_liveness.yaml
```

* k8 uses liveness probes to know when to restart a container.For example, liveness probes could catch a deadlock, where an application is running, but unable to make progress.Restarting a container in such a state can help to make the application more available despite bugs

#### Image Pull policy 

make diff deployment.yaml while on tutorial, to explain
```
file is \\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-action-02-declarative-approach-basics\deployment_liveness.yaml
```

## Managing data and volumes with K8

* What about data , when pod hosting this container is removed

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-01-starting-setup\docker-compose.yaml
```

* how k8 deals with volumes

* k8 need to be configured to mount volume to our containers

* Demo without Volume

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-02-deployment-and-service

\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-02-deployment-and-service\deployment.yaml
```

* When pod is restarted data is lost

### emptyDir

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-03-first-volume\deployment.yaml
```

### hostPath

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-03-first-volume\deployment_hostpath.yaml
```

This sol won't work on multiple worker node

### Persistent volumes

* Pod and Node independent volumes are needed
* Need Persistent volume, which are pod and node independent
* User have full power how this volume is configured 

* Persistent volumes are new entities in clusters which are detached from nodes and pods
* Instead there is something PV Claims , inside nodes, which reached out to PVs. 
* PV don't store data on node

![](pv.png)

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-05-pv-and-pvc\host-pv.yaml

\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-05-pv-and-pvc\host-pvc.yaml

\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-05-pv-and-pvc\deployment.yaml
```

### Config Maps



```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-06-env\app.js (process.env.STORY_FOLDER)


\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-06-env\environment.yaml

\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-data-06-env\deployment.yaml


```


## Networking

![](demo_app.png)

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-network-02-dummy-user-service


Inside file
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-network-02-dummy-user-service\users-api\users-app.js

```

```
Inside app.post('/signup', async (req, res)
comment line
const hashedPW = await axios.get('http://auth/hashed-password/' + password);

Instead put
const hashedPW = 'dummy text';
```


```
Inside
app.post('/login', async (req, res) 
comment response which calls auth service
Harcode response
const response = { status: 200, data: { token: 'abc' } };
```


```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-network-02-dummy-user-service\kubernetes\users-deployment.yaml
```



### Pod Internal communication

```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-network-03-pod-internal\kubernetes\users-deployment.yaml

In 
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-network-03-pod-internal\users-api\users-app.js

auth service is called as ${process.env.AUTH_ADDRESS}
and localhost is passed from users-deployment.yaml
```

### Pod 2 Pod communication


```
\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-network-04-automatic-domain-names

\\wsl.localhost\Ubuntu-20.04\home\chandra\docker-learnings\kub-network-04-automatic-domain-names\kubernetes\users-deployment.yaml
```