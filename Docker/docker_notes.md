# Docker architecture:


* Client-server architecture
* Client talks to the Docker daemon
* The Docker daemon handles:
    - Building
    - Running
    - Distributing
    
* Both communicate using a REST API:
	- UNIX sockets
	- Network interface


## The Docker daemon (dockerd):

* Listens for Docker API requests and manages Docker objects:
	* Images
	* Containers
	* Networks
	* Volumes


# The Docker client (docker):


How users interact with Docker
Sends commands to dockerd

* Docker registries:

	* Stores Docker images
	* Public registry such as DockerHub
	* Let you run your own private registry


## Docker objects:


# Images:
Read-only template with instructions for creating a Docker container
Image is based on another image
Create your own images
Use images published to a registry
Use a Dockerfile to build images

# Containers:
Runnable instance of an image
Connect a container to networks
Attach storage
Create a new image based on its current state
Isolated from other containers and the host machine

# Services
Scale containers across multiple Docker daemons
Docker Swarm
Define the desired state
Service is load-balanced


# Docker Swarm:

Multiple Docker daemons (Master and Workers)
The daemons all communicate using the Docker API
Supported in Docker 1.12 and higher





# Under The Hood


## Docker engine:

* Modular in design:
    Batteries included but replaceable
* Based on open-standards outline by the Open Container Initiative

* The major components:
    - Docker client
    - Docker daemon
    - containerd
    - runc
* The components work together to create and run containers




 # Brief History of the Docker Engine


## The first release of Docker:

* The Docker daemon:
Monolithic binary
Docker client
Docker API
Container runtime
Image builds
Much more...

* LXC:
Namespaces
Control groups (cgroups)
Linux-specific



## Refactoring of the Docker Engine


# LXC was later replaced with libcontainer:
Docker 0.9
Platform agnostic<br/>

# Issues with the monolithic Docker daemon:
Harder to innovate
Slow
Not what the ecosystem wanted<br/>


# Docker became more modular:

Smaller more specialized tools
Pluggable architecture


# Open Container Initiative:

Image spec
Container runtime spec
Version 1.0 release in 2017
Docker Inc. heavily contributed
Docker 1.11 (2016) used the specification as much as possible


# runc:

Implemenation of the OCI container-runtime-spec
Lightweght CLI wrapper for libcontainer
Create containers


# containerd:

Manages container lifecycle
Start
Stop
Pause
Delete
Image management
Part of the 1.11 release


# shim:

Implemenation of daemonless Containers
containerd forks an instance of runc for each new container
runc process exits after the container is created
shim process becomes the container parent
Responsible for:
STDIN and STDOUT
Reporting exit status to the Docker daemon


## Running Containers

`docker container run -it --name <NAME> <IMAGE>:<TAG>`

**Creating a container:**

CLI use for executing a command
Docker client uses the appropriate API payload
POSTs to the correct API endpoint
Docker deamon receives instructions
Docker deamon calls containerd to start a new container
Docker daemon uses gRPC (a CRUD style API)
containerd creates an OCI bundle from the Docker image
Tells runc to create a container using the OCI bundle
runc interfaces with the OS kernal to get the constructs needed to create a container
This includes namespaces, cgroups, etc.
Container process starts as a child process
runc exits once the container starts
Process is complete, and container is running<br/><br/><br/>

## What are Docker images?<br/>

Docker Images:

Files comprised of multiple layers
Execute code in a Docker container
Built from the instructions
Use images to create an instance of a container
Docker images and layers
Image are made of multiple layers.
Each layer represents an instruction in the image’s Dockerfile.
Each layer except, the very last one, is read-only.
Each layer is only a set of differences from the layer before it.
Layers are stacked on top of each other.
Containers add new writable layers on top of the underlying layers
All changes made to a running container is made to the Container layer
What are containers?
A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.
Container and layers
Top writable layer
All changes are stored in the writable layer
The writable layer is deleted when the container is deleted
The image remains unchanged<br/><br/><br/>

# What is Docker Hub?<br/>

## Docker Hub:

*Public Docker registry
*Provided by Docker
*Features:
    -Repositories
    -Teams and Organizations
    -Official Images
    -Publisher Images
    -Builds
    -Webhooks

Management Commands:

builder Manage builds
config Manage Docker configs
container Manage containers
engine Manage the docker engine
image Manage images
network Manage networks
node Manage Swarm nodes
plugin Manage plugins
secret Manage Docker secrets
service Manage services
stack Manage Docker stacks
swarm Manage Swarm
system Manage Docker
trust Manage trust on Docker images
volume Manage volumes
docker image:

build Build an image from a dockerfile
history Show the history of an image
import Import the contents from a tarball to create a filesystem image
inspect Display detailed information on one or more images
load Load an image from a tar file or STDIN
ls List images
prune Remove unused images
pull Pull an image or a repository from a registry
push Push an image or a repository to a registry
rm Remove one or more images
save Save one or more images to a tar file (streamed to STDOUT by default)
tag Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
docker container:

attach Attach local standard input, output, and error streams to a running container
commit Create a new image from a container's changes
cp Copy files/folders between a container and the local filesystem
create Create a new container
diff Inspect changes to files or directories on a container's filesystem
exec Run a command in a running container
export Export a container's filesystem as a tar archive
inspect Display detailed information on one or more containers
kill Kill one or more running containers
logs Fetch the logs of a container
ls List containers
pause Pause all processes within one or more containers
port List port mappings or a specific mapping for the container
prune Remove all stopped containers
rename Rename a container
restart Restart one or more containers
rm Remove one or more containers
run Run a command in a new container
start Start one or more stopped containers
stats Display a live stream of container(s) resource usage statistics
stop Stop one or more running containers
top Display the running processes of a container
unpause Unpause all processes within one or more containers
update Update configuration of one or more containers
wait Block until one or more containers stop, then print their exit codes

## Creating Containers
In this lesson, we will take a deeper look into creating containers, by exploring a few of the flags that will alter it's behavior when created.

## docker container run:

--help Print usage
--rm Automatically remove the container when it exits
-d, --detach Run container in background and print container ID
-i, --interactive Keep STDIN open even if not attached
--name string Assign a name to the container
-p, --publish list Publish a container's port(s) to the host
-t, --tty Allocate a pseudo-TTY
-v, --volume list Mount a volume (the bind type of mount)
--mount mount Attach a filesystem mount to the container
--network string Connect a container to a network (default "default")

### Create a container and attach to it:

docker container run –it busybox

### Create a container and run it in the background:

docker container run –d nginx

### Create a container that you name and run it in the background:

docker container run –d –name myContainer busybox

## Exposing and Publishing Container Ports

Building on what we've already learned, this lesson will focus on exposing ports on a container, as well as how to publish them.

## Exposing:

Expose a port or a range of ports
This does not publish the port
Use --expose [PORT]
docker container run --expose 1234 [IMAGE]


## Publishing:

Maps a container's port to a host`s port
-p or --publish publishes a container's port(s) to the host
-P, or --publish-all publishes all exposed ports to random ports
docker container run -p [HOST_PORT]:[CONTAINER_PORT] [IMAGE]
docker container run -p [HOST_PORT]:[CONTAINER_PORT]/tcp -p [HOST_PORT]:[CONTAINER_PORT]/udp [IMAGE]
docker container run -P
Lists all port mappings or a specific mapping for a container:

docker container port [Container_NAME]

## Executing Container Commands
In this lesson we'll see three different ways to execute commands on containers.

# Executing a command:

Dockerfile
During a Docker run
Using the exec command

### Commands can be:

One and done Commands
Long running Commands

## Start a container with a command:

docker container run [IMAGE] [CMD]


## Execute a command on a container:

docker container exec -it [NAME] [CMD]
Example:

docker container run -d -p 8080:80 nginx
docker container ps
docker container exec -it [NAME] /bin/bash
docker container exec -it [NAME] ls /usr/share/nginx/html/

# Container Logging

## Create a container using the weather-app image.

docker container run --name weather-app -d -p 80:3000 linuxacademycontent/weather-app


## Show information logged by a running container:

docker container logs [NAME]


## Show information logged by all containers participating in a service:

docker service logs [SERVICE]


## Logs need to be output to STDOUT and STDERR.

### Nginx Example:

RUN ln -sf /dev/stdout /var/log/nginx/access.log \

    && ln -sf /dev/stderr /var/log/nginx/error.log

## Debug a failed container deploy:

docker container run -d --name ghost_blog \
-e database__client=mysql \
-e database__connection__host=mysql \
-e database__connection__user=root \
-e database__connection__password=P4sSw0rd0! \
-e database__connection__database=ghost \
-p 8080:2368 \
ghost:1-alpine
