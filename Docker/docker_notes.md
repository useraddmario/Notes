# Docker architecture:
<br/>
<br/>

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


## The Docker client (docker):


* How users interact with Docker
* Sends commands to dockerd <br/>

### Docker registries:

* Stores Docker images
* Public registry such as DockerHub
* Let you run your own private registry
<br/>

## Docker objects:


### Images:
* Read-only template with instructions for creating a Docker container
* Image is based on another image
* Create your own images
* Use images published to a registry
* Use a Dockerfile to build images

### Containers:
* Runnable instance of an image
* Connect a container to networks
* Attach storage
* Create a new image based on its current state
* Isolated from other containers and the host machine

### Services
* Scale containers across multiple Docker daemons
* Docker Swarm
* Define the desired state
* Service is load-balanced


### Docker Swarm:

* Multiple Docker daemons (Master and Workers)
* The daemons all communicate using the Docker API
* Supported in Docker 1.12 and higher
<br/>
<br/>



# Under The Hood


## Docker engine:

* Modular in design:
    - Batteries included but replaceable
* Based on open-standards outline by the Open Container Initiative

* The major components:
    - Docker client
    - Docker daemon
    - containerd
    - runc
* The components work together to create and run containers
<br/>
<br/>
 # Brief History of the Docker Engine


## The first release of Docker:

* The Docker daemon:
    - Monolithic binary
    - Docker client
    - Docker API
    - Container runtime
    - Image builds
    - Much more...

* LXC:
    - Namespaces
    - Control groups (cgroups)
    - Linux-specific



## Refactoring of the Docker Engine


### LXC was later replaced with libcontainer:
* Docker 0.9
* Platform agnostic<br/>

### Issues with the monolithic Docker daemon:
* Harder to innovate
* Slow
* Not what the ecosystem wanted<br/>


### Docker became more modular:

* Smaller more specialized tools
* Pluggable architecture


### Open Container Initiative:

* Image spec
* Container runtime spec
* Version 1.0 release in 2017
* Docker Inc. heavily contributed
* Docker 1.11 (2016) used the specification as much as possible


### runc:

* Implemenation of the OCI container-runtime-spec
* Lightweght CLI wrapper for libcontainer
* Create containers


### containerd:

* Manages container lifecycle
* Start
* Stop
* Pause
* Delete
* Image management
* Part of the 1.11 release


### shim:

* Implemenation of daemonless Containers
* containerd forks an instance of runc for each new container
* runc process exits after the container is created
* shim process becomes the container parent
* Responsible for:
* STDIN and STDOUT
* Reporting exit status to the Docker daemon


## Running Containers

`docker container run -it --name <NAME> <IMAGE>:<TAG>`

**Creating a container:**

* CLI use for executing a command
* Docker client uses the appropriate API payload
* POSTs to the correct API endpoint
* Docker deamon receives instructions
* Docker deamon calls containerd to start a new container
* Docker daemon uses gRPC (a CRUD style API)
* containerd creates an OCI bundle from the Docker image
* Tells runc to create a container using the OCI bundle
* runc interfaces with the OS kernal to get the constructs needed to create a container
* This includes namespaces, cgroups, etc.
* Container process starts as a child process
* runc exits once the container starts
* Process is complete, and container is running<br/><br/><br/>

## What are Docker images?

**Docker Images:**

* Files comprised of multiple layers
* Execute code in a Docker container
* Built from the instructions
* Use images to create an instance of a container
* Docker images and layers
* Image are made of multiple layers.
* Each layer represents an instruction in the image’s Dockerfile.
* Each layer except, the very last one, is read-only.
* Each layer is only a set of differences from the layer before it.
* Layers are stacked on top of each other.
* Containers add new writable layers on top of the underlying layers
* All changes made to a running container is made to the Container layer
* What are containers?
* A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.
* Container and layers
* Top writable layer
* All changes are stored in the writable layer
* The writable layer is deleted when the container is deleted
* The image remains unchanged
<br/>
<br/>

# What is Docker Hub?<br/>

## Docker Hub:

* Public Docker registry
* Provided by Docker
* Features:
    - Repositories
    - Teams and Organizations
    - Official Images
    - Publisher Images
    - Builds
    - Webhooks

## Management Commands:

* builder - Manage builds
* config  - Manage Docker configs
* container  - Manage containers
* engine  - Manage the docker engine
* image  - Manage images
* network  - Manage networks
* node  - Manage Swarm nodes
* plugin  - Manage plugins
* secret  - Manage Docker secrets
* service  - Manage services
* stack  - Manage Docker stacks
* swarm  - Manage Swarm
* system  - Manage Docker
* trust  - Manage trust on Docker images
* volume  - Manage volumes
* docker  - image:

* build  - Build an image from a dockerfile
* history  - Show the history of an image
* import  - Import the contents from a tarball to create a filesystem image
* inspect  - Display detailed information on one or more images
* load  - Load an image from a tar file or STDIN
* ls  - List images
* prune  - Remove unused images
* pull  - Pull an image or a repository from a registry
* push  - Push an image or a repository to a registry
* rm  - Remove one or more images
* save  - Save one or more images to a tar file (streamed to STDOUT by default)
* tag  - Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE

## docker container:

* attach  - Attach local standard input, output, and error streams to a running container
* commit  - Create a new image from a container's changes
* cp  - Copy files/folders between a container and the local filesystem
* create  - Create a new container
* diff  - Inspect changes to files or directories on a container's filesystem
* exec  - Run a command in a running container
* export  - Export a container's filesystem as a tar archive
* inspect  - Display detailed information on one or more containers
* kill  - Kill one or more running containers
* logs  - Fetch the logs of a container
* ls  - List containers
* pause  - Pause all processes within one or more containers
* port  - List port mappings or a specific mapping for the container
* prune  - Remove all stopped containers
* rename  - Rename a container
* restart  - Restart one or more containers
* rm  - Remove one or more containers
* run  - Run a command in a new container
* start  - Start one or more stopped containers
* stats  - Display a live stream of container(s) resource usage statistics
* stop  - Stop one or more running containers
* top  - Display the running processes of a container
* unpause  - Unpause all processes within one or more containers
* update  - Update configuration of one or more containers
* wait  - Block until one or more containers stop, then print their exit codes

## Creating Containers

` docker container run --help


`--rm Automatically remove the container when it exits`
`-d, --detach Run container in background and print container ID`
`-i, --interactive Keep STDIN open even if not attached`
`--name string Assign a name to the container`
`-p, --publish list Publish a container's port(s) to the host`
`-t, --tty Allocate a pseudo-TTY`
`-v, --volume list Mount a volume (the bind type of mount)`
`--mount mount Attach a filesystem mount to the container`
`--network string Connect a container to a network (default "default")`

### Create a container and attach to it:

`docker container run –it busybox`

### Create a container and run it in the background:

`docker container run –d nginx`

### Create a container that you name and run it in the background:

`docker container run –d –name myContainer busybox`
<br/>


# Exposing and Publishing Container Ports

## Exposing:

### Expose a port or a range of ports
**This does not publish the port**
`Use --expose [PORT]`

`docker container run --expose 1234 [IMAGE]`


## Publishing:

**Maps a container's port to a host`s port**

`-p or --publish publishes a container's port(s) to the host`
`-P, or --publish-all publishes all exposed ports to random ports`
`docker container run -p [HOST_PORT]:[CONTAINER_PORT] [IMAGE]`
`docker container run -p [HOST_PORT]:[CONTAINER_PORT]/tcp -p [HOST_PORT]:[CONTAINER_PORT]/udp [IMAGE]`
`docker container run -P`

**Lists all port mappings or a specific mapping for a container:**

`docker container port [Container_NAME]`

## Executing Container Commands


# Executing a command:

* Dockerfile
* During a Docker run
* Using the exec command

### Commands can be:

* One and done Commands
* Long running Commands

## Start a container with a command:

`docker container run [IMAGE] [CMD]`


## Execute a command on a container:

`docker container exec -it [NAME] [CMD]`

**Example:**

`docker container run -d -p 8080:80 nginx`
`docker container ps`
`docker container exec -it [NAME] /bin/bash`
`docker container exec -it [NAME] ls /usr/share/nginx/html/`
<br/>
<br/>

# Container Logging

## Create a container using the weather-app image.

`docker container run --name weather-app -d -p 80:3000 linuxacademycontent/weather-app`


## Show information logged by a running container:

`docker container logs [NAME]`


## Show information logged by all containers participating in a service:

`docker service logs [SERVICE]`


## Logs need to be output to STDOUT and STDERR.

**Nginx Example:**

`RUN ln -sf /dev/stdout /var/log/nginx/access.log \`
`    && ln -sf /dev/stderr /var/log/nginx/error.log`

## Debug a failed container deploy:

`docker container run -d --name ghost_blog \`
`-e database__client=mysql \`
`-e database__connection__host=mysql \`
`-e database__connection__user=root \`
`-e database__connection__password=P4sSw0rd0! \`
`-e database__connection__database=ghost \`
`-p 8080:2368 \`
`ghost:1-alpine`


# Networking Overview

## Docker Networking 101

### Docker Networking:

* Open-source pluggable architecture
* Container Network Model (CNM)
* libnetwork implements CNM
* Drivers extend the network topologies

### Network Drivers:

* bridge - **link layer device**, **default network**, layer of isolation, only works on Linux
* host - standalone containers, uses the host’s networking directly 
* overlay - connect multiple Docker daemons together for swarm services to communicate; can use overlay networks for communication between a swarm service and a standalone container, or between two standalone containers on different Docker daemons; no need to do OS-level routing between these containers
* macvlan -  allow you to assign a MAC address to a container; best for legacy, network monitoring app or smth that expects a hardware connection
* none - disable all networking, used in conjunction with a custom network driver; not available for swarm services
* Network plugins - install and use 3rd party network plugins with Docker plugins are available from Docker Hub or from 3rd party vendors

## Container Network Model

### Defines three building blocks:

* Sandboxes - isolates network stack including the network interfaces, ports, route tables and DNS
* Endpoints - virt NICs; responsible for connection to Sandbox
* Networks - software implementations of the 802.1D bridge


## Networking Commands

### Networking Basics

`ifconfig`

**Docker uses:**
Class B network inet 172.17.0.1 netmask 255.255.0.0 /16 65534

`docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500`
        `inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255`
        `ether 02:42:49:3b:29:0b  txqueuelen 0  (Ethernet)`
        `RX packets 0  bytes 0 (0.0 B)`
        `RX errors 0  dropped 0  overruns 0  frame 0`
        `TX packets 0  bytes 0 (0.0 B)`
        `TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0`


List all Docker network commands:

`docker network -h`

**Command Summary:** connect - Connect a container to a network create - Create a network disconnect - Disconnect a container from a network inspect - Display detailed information on one or more networks ls - List networks prune - Remove all unused networks rm - Remove one or more networks

List all Docker networks on the host:

`docker network ls`

**Default Docker networks:**
`docker network ls`
`NETWORK ID          NAME                DRIVER              SCOPE`
`f7ddb9f1e359        bridge              bridge              local`
`1ca008bd8b6f        host                host                local`
`d7d7d6a04bd9        none                null                local`



`docker network ls --no-trunc`

Getting detailed info on a network:

`docker network inspect [NAME]`

docker network inspect bridge --format='{{.IPAM.Config}}'
[{172.17.0.0/16  172.17.0.1 map[]}]

docker network inspect bridge --format='{{json .IPAM.Config}}'
[{"Subnet":"172.17.0.0/16","Gateway":"172.17.0.1"}]

docker network inspect bridge --format='{{range .IPAM.Config}}{{.Subnet}}{{end}}'
172.17.0.0/16

docker network inspect bridge --format '{{.ID}}: {{.Driver}} {{.Scope}}'
f7ddb9f1e3591853cb69b904164b59f68b8a23e4cc4b6b5edf8836f5f1339739: bridge local

Creating a network:

`docker network create br00`
<br/>
`ecee34a4de25d5c16b5a51088f036676a5199ccc90c320e3c5d6fe385f1d4a1a`

`docker network inspect br00`
<br/>
`[`
    `{`
        `"Name": "br00",`
        `"Id": "ecee34a4de25d5c16b5a51088f036676a5199ccc90c320e3c5d6fe385f1d4a1a",`
        `"Created": "2021-04-01T04:55:05.503208315Z",`
        `"Scope": "local",`
        `"Driver": "bridge",`
        `"EnableIPv6": false,`
        `"IPAM": {`
            `"Driver": "default",`
            `"Options": {},`
            `"Config": [`
                `{`
                    `"Subnet": "172.18.0.0/16",`
                    `"Gateway": "172.18.0.1"`
                `}`
            `]`
        `},`
        `"Internal": false,`
        `"Attachable": false,`
        `"Ingress": false,`
        `"ConfigFrom": {`
            `"Network": ""`
        `},`
        `"ConfigOnly": false,`
        `"Containers": {},`
        `"Options": {},`
        `"Labels": {}`
    `}`
`]`

Deleting a network:

`docker network rm [NAME]`

Remove all unused networks

`docker network prune`


### Adding and Removing containers to a network

Create a container with no network:

`docker container run -d --name network-test03 -p 8081:80 nginx`

Create a new network:

`docker network create br01`

Add the container to the bridge network:

`docker network connect br01 network-test03`

Inspect network-test03 to see the networks:

`docker container inspect network-test03`

`docker container inspect network-test03 --format='{{json .NetworkSettings}}' | jq`
<br/>
`{`
  `"Bridge": "",`
  `"SandboxID": "f4a240cf4e6466fcb37a90cb312999df5fc02ad7ef10b725a6b044c8f1295f21",`
  `"HairpinMode": false,`
  `"LinkLocalIPv6Address": "",`
  `"LinkLocalIPv6PrefixLen": 0,`
  `"Ports": {`
    `"80/tcp": [`
      `{`
        `"HostIp": "0.0.0.0",`
        `"HostPort": "8081"`
      `}`
    `]`
  `},`
  `"SandboxKey": "/var/run/docker/netns/f4a240cf4e64",`
  `"SecondaryIPAddresses": null,`
  `"SecondaryIPv6Addresses": null,`
  `"EndpointID": "b9b182ef1a00ef9cadfdbb6c5a8880fb06a09de0a359d757fac6205c4b04a16c",`
  `"Gateway": "172.17.0.1",`
  `"GlobalIPv6Address": "",`
  `"GlobalIPv6PrefixLen": 0,`
  `"IPAddress": "172.17.0.2",`
  `"IPPrefixLen": 16,`
  `"IPv6Gateway": "",`
  `"MacAddress": "02:42:ac:11:00:02",`
  `"Networks": {`
    `"br01": {`
      `"IPAMConfig": {},`
      `"Links": null,`
      `"Aliases": [`
        `"b1c944bc3942"`
      `],`
      `"NetworkID": "0112218a887d9bdd6d855a73b9126bf5897271ec2cccd5b4a7553ecf31bb3f2e",`
      `"EndpointID": "e4bf48be7337bc2d4011630c03ba254127e0c8f295e51488f7ac067c7b183ee6",`
      `"Gateway": "172.19.0.1",`
      `"IPAddress": "172.19.0.2",`
      `"IPPrefixLen": 16,`
      `"IPv6Gateway": "",`
      `"GlobalIPv6Address": "",`
      `"GlobalIPv6PrefixLen": 0,`
      `"MacAddress": "02:42:ac:13:00:02",`
      `"DriverOpts": null`
    `},`
    `"bridge": {`
      `"IPAMConfig": null,`
      `"Links": null,`
      `"Aliases": null,`
      `"NetworkID": "f7ddb9f1e3591853cb69b904164b59f68b8a23e4cc4b6b5edf8836f5f1339739",`
      `"EndpointID": "b9b182ef1a00ef9cadfdbb6c5a8880fb06a09de0a359d757fac6205c4b04a16c",`
      `"Gateway": "172.17.0.1",`
      `"IPAddress": "172.17.0.2",`
      `"IPPrefixLen": 16,`
      `"IPv6Gateway": "",`
      `"GlobalIPv6Address": "",`
      `"GlobalIPv6PrefixLen": 0,`
      `"MacAddress": "02:42:ac:11:00:02",`
      `"DriverOpts": null`
    `}`
  `}`
`}`




Remove network-test03 from br01:

`docker network disconnect br01 network-test03`




## Networking Containers

### Creating a network and defining a Subnet and Gateway


Create a bridge network with a subnet and gateway:

`docker network create --subnet 10.1.0.0/24 --gateway 10.1.0.1 br02`


Run ifconfig to view the bridge interface for br02:

`ifconfig`


Inspect the br02 network:

`docker network inspect br02`


Prune all unused networks:

`docker network prune`


Create a network with an IP range:

`docker network create --subnet 10.1.0.0/16 --gateway 10.1.0.1 \`
`--ip-range=10.1.4.0/24 --driver=bridge --label=host4network br04`


Inspect the br04 network:

`docker network inspect br04`


Create a container using the br04 network:

`docker container run --name network-test01 -it --network br04 centos /bin/bash`


Install Net Tools:

yum install -y net-tools


Get the IP info for the container:

`ifconfig`


Get the gateway info the container:

`netstat -rn`


Get the DNS info for the container:

`cat /etc/resolv.conf`

**Docker manages /etc/hostname /etc/resolv.conf /etc/hosts**


Assigning IPs to a container:

Create a new container and assign an IP to it:

`docker container run -d --name network-test02 --ip 10.1.4.102 --network br04 nginx`


Get the IP info for the container:

`docker container inspect network-test02 | grep IPAddr`

docker container inspect network-test02 --format='{{json .NetworkSettings.Networks.br04.IPAddress}}' | jq
"10.1.4.102"
or
docker container inspect network-test02 --format='{{.NetworkSettings.Networks.br04.IPAddress}}'
10.1.4.102


Inspect network-test03 to see that br01 was removed:

`docker container inspect network-test04`


Networking two containers

Create an internal network:

`docker network create -d bridge --internal localhost`


Create a MySQL container that is connected to localhost:

`docker container run -d --name test_mysql \`
`-e MYSQL_ROOT_PASSWORD=P4sSw0rd0 \`
`--network localhost mysql:5.7`


Create a container that can ping the MySQL container:

`docker container run -it --name ping-mysql \`
`--network bridge \`
`centos`


Connect ping-mysql to the localhost network:

`docker network connect localhost ping-mysql`


Restart and attach to container:

`docker container start -ia ping-mysql`


Create a container that can't ping the MySQL container:

`docker container run -it --name cant-ping-mysql centos`


Create a Nginx container that is not publicly accessible:

`docker container run -d --name private-nginx -p 8081:80 --network localhost nginx`


Inspect private-nginx:

`docker container inspect private-nginx`




# Storage Overview

## Docker Storage 101

### Categories of data storage:

* Non-persistent
  * Local storage
  * Data that is ephemeral
  * Every container has it
  * Tied to the lifecycle of the contain
* Persistent
  * Volumes
      - Volumes are decoupled from containers


## Non-persistent Data


### Non-persistent data:

* By default all container use local storage
* Storage locations:
  * Linux: /var/lib/docker/[STORAGE-DRIVER]/
  * Windows: C:\ProgramData\Docker\windowsfilter\
* Storage Drivers:
  * RHEL uses overlay2.
  * Ubuntu uses overlay2 or aufs.
  * SUSE uses btrfs.
  * Windows uses its own.


## Persistent Data Using Volumes

### Volumes:

* Use a volume for persistent data:
  * Create the volume first, then create your container.
* Mounted to a directory in the container
* Data is written to the volume
* Deleting a container does not delete the volume
* First-class citizens
* Uses the local driver
* Third party drivers:
  * Block storage
  * File storage
  * Object storage
* Storage locations:
  * Linux: /var/lib/docker/volumes/
  * Windows: C:\ProgramData\Docker\volumes



## Volume Commands

### Volume Basics
List all Docker volume commands:
`docker volume -h`

* create: Create a volume.
* inspect: Display detailed information on one or more volumes.
* ls: List volumes.
* prune: Remove all unused local volumes.
* rm: Remove one or more volumes.

List all volumes on a host:

`docker volume ls`

Create two new volumes:

`docker volume create test-volume1`
`docker volume create test-volume2`


Get the flags available when creating a volume:

`docker volume create -h`

Inspecting a volume:

`docker volume inspect test-volume1`

Deleting a volume:

`docker volume rm test-volume`

Removing all unused volumes:

`docker volume prune`



## Using Bind Mounts


**Use case: config files**

Volumes use a new directory that is created within Docker’s storage directory on the host machine, and Docker manages that directory’s contents.

Using the mount flag:

```
mkdir target

docker container run -d \
  --name nginx-bind-mount1 \
  --mount type=bind,source="$(pwd)"/target,target=/app \
  nginx
docker container ls
```

Bind mounts won't show up when listing volumes:

`docker volume ls`


Inspect the container to find the bind mount:

`docker container inspect nginx-bind-mount1`


Create a new file in /app on the container:

```
docker container exec -it nginx-bind-mount1 /bin/bash
cd target
touch file1.txt
ls
exit
```


Using the volume flag:

```
docker container run -d \
 --name nginx-bind-mount2 \
 -v "$(pwd)"/target2:/app \
 nginx
 ```


Create /app/file3.txt in the container:

`docker container exec -it nginx-bind-mount2 touch /app/file3.txt`
`ls target2`


Create an nginx.conf file:

```
mkdir nginx
cat << EOF >  nginx/nginx.conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
EOF
```



Create an Nginx container that creates a bind mount to nginx.conf:

```
docker container run -d \
 --name nginx-bind-mount3 \
 -v "$(pwd)"/nginx/nginx.conf:/etc/nginx/nginx.conf \
 nginx
 ```


Look at the bind mount by inspecting the container:

`docker container inspect nginx-bind-mount3`


## Using Volumes for Persistent Storage

Volumes are easier to back up or migrate than bind mounts. You can manage volumes using Docker CLI commands or the Docker API. They work on both Linux and Windows containers. Volumes can be more safely shared among multiple containers. Volume drivers allow for:

* Storing volumes on remote hosts or cloud providers
* Encrypting the contents of volumes
* Add other functionality

New volumes can have their content pre-populated by a container.



Create a new volume for an Nginx container:

`docker volume create html-volume`


Creating a volume using that volume mount:

```
docker container run -d \
 --name nginx-volume1 \
 --mount type=volume,source=html-volume,target=/usr/share/nginx/html/ \
 nginx
 ```


Inspect the volume:

`docker volume inspect html-volume`



List the contents of html-volume:

`sudo ls /var/lib/docker/volumes/html-volume/_data`


Creating a volume using that volume flag:

```
docker container run -d \
 --name nginx-volume2 \
 -v html-volume:/usr/share/nginx/html/ \
 nginx
 ```



Edit index.html:

`sudo vi /var/lib/docker/volumes/html-volume/_data/index.html`



Inspect nginx-volume2 to get the private IP:

`docker container inspect nginx-volume2`



Login into nginx-volume1 and go to the html directory:

`docker container exec -it nginx-volume1 /bin/bash`
`cd /usr/share/nginx/html`
`cat index.hml`



Install Vim:

`apt-get update -y`
`apt-get install vim -y`



Using a readonly volume:

```
docker run -d \
  --name=nginx-volume3 \
  --mount source=html-volume,target=/usr/share/nginx/html,readonly \
  nginx
```



Login into nginx-volume3 and go to the html directory:

`docker container exec -it nginx-volume3 /bin/bash`
`cd /usr/share/nginx/html`
`cat index.hml`



Install Vim:

`apt-get update -y`
`apt-get install vim -y`




# Introduction to the Dockerfile Format

* Docker images consist of read-only layers.
* Each represents a Dockerfile instruction.
* Layers are stacked.
* Each layer is a result of the changes from the previous layer.
* Images are built using the docker image build command.


## Dockerfile Layers

```
Dockerfile:  
FROM ubuntu:15.04  
COPY . /app  
RUN make /app  
CMD python /app/app.py  
```

* FROM creates a layer from the ubuntu:15.04 Docker image.
* COPY adds files from your Docker client’s current directory.
* RUN builds your application with make.
* CMD specifies what command to run within the container.


## Best Practices

### General guidelines:

* Keep containers as ephemeral as possible.
* Follow Principle 6 of the 12 Factor App.
* Avoid including unnecessary files.
* Use .dockerignore.
* Use multi-stage builds.
* Don’t install unnecessary packages.
* Decouple applications.
* Minimize the number of layers.
* Sort multi-line arguments.
* Leverage build cache.





# Working with Instructions


* ` FROM: `Initializes a new build stage and sets the Base Image
* ` RUN: `Will execute any commands in a new layer
* ` CMD: `Provides a default for an executing container. There can only be one CMD instruction in a Dockerfile
* ` LABEL: `Adds metadata to an image
* ` EXPOSE: `Informs Docker that the container listens on the specified network ports at runtime
* ` ENV: `Sets the environment variable <key> to the value <value>
* ` ADD: `Copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the image at the path <dest>.
* ` COPY: `Copies new files or directories from <src> and adds them to the filesystem of the container at the path <dest>.
* ` ENTRYPOINT: `Allows for configuring a container that will run as an executable
* ` VOLUME: `Creates a mount point with the specified name and marks it as holding externally mounted volumes from native host or other containers
* ` USER: `Sets the user name (or UID) and optionally the user group (or GID) to use when running the image and for any RUN, CMD, and ENTRYPOINT instructions that follow it in the Dockerfile
* ` WORKDIR: `Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow it in the Dockerfile
* ` ARG: `Defines a variable that users can pass at build-time to the builder with the docker build command, using the --build-arg <varname>=<value> flag
* ` ONBUILD: `Adds a trigger instruction to the image that will be executed at a later time, when the image is used as the base for another build
* ` HEALTHCHECK: `Tells Docker how to test a container to check that it is still working
* ` SHELL: `Allows the default shell used for the shell form of commands to be overridden




To set up the environment:

```
sudo yum install git -y
mkdir docker_images
cd docker_images
mkdir weather-app
cd weather-app
git clone https://github.com/linuxacademy/content-weather-app.git src
```



Create the Dockerfile:

`vim Dockerfile`


Dockerfile contents:

```
# Create an image for the weather-app
FROM node
LABEL org.label-schema.version=v1.1
RUN mkdir -p /var/node
ADD src/ /var/node/
WORKDIR /var/node
RUN npm install
EXPOSE 3000
CMD ./bin/www
```



Build the weather-app image:

`docker image build -t linuxacademy/weather-app:v1 .`



List the images:

`docker image ls`



Create the weather-app container:

`docker container run -d --name weather-app1 -p 8081:3000 linuxacademy/weather-app:v1`



List all running containers:

`docker container ls`


## Environment Variables

### Setup your environment:

```
cd docker_images
mkdir env
cd env
```



Use the --env flag to pass an environment variable when building an image:

`--env [KEY]=[VALUE]`



Use the ENV instruction in the Dockerfile:

`ENV [KEY]=[VALUE]  `
`ENV [KEY] [VALUE]`



Clone the weather-app:

`git clone https://github.com/linuxacademy/content-weather-app.git src`



Create the Dockerfile

`vim Dockerfile`



Dockerfile contents:

```
# Create an image for the weather-app
FROM node
LABEL org.label-schema.version=v1.1
ENV NODE_ENV="development"
ENV PORT 3000

RUN mkdir -p /var/node
ADD src/ /var/node/
WORKDIR /var/node
RUN npm install
EXPOSE $PORT
CMD ./bin/www
```



Create the weather-app container:

`docker image build -t linuxacademy/weather-app:v2 .`



Inspect the container to see the environment variables:

`docker image inspect linuxacademy/weather-app:v2`



Deploy the weather-dev application:

`docker container run -d --name weather-dev -p 8082:3001 --env PORT=3001 linuxacademy/weather-app:v2`



Inspect the development container to see the environment variables:

`docker container inspect weather-dev`




Deploy the weather-app to production:

`docker container run -d --name weather-app2 -p 8083:3001 --env PORT=3001 --env NODE_ENV=production linuxacademy/weather-app:v2`



Inspect the production container to see the environment variables:

`docker container inspect weather-app2`



Get the logs for weather-app2:

`docker container logs weather-app2`
`docker container run -d --name weather-prod -p 8084:3000 --env NODE_ENV=production linuxacademy/weather-app:v2`






# Build Arguments

Use the --build-arg flag when building an image:

`--build-arg [NAME]=[VALUE]`



Use the ARG instruction in the Dockerfile:

`ARG [NAME]=[DEFAULT_VALUE]`



Navigate to the args directory:

```
cd docker_images
mkdir args
cd args
```



Clone the weather-app:

`git clone https://github.com/linuxacademy/content-weather-app.git src`



Create the Dockerfile:

`vi Dockerfile`



Dockerfile:

```
# Create an image for the weather-app
FROM node
LABEL org.label-schema.version=v1.1
ARG SRC_DIR=/var/node

RUN mkdir -p $SRC_DIR
ADD src/ $SRC_DIR
WORKDIR $SRC_DIR
RUN npm install
EXPOSE 3000
CMD ./bin/www
```



Build the weather-app image:

`docker image build -t linuxacademy/weather-app:v3 --build-arg SRC_DIR=/var/code .`



Inspect the image:

`docker image inspect linuxacademy/weather-app:v3 | grep WorkingDir`



Create the weather-app container:

`docker container run -d --name weather-app3 -p 8085:3000 linuxacademy/weather-app:v3`



Verify that the container is working by executing curl:

`curl localhost:8085`





# Working with Non-privileged Users

Setup your environment:

```
cd docker_images
mkdir non-privileged-user
cd non-privileged-user
```



Create the Dockerfile:

`vi Dockerfile`



Dockerfile contents:

```
# Creates a CentOS image that uses cloud_user as a non-privileged user
FROM centos:latest
RUN useradd -ms /bin/bash cloud_user
USER cloud_user
```



Build the new image:

`docker image build -t centos7/nonroot:v1 .`



Create a container using the new image:

`docker container run -it --name test-build centos7/nonroot:v1 /bin/bash`



Connecting as a privileged user:

`docker container start test-build`
`docker container exec -u 0 -it test-build /bin/bash`



Set up the environment:

```
cd ~/docker_images
mkdir node-non-privileged-user
cd node-non-privileged-user
```



Create the Dockerfile:

`vi Dockerfile`



Dockerfile contents:

```
# Create an image for the weather-app
FROM node
LABEL org.label-schema.version=v1.1
RUN useradd -ms /bin/bash node_user
USER node_user
ADD src/ /home/node_user
WORKDIR /home/node_user
RUN npm install
EXPOSE 3000
CMD ./bin/www
git clone https://github.com/linuxacademy/content-weather-app.git src
```



Build the weather-app image using the non-privileged user node_user:

`docker image build -t linuxacademy/weather-app-nonroot:v1 .`



Create a container using the linuxacademy/weather-app-nonroot:v1 image:

`docker container run -d --name weather-app-nonroot -p 8086:3000 linuxacademy/weather-app-nonroot:v1`






# Order of Execution

Setup your environment:

```
cd docker_images
mkdir centos-conf
cd centos-conf
```



Create the Dockerfile:

`vi Dockerfile`



Dockerfile contents:

```
# Creates a CentOS image that uses cloud_user as a non-privileged user
FROM centos:latest
RUN mkdir -p ~/new-dir1
RUN useradd -ms /bin/bash cloud_user
USER cloud_user
RUN mkdir -p ~/new-dir2
RUN mkdir -p /etc/myconf
RUN echo "Some config data" >> /etc/myconf/my.conf
```



Build the new image:

`docker image build -t centos7/myconf:v1 .`





Using the Volume Instruction

Set up your environment:

`cd docker_images`
`mkdir volumes`
`cd volumes`



Create the Dockerfile:

vi Dockerfile



Build an Nginx image that uses a volume:

`FROM nginx:latest`
`VOLUME ["/usr/share/nginx/html/"]`



Build the new image:

`docker image build -t linuxacademy/nginx:v1 .`



Create a new container using the linuxacademy/nginx:v1 image:

`docker container run -d --name nginx-volume linuxacademy/nginx:v1`



Inspect nginx-volume:

`docker container inspect nginx-volume`



List the volumes:

`docker volume ls | grep [VOLUME_NAME]`



Inspect the volumes:

`docker volume inspect [VOLUME_NAME]`






# Entrypoint vs. Command
In this lesson, we will begin working with the ENTRYPOINT instruction. Though ENTRYPOINT functions very similarly to CMD it's behaviors are very different.

ENTRYPOINT allows us to configure a container that will run as an executable.
We can override all elements specified using CMD.
Using the docker run --entrypoint flag will override the ENTRYPOINT instruction.



Setup your environment:

```
cd docker_images
mkdir entrypoint
cd entrypoint
```



Create the Dockerfile:

`vi Dockerfile`



Dockerfile contents:

```
# Create an image for the weather-app
FROM node
LABEL org.label-schema.version=v1.1
ENV NODE_ENV="production"
ENV PORT 3001

RUN mkdir -p /var/node
ADD src/ /var/node/
WORKDIR /var/node
RUN npm install
EXPOSE $PORT
ENTRYPOINT ./bin/www
```



Clone the image:

`git clone https://github.com/linuxacademy/content-weather-app.git src`




Build the image:

`docker image build -t linuxacademy/weather-app:v4 .`




Deploy the weather-app:

`docker container run -d --name weather-app4 linuxacademy/weather-app:v4`




Inspect weather-app4:

```
docker container inspect weather-app4 | grep Cmd
docker container inspect weather-app-nonroot
docker container inspect weather-app4
```



Create the weather-app container:

`docker container run -d --name weather-app5 -p 8083:3001 linuxacademy/weather-app:v4 echo "Hello World"`



Inspect weather-app5:

`docker container inspect weather-app5`



Create the volumes for Prometheus:

```
docker volume create prometheus
docker volume create prometheus_data

sudo chown -R nfsnobody:nfsnobody /var/lib/docker/volumes/prometheus/
sudo chown -R nfsnobody:nfsnobody /var/lib/docker/volumes/prometheus_data/
```




Create the Prometheus container:

```
docker run --name prometheus -d -p 8084:9090 \
  -v prometheus:/etc/prometheus \
  -v prometheus_data:/prometheus/data \
  prom/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/prometheus/data
  ```


Inspect Prometheus:

`docker container inspect prometheus`





# Using .dockerignore

In this lesson, we'll create a .dockerignore file, so that we can exclude files we don't want copied over when building an image.

Setup your environment:

```
cd docker_images
mkdir dockerignore
cd dockerignore
git clone https://github.com/linuxacademy/content-weather-app.git src
cd src
git checkout dockerignore
cd ../
```



Create the .dockerignore file:

`vi .dockerignore`



Add the following to .dockerignore:

```
# Ignore these files
*/*.md
*/.git
src/docs/
*/tests/
```



Create the Dockerfile:

`vi Dockerfile`



Dockerfile contents:

```
# Create an image for the weather-app
FROM node
LABEL org.label-schema.version=v1.1
ENV NODE_ENV="production"
ENV PORT 3000

RUN mkdir -p /var/node
ADD src/ /var/node/
WORKDIR /var/node
RUN npm install
EXPOSE $PORT
ENTRYPOINT ["./bin/www"]
```



Build the image:

`docker image build -t linuxacademy/weather-app:v5 .`



Create the weather-app container:

`docker container run -d --name weather-app-ignore linuxacademy/weather-app:v5`



List the contents of /var/node:

`docker container exec weather-app-ignore ls -la /var/node`








