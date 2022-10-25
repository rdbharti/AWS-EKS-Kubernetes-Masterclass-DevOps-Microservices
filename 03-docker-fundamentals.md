# Docker Introduction
### What problems we have with Traditional Infrastructure?
- Needed to perform install/configs on every server/machine and every environment (dev/qa/staging/production)
- Issues related to libraries and dependecies keep arising and needed to be resolved. Which resulted in Compatibility and Dependency issues.
- Difficult to track changes across environments and they end-up with inconsistencies across environments.
- needed more resources to handle operational issues on day to day basis
    - server support
    - patching releases
- For new-developers it was time taking to provision his development environment in traditional approach.

### VMs vs Containers 
- Each containers has their own libraries, dependencies, image etc.

![Virtual Machine Vs Containers](https://k21academy.com/wp-content/uploads/2020/05/2020_05_13_12_19_07_PowerPoint_Slide_Show_Azure_AZ104_M01_Compute_ed1_-1536x701.png)

# Advantages of using Docker
- Flexible
    - Even the most complex applications can be containerazed
- LightWeight
    - Conatiners leverage and share the host kernal, making them more efficient in terms of system resources than virtual machines.
- Portable
    - You can build locally -> Deploy to Cloud -> Run anywhere
- Loosely Coupled
    - Containers are highly self suffcient and encapsulated, 
    allowing to replace/upgrade one without disrupting others.
- Scalable
    - Can increase and automatically distribute cconatiners replicas across a datacentre.
- Secure
    - Containers apply aggressive constrains and isolations to process without any configuration required on the part of the user.

# Docker Terminology 
- ## Docker Daemon
    - The Docker daemon (dockerd) listens for Docker API requests and manages Docker objects
such as images, containers, networks, and volumes.
- ## Docker Client
    - Docker client can be present on either Docker Host or any other machine.
    - The Docker client (docker) is the primary way that many Docker users interact with Docker.
    - When you use commands such as docker run, the client sends these commands to dockerd (Docker Daemon), which carries them out.
    - The docker command uses the Docker API.
    - The Docker client can communicate with more than one daemon.
- ## Docker Images
    - An image is a read-only template with instructions for creating a Docker container.
    - Often, an image is based on another image, with some additional customization.
    - For example, we may build an image which is based on the ubuntu image, but installs the
Apache web server and our application, as well as the configuration details needed to make
our application run.
- ## Docker Containers
    - A container is a runnable instance of an image.
    - We can create, start, stop, move, or delete a container using the Docker API or CLI.
    - We can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.
    - When a container is removed, any changes to its state that are not stored in persistent storage disappear.

- ## Docker Registry or Docker Hub
    - A Docker registry stores Docker images.
    - Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default.
    - We can even run our own private registry.
    - When we use the docker pull or docker run commands, the required images are pulled from our configured registry.
    - When we use the docker push command, our image is pushed to our configured registry.

    # Docker Installation
     - ## Windows 
        ```https://docs.docker.com/desktop/install/windows-install/```

     - ## AmznLinux2 
        ```markdown
            sudo yum update

            sudo yum install docker -y

            sudo systemctl enable docker

            sudo systemctl start docker
        ```

# Flow-1: Pull Docker Image from Docker Hub and Run it

## Step-1: Verify Docker version and also login to Docker Hub
```
docker version
docker login
```

## Step-2: Pull Image from Docker Hub
```
docker pull stacksimplify/dockerintro-springboot-helloworld-rest-api:1.0.0-RELEASE
```

## Step-3: Run the downloaded Docker Image & Access the Application
- Copy the docker image name from Docker Hub
```
docker run --name app1 -p 80:8080 -d stacksimplify/dockerintro-springboot-helloworld-rest-api:1.0.0-RELEASE
http://localhost/hello

# For Mac with Apple Chips (use different application)
Step-1: Install Docker with Apple Chips binary (https://docs.docker.com/desktop/mac/install/) on your mac machine

Step-2: Run the simple Nginx Application container. 
docker run --name kube1 -p 80:80 --platform linux/amd64 -d  stacksimplify/kubenginx:1.0.0
http://localhost

## Sample Output
kalyanreddy@Kalyans-Mac-mini-2 ~ % docker run --name kube1 -p 80:80 --platform linux/amd64 -d  stacksimplify/kubenginx:1.0.0
370f238d97556813a4978572d24983d6aaf80d4300828a57f27cda3d3d8f0fec
kalyanreddy@Kalyans-Mac-mini-2 ~ % curl http://localhost
<!DOCTYPE html>
<html>
   <body style="background-color:lightgoldenrodyellow;">
      <h1>Welcome to Stack Simplify</h1>
      <p>Kubernetes Fundamentals Demo</p>
      <p>Application Version: V1</p>
   </body>
</html>%
kalyanreddy@Kalyans-Mac-mini-2 ~ % 

```

## Step-4: List Running Containers
```
docker ps
docker ps -a
docker ps -a -q
```

## Step-5: Connect to Container Terminal
```
docker exec -it <container-name> /bin/sh
```

## Step-6: Container Stop, Start 
```
docker stop <container-name>
docker start  <container-name>
```

## Step-7: Remove Container 
```
docker stop <container-name> 
docker rm <container-name>
```

## Step-8: Remove Image
```
docker images
docker rmi  <image-id>
```

# Flow-2: Create a new Docker Image, Run as Container and Push to Docker Hub

## Pre-requisite Step
- Create your Docker hub account. 
- https://hub.docker.com/
- **Important Note**: In the below listed commands wherever you see **stacksimplify** you can replace with your docker hub account id. 


## Step-1: Run the base Nginx container
- Access the URL http://localhost
```
docker run --name mynginxdefault -p 80:80 -d nginx
docker ps
docker stop mynginxdefault
```

## Step-2: Create Dockerfile and copy our customized index.html
- **Dockerfile**
```
FROM nginx
COPY index.html /usr/share/nginx/html
```

## Step-3: Build Docker Image & run it
```
docker build -t stacksimplify/mynginx_image1:v1 .
docker run --name mynginx1 -p 80:80 -d stacksimplify/mynginx_image1:v1

Replace your docker hub account Id
docker build -t <your-docker-hub-id>/mynginx_image1:v1 .
docker run --name mynginx1 -p 80:80 -d <your-docker-hub-id>/mynginx_image1:v1
```

## Step-4: Tag & push the Docker image to docker hub
```
docker images
docker tag stacksimplify/mynginx_image1:v1 stacksimplify/mynginx_image1:v1-release
docker push stacksimplify/mynginx_image1:v1-release

Replace your docker hub account Id
docker tag <your-docker-hub-id>/mynginx_image1:v1 <your-docker-hub-id>/mynginx_image1:v1-release
docker push <your-docker-hub-id>/mynginx_image1:v1-release
```
## Step-5: Verify the same on docker hub
- Login to docker hub and verify the image we have pushed
- Url: https://hub.docker.com/repositories

# Docker - Essential Commands
- The below are the list of essential commands we are in need 

|     Commands                 |    Description                                  |
| ------------------------------- | --------------------------------------------- |
| docker ps | List all running containers |
| docker ps -a | List all containers stopped, running |
| docker stop container-id | Stop the container which is running |
| docker start container-id | Start the container which is stopped |
| docker restart container-id | Restart the container which is running |
| docker port container-id | List port mappings of a specific container |
| docker rm container-id or name | Remove the stopped container |
| docker rm -f container-id or name| Remove the running container forcefully |
| docker pull image-info | Pull the image from docker hub repository |
| docker pull stacksimplify/springboot-helloworld-rest-api:2.0.0-RELEASE | Pull the image from docker hub repository |
| docker exec -it container-name /bin/sh | Connect to linux container and execute commands in container |
| docker rmi image-id | Remove the docker image |
| docker logout | Logout from docker hub |
| docker login -u username -p password | Login to docker hub |
| docker stats | Display a live stream of container(s) resource usage statistics |
| docker top container-id or name | Display the running processes of a container |
| docker version | Show the Docker version information |
