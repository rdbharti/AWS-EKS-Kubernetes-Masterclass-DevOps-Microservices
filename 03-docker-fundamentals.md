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