*These are my quick notes before organizing* 

Deployed the Portainer docker container on Server 'Helix' 

Docker is a powerful platform designed to streamline the development, deployment, and running of applications through the use of containers.
### Core Concepts:

- **Containers**: Docker containers are lightweight, standalone executable packages that include everything needed to run a piece of software, including the code, runtime, system tools, libraries, and settings.
    
- **Images**: Docker images are read-only templates used to create containers. They contain the application and its dependencies and can be versioned and shared.
    
- **Dockerfile**: A Dockerfile is a script containing a series of instructions on how to build a Docker image. It automates the image creation process.
    
- **Docker Engine**: This is the runtime that builds, runs, and manages Docker containers on the host operating system.
    
- **Docker Hub**: A registry service where Docker images can be stored, shared, and accessed. It’s a central repository for finding and distributing container images.

Portainer is a web based management interface that simplifies the management of docker and/or other containerized environments

https://portainer.cameronxdx.net

![](attachments/Pasted%20image%2020250131121441.png)

![](attachments/Pasted%20image%2020250131121513.png)

`curl -fsSL https://get.docker.com -o get-docker.sh`
`sudo sh get-docker.sh`