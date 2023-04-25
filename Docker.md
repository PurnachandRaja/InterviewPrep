### 1.How to pass arguments While building Docker image ?
ARG instruction defines a variable that can be passed at build time. Once it is defined in the Dockerfile you can pass with this flag --build-arg while building the image. We can have multiple ARG instruction in the Dockerfile. ARG is the only instruction that can precede the FROM instruction in the Dockerfile.

```dockerfile
# base image
ARG TAG=slim
FROM node:$TAG

# setting the work direcotry
WORKDIR /usr/src/app

# copy package.json
COPY ./package.json .

# install dependencies
RUN npm install

# COPY index.js
COPY ./index.js .

EXPOSE 3080

RUN node -v

CMD ["node","index.js"]
```

docker build with build arg
```
// with slim tag
docker build -t nodejs-server -f Dockerfile.arg --build-arg TAG=slim .
// with latest tag
docker build -t nodejs-server -f Dockerfile.arg --build-arg TAG=latest .
```

If you want to pass multiple build arguments with docker build command you have to pass each argument with separate — build-arg.

```
docker build -t <image-name>:<tag> --build-arg <key1>=<value1> --build-arg <key2>=<value2>
```

### 2.How to pass arguments/vaules while building Docker conatiner ?
We can pass an argument with docker run command those will be passed to entry point example 

```
docker run <imagename> 10 100
```
### 3.How to scan the Docker Image ?
The docker scan command allows you to scan existing Docker images using the image name or ID.

```
docker scan hello-world
```
### 4.What is the difference between CMD & ENTRYPOINT ?
Both CMD and ENTRYPOINT instructions define what command gets executed when running a container. There are few rules that describe their co-operation.

Dockerfile should specify at least one of CMD or ENTRYPOINT commands.

ENTRYPOINT should be defined when using the container as an executable.

CMD should be used as a way of defining default arguments for an ENTRYPOINT command or for executing an ad-hoc command in a container.

CMD will be overridden when running the container with alternative arguments.

### 5.Explain Docker Networking ?
Docker networking is the system that enables communication between Docker containers, as well as between Docker containers and the host system. Docker networking enables containers to communicate with each other, share resources, and access services running on the host or other containers in a network.

Docker networking provides different network drivers that allow you to create different types of networks for your containers. These drivers include:

Bridge network driver: This is the default network driver in Docker and it creates a private internal network for the containers. Each container is assigned an IP address, and they can communicate with each other using those addresses.

Host network driver: This driver allows containers to use the host system's network directly. This means that containers can access resources on the host system as if they were running directly on the host.

Overlay network driver: This driver enables communication between containers running on different Docker hosts. It is commonly used in container orchestration systems like Docker Swarm or Kubernetes.

Macvlan network driver: This driver allows containers to have their own MAC address and be directly connected to the physical network. This is useful for scenarios where you want containers to appear as if they are physical hosts on the network.

### 6.What is the purpose of Docker multistage build ?
To reduce the size of the docker image. docker build get extensively large once you start adding more artifacts that would be needed in production. However, most of the time you’ll not need the built environment as part of the final container.

Our hello world program is stored as a main.go file.

```go
package main

import "fmt"

func main() {
    fmt.Println("hello world")
}
```
multi-stage docker file named Dockerfile, for this example, will look like this:

```dockerfile
FROM golang:1.11.1 AS builder
RUN mkdir /app
ADD . /app
WORKDIR /app
# Create executable for Go code
RUN CGO_ENABLED=0 GOOS=linux go build -o main ./...

# Second stage build which will execute the binary
FROM alpine:latest AS production
COPY --from=builder /app .
CMD ["./main"]
```
Let’s break the multi-stage builds into sections

```dockerfile
FROM golang:1.11.1 AS builder
```
We are aliasing the first image as builder, which we are then referring to in the second build, to copy our artifact from (in this case the golang executable file) as:
```dockerfile
COPY --from=builder /app .
```
In the first stage, we are making an executable file for the Go code. This env would need golang and thus the very first base image that we use is of golang. Next, in the second stage, we just use alpine as the base image (which is very small in size) and does not contain the golang runtime. We just copy our executable from the previous stage in our final stage.

The container that is spawned from this multi-stage docker file, will just have the alpine image and run the executable. It does not care about the source code or how it is generated and if it even has dependencies for that.


### 7.Explain about the Docker volumes ?
In Docker, a volume is a way to store and share data between containers or between a container and the host system. A volume is a directory or a file in the host or within a container that is mounted as a data volume within a container.

There are several types of Docker volumes, including:

Host-mounted volumes: These volumes are created on the host system and mounted into a container.

Docker-managed volumes: These volumes are created and managed by Docker itself.

Named volumes: These volumes are created with a specific name and can be shared between containers or between a container and the host.

Anonymous volumes: These volumes are created automatically by Docker and are used for temporary storage.

### 8.Explain what is /var/run/docker.sock ?
/var/run/docker.sock is basically the Unix socket the Docker daemon listens on by default

### 9.How to get low level information of Docker object ?
docker inspect is a command that returns detailed, low-level information on Docker objects. Those objects can be docker images, containers, networks, volumes, plugins, etc.

### 10.How to audit your docker image for vulnerabilities locally?
Vulnerability scanning for Docker local images allows developers and development teams to review the security state of the container images and take actions to fix issues identified during the scan, resulting in more secure deployments.

```
docker scan IMAGE_NAME
```

### 11. Difference between a Dockerfile and Docker Compose file?
A Dockerfile is a simple text file that contains the commands a you could call to assemble an image.
```
docker build -t name
```
Docker Compose is a tool that allows you to define and run multi-container Docker applications.
```
Docker-compose up
Docker-compose down
```

### 12. After successfully creating the docker command 'docker run ubuntu' the ubuntu container is getting killed automatically, can you tell us the reason why?
- This is the default behaviour of a container, when a process running inside a container exits the container is killed/exits automatically.
- If you want your container to run then make the ENTRYPOINT/CMD script run in the foreground without exiting.

```
docker images
docker inspect <image_name> | grep -i cmd (shows what is script running with cmd)
```
### 13. How do we run an NGINX web server on localhost:8088 using a Bridge Network?
```
docker run -it -d -p 8088:80 --name web_server nginx
```
Bridge network is default network on docker, verify using
```
docker inspect <container_name> | grep -i bridge
```

### 14.How to make container volume persistent? And how do we mount the volume on the localhost "root/nginx" directory? Not in default directory "/var/lib/volumes"
- We can achieve this by using a Bind mount option
- Bind mounts will mount a file or directory onto your container from your host machine
```
docker run --rm --name nginx_webserver --mount type=bind,source=/root/nginx,target=/usr/share/nginx -p 8080:80 -d nginx
```

### 15.Our company has restrictions on pulling/pushing from Docker or any cloud provider private/public registry, so how do i set up my own local Docker registry, if possible? Then how?
- This can achieved by running our own "Registry" container.
```
docker run -d -p 5000:5000 --name registry registry:2
docker image tag <image_name> localhost:5000/<image_name>
docker push localhost:5000/<image_name>
```

### 16. How to restrict memory and cpu limits when creating a container?
We can restrict memory of container can use by using -m option on docker run command
```
docker run -m 100m ubuntu
```
CPU limits
```
docker run --cpus="1"
```



