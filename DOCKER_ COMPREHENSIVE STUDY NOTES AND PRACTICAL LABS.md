# DOCKER: COMPREHENSIVE STUDY NOTES AND PRACTICAL LABS

## A Beginner-to-Intermediate Guide to Docker, Containers and Containerization

---

# TABLE OF CONTENTS

1. Introduction to Docker
2. Why Docker?
3. Containers vs Virtual Machines
4. Docker Architecture
5. Installing Docker
6. Docker Images
7. Docker Containers
8. Essential Docker Commands
9. Running Your First Container
10. Port Mapping
11. Working Inside Containers
12. Docker Logs and Inspection
13. Dockerfile
14. Building Custom Docker Images
15. Docker Image Layers and Caching
16. Environment Variables
17. Docker Volumes
18. Bind Mounts
19. Docker Networking
20. Docker Compose
21. Multi-Container Applications
22. Docker Hub and Registries
23. Docker Security Best Practices
24. Docker Cleanup and Resource Management
25. Docker in AWS
26. Docker + CI/CD
27. Introduction to Kubernetes
28. Practical Labs
29. Final Capstone Project
30. Docker Command Cheat Sheet

---

# MODULE 1: INTRODUCTION TO DOCKER

## What is Docker?

Docker is an open-source platform that allows developers and system administrators to package, distribute, and run applications inside isolated environments called **containers**.

A Docker container packages an application together with everything it needs to run, including:

- Application code
- Runtime
- Libraries
- Dependencies
- Configuration files

This ensures that the application runs consistently regardless of where it is deployed.

For example:

An application may work perfectly on a developer's laptop but fail when deployed to a production server because of:

- Different operating systems
- Missing dependencies
- Different library versions
- Configuration differences

Docker solves this problem.

### The popular phrase is:

> "It works on my machine."

With Docker:

> "If the container works, it should work everywhere Docker is supported."

---

# MODULE 2: WHY DOCKER?

Before Docker, deploying applications could be complicated.

Imagine deploying a Python application.

You may need to install:

- Python
- Specific Python version
- Required libraries
- Dependencies
- Web server
- Environment variables

Another developer may install a slightly different version, causing problems.

Docker packages everything together.

```text
Traditional Deployment

Application
     ↓
Install Dependencies
     ↓
Configure Server
     ↓
Install Runtime
     ↓
Deploy Application
     ↓
Hope Everything Works 😂


Docker Deployment

Application + Dependencies
          ↓
     Docker Image
          ↓
     Docker Container
          ↓
      Application Runs
```

---

# MODULE 3: CONTAINERS VS VIRTUAL MACHINES

This is one of the most important Docker concepts.

## Virtual Machines

Virtual machines simulate complete computers.

Each VM contains:

```text
Application
Dependencies
Guest Operating System
Hypervisor
Host Operating System
Physical Hardware
```

Examples of hypervisors:

- VMware
- VirtualBox
- Hyper-V

Each virtual machine requires its own operating system.

This consumes significant:

- Memory
- CPU
- Storage

---

## Docker Containers

Containers share the host operating system kernel.

```text
Application A      Application B
     ↓                  ↓
Container A        Container B
     ↓                  ↓
Docker Engine
        ↓
Host Operating System
        ↓
Hardware
```

### Advantages of Containers

- Lightweight
- Faster startup
- Portable
- Efficient
- Easy to scale
- Consistent environments

---

## Comparison

| Feature | Virtual Machine | Docker Container |
|---|---|---|
| Operating System | Separate OS | Shares Host Kernel |
| Startup Time | Minutes | Seconds |
| Size | GBs | MBs |
| Resource Usage | High | Low |
| Portability | Moderate | High |
| Isolation | Strong | Lightweight Isolation |

---

# MODULE 4: DOCKER ARCHITECTURE

Docker consists of several important components.

## 1. Docker Client

The Docker client is what you interact with when running commands.

Example:

```bash
docker run nginx
```

The Docker client sends your command to the Docker daemon.

---

## 2. Docker Daemon

The Docker daemon is responsible for:

- Building images
- Running containers
- Managing networks
- Managing volumes

The daemon runs in the background.

---

## 3. Docker Images

An image is a blueprint used to create containers.

Think of it as:

> A class in programming.

---

## 4. Docker Containers

A container is a running instance of an image.

Think of it as:

> An object created from a class.

Example:

```text
NGINX IMAGE
     ↓
     ↓ Create Container
     ↓
NGINX CONTAINER
```

---

## 5. Docker Registry

A registry stores Docker images.

The most popular public registry is Docker Hub.

Examples include:

- Docker Hub
- Amazon ECR
- GitHub Container Registry
- Google Artifact Registry

---

# MODULE 5: INSTALLING DOCKER

After installing Docker, verify the installation.

```bash
docker --version
```

You can also check detailed information.

```bash
docker info
```

To confirm Docker is working:

```bash
docker run hello-world
```

Docker will:

1. Search for the image locally.
2. Download it if unavailable.
3. Create a container.
4. Run the container.
5. Display a message.

---

# PRACTICAL LAB 1: RUN YOUR FIRST CONTAINER

## Objective

Run your first Docker container.

### Step 1

Check Docker version.

```bash
docker --version
```

### Step 2

Run Hello World.

```bash
docker run hello-world
```

### Expected Result

You should see a message confirming that Docker is working correctly.

### What Happened?

```text
Docker Client
      ↓
Docker Daemon
      ↓
Check Local Image
      ↓
Image Not Found
      ↓
Pull from Docker Hub
      ↓
Create Container
      ↓
Run Container
```

---

# MODULE 6: DOCKER IMAGES

A Docker image is a read-only template used to create containers.

Examples:

- nginx
- ubuntu
- mysql
- redis
- python
- node

List images:

```bash
docker images
```

or:

```bash
docker image ls
```

---

## Pulling an Image

Download an image from Docker Hub.

```bash
docker pull nginx
```

Check downloaded images:

```bash
docker images
```

---

## Pulling Specific Versions

Instead of downloading the latest version:

```bash
docker pull nginx:latest
```

You can specify a version:

```bash
docker pull nginx:1.25
```

This is called **tagging**.

---

## Removing Images

Remove an image:

```bash
docker rmi nginx
```

Or:

```bash
docker image rm nginx
```

---

# PRACTICAL LAB 2: WORKING WITH IMAGES

### Step 1: Pull Ubuntu

```bash
docker pull ubuntu
```

### Step 2: List Images

```bash
docker images
```

### Step 3: Pull Nginx

```bash
docker pull nginx
```

### Step 4: Inspect Image

```bash
docker image inspect nginx
```

### Step 5: Remove an Unused Image

```bash
docker image rm ubuntu
```

---

# MODULE 7: DOCKER CONTAINERS

A container is a running instance of an image.

Example:

```text
IMAGE = Blueprint

CONTAINER = Running Application
```

Run Nginx:

```bash
docker run nginx
```

However, this runs in the foreground.

To run in detached mode:

```bash
docker run -d nginx
```

The `-d` means:

> Detached Mode

The container runs in the background.

---

## Listing Running Containers

```bash
docker ps
```

To see all containers:

```bash
docker ps -a
```

---

## Stopping Containers

```bash
docker stop <container_id>
```

Example:

```bash
docker stop 4a1234567890
```

You can also use container names.

```bash
docker stop mycontainer
```

---

## Starting Containers

```bash
docker start <container_id>
```

---

## Restarting Containers

```bash
docker restart <container_id>
```

---

## Removing Containers

```bash
docker rm <container_id>
```

To forcefully remove a running container:

```bash
docker rm -f <container_id>
```

---

# PRACTICAL LAB 3: CONTAINER MANAGEMENT

### Step 1

Run Nginx.

```bash
docker run -d nginx
```

### Step 2

Check running containers.

```bash
docker ps
```

### Step 3

Stop the container.

```bash
docker stop <container_id>
```

### Step 4

Check all containers.

```bash
docker ps -a
```

### Step 5

Start it again.

```bash
docker start <container_id>
```

### Step 6

Remove it.

```bash
docker rm -f <container_id>
```

---

# MODULE 8: NAMING CONTAINERS

Docker automatically generates funny names like:

```text
happy_turing
kind_pasteur
angry_einstein
```

You can give containers meaningful names.

```bash
docker run -d --name my-nginx nginx
```

Check:

```bash
docker ps
```

Now you can manage it easily.

```bash
docker stop my-nginx
```

Instead of using a long container ID.

---

# MODULE 9: PORT MAPPING

Containers run in isolated environments.

For example, Nginx normally listens on port 80.

But the container's port is not automatically accessible from your computer.

We use **port mapping**.

Syntax:

```bash
docker run -p HOST_PORT:CONTAINER_PORT IMAGE
```

Example:

```bash
docker run -d -p 8080:80 nginx
```

Meaning:

```text
Your Computer
Port 8080
     ↓
Docker Container
Port 80
     ↓
Nginx
```

Now open:

```text
http://localhost:8080
```

You should see the Nginx welcome page.

---

# PRACTICAL LAB 4: PORT MAPPING

Run:

```bash
docker run -d --name nginx-server -p 8080:80 nginx
```

Check:

```bash
docker ps
```

Open your browser:

```text
http://localhost:8080
```

You should see:

> Welcome to nginx!

Congratulations! You have deployed a web server using Docker.

---

# MODULE 10: WORKING INSIDE A CONTAINER

Sometimes you need to enter a running container.

Use:

```bash
docker exec -it <container_name> bash
```

Example:

```bash
docker exec -it nginx-server bash
```

You are now inside the container.

Try:

```bash
ls
```

Check your current directory:

```bash
pwd
```

Exit:

```bash
exit
```

---

## Important Commands

Execute a command inside a container:

```bash
docker exec nginx-server ls
```

Interactive shell:

```bash
docker exec -it nginx-server bash
```

Some containers use `sh`.

```bash
docker exec -it nginx-server sh
```

---

# PRACTICAL LAB 5: ENTER A CONTAINER

Run:

```bash
docker run -d --name ubuntu-container ubuntu sleep infinity
```

Enter the container:

```bash
docker exec -it ubuntu-container bash
```

Inside the container:

```bash
pwd
```

```bash
ls
```

Check OS information:

```bash
cat /etc/os-release
```

Exit:

```bash
exit
```

---

# MODULE 11: DOCKER LOGS

Containers generate logs.

View logs:

```bash
docker logs <container_name>
```

Example:

```bash
docker logs nginx-server
```

Follow logs in real-time:

```bash
docker logs -f nginx-server
```

This is useful for troubleshooting.

---

# MODULE 12: DOCKER INSPECT

Docker inspect provides detailed information about Docker objects.

Example:

```bash
docker inspect nginx-server
```

You can inspect:

- Containers
- Images
- Networks
- Volumes

Information includes:

- IP address
- Ports
- Mounts
- Environment variables
- Network configuration

---

# PRACTICAL LAB 6: LOGS AND INSPECTION

Run:

```bash
docker logs nginx-server
```

Inspect:

```bash
docker inspect nginx-server
```

Try finding:

- Container IP address
- Port mapping
- Container creation time
- Network information

---

# MODULE 13: DOCKERFILE

A Dockerfile is a text file containing instructions for building a Docker image.

Example:

```dockerfile
FROM nginx

COPY index.html /usr/share/nginx/html/index.html
```

Docker reads the instructions and builds an image.

---

# IMPORTANT DOCKERFILE INSTRUCTIONS

## FROM

Defines the base image.

```dockerfile
FROM ubuntu
```

---

## RUN

Executes commands during image creation.

```dockerfile
RUN apt update
```

Example:

```dockerfile
RUN apt update && apt install -y nginx
```

---

## COPY

Copies files from your computer into the image.

```dockerfile
COPY . /app
```

---

## WORKDIR

Sets the working directory.

```dockerfile
WORKDIR /app
```

---

## EXPOSE

Documents the port used by the application.

```dockerfile
EXPOSE 80
```

---

## ENV

Sets environment variables.

```dockerfile
ENV APP_ENV=production
```

---

## CMD

Defines the default command when a container starts.

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

---

## ENTRYPOINT

Defines the main executable.

```dockerfile
ENTRYPOINT ["python"]
```

---

# MODULE 14: BUILDING YOUR FIRST CUSTOM IMAGE

Let's create a custom website.

Create a folder:

```bash
mkdir docker-nginx-project
```

Enter it:

```bash
cd docker-nginx-project
```

Create:

```text
Dockerfile
index.html
```

---

## index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Docker Website</title>
</head>
<body>
    <h1>Hello Boss! 🚀</h1>
    <p>This website is running inside a Docker container.</p>
</body>
</html>
```

---

## Dockerfile

```dockerfile
FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

---

## Build the Image

```bash
docker build -t my-docker-website .
```

The dot `.` means:

> Use the current directory as the build context.

Check:

```bash
docker images
```

---

## Run the Container

```bash
docker run -d -p 8080:80 my-docker-website
```

Open:

```text
http://localhost:8080
```

Your custom website should appear.

---

# PRACTICAL LAB 7: CUSTOM NGINX WEBSITE

## Objective

Build and deploy your own Docker image.

### Tasks

1. Create project directory.
2. Create HTML page.
3. Create Dockerfile.
4. Build image.
5. Run container.
6. Access application through browser.

---

# MODULE 15: DOCKER IMAGE LAYERS

Docker images are built in layers.

Example:

```dockerfile
FROM ubuntu

RUN apt update

RUN apt install -y python

COPY app.py /app/

CMD ["python", "/app/app.py"]
```

Each instruction creates a layer.

```text
Layer 1 → Ubuntu
Layer 2 → apt update
Layer 3 → Python installation
Layer 4 → Application copy
Layer 5 → CMD
```

Docker caches layers.

This makes builds faster.

---

## Dockerfile Optimization

Bad:

```dockerfile
COPY . .

RUN pip install -r requirements.txt
```

Better:

```dockerfile
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

Why?

If your application code changes but `requirements.txt` doesn't, Docker can reuse the dependency layer.

---

# MODULE 16: ENVIRONMENT VARIABLES

Environment variables allow applications to receive configuration dynamically.

Example:

```bash
docker run -e APP_ENV=production nginx
```

Check environment variables:

```bash
docker exec nginx-server env
```

---

# PRACTICAL LAB 8: ENVIRONMENT VARIABLES

Run:

```bash
docker run -it --rm -e NAME=Victor ubuntu bash
```

Inside:

```bash
echo $NAME
```

Expected output:

```text
Victor
```

---

# MODULE 17: DOCKER VOLUMES

Containers are temporary.

Suppose you install MySQL in a container and create a database.

If the container is deleted:

```bash
docker rm database-container
```

Your data may disappear.

Volumes solve this problem.

---

## Creating a Volume

```bash
docker volume create mydata
```

List volumes:

```bash
docker volume ls
```

Inspect:

```bash
docker volume inspect mydata
```

---

## Using a Volume

```bash
docker run -v mydata:/data ubuntu
```

Architecture:

```text
Docker Container
       │
       │
       ↓
Docker Volume
       │
       ↓
Persistent Data
```

Even if the container is removed, the volume can remain.

---

# PRACTICAL LAB 9: DATA PERSISTENCE

Create a volume:

```bash
docker volume create test-volume
```

Run Ubuntu:

```bash
docker run -it --name volume-test -v test-volume:/data ubuntu bash
```

Inside:

```bash
echo "Docker Persistent Storage" > /data/test.txt
```

Exit:

```bash
exit
```

Remove container:

```bash
docker rm volume-test
```

Create another container using the same volume:

```bash
docker run -it -v test-volume:/data ubuntu bash
```

Check:

```bash
cat /data/test.txt
```

Your file should still exist.

---

# MODULE 18: BIND MOUNTS

Bind mounts connect a directory on your computer directly to a container.

Example:

```bash
docker run -v $(pwd):/app ubuntu
```

Architecture:

```text
Your Computer
/project
     │
     │ Bind Mount
     ↓
Docker Container
/app
```

Changes made on your computer can immediately appear inside the container.

---

# PRACTICAL LAB 10: BIND MOUNTS

Create:

```bash
mkdir bind-mount-test
```

Enter:

```bash
cd bind-mount-test
```

Create:

```bash
echo "Hello Docker" > test.txt
```

Run:

```bash
docker run -it -v $(pwd):/data ubuntu bash
```

Inside:

```bash
cat /data/test.txt
```

---

# MODULE 19: DOCKER NETWORKING

Containers often need to communicate.

Example:

```text
Frontend
    ↓
Backend API
    ↓
Database
```

Docker networking allows this communication.

---

## Types of Docker Networks

### Bridge Network

Default network for containers.

```text
Container A
     ↓
Docker Bridge Network
     ↓
Container B
```

---

### Host Network

Container uses the host's network directly.

---

### None Network

Container has no network access.

---

## List Networks

```bash
docker network ls
```

Inspect:

```bash
docker network inspect bridge
```

---

## Create Custom Network

```bash
docker network create app-network
```

Run containers:

```bash
docker run -d --name database --network app-network mysql
```

```bash
docker run -d --name backend --network app-network my-backend
```

Containers can communicate using names.

Example:

```text
backend → database
```

---

# PRACTICAL LAB 11: CONTAINER NETWORKING

Create network:

```bash
docker network create my-network
```

Run first container:

```bash
docker run -dit --name container1 --network my-network ubuntu
```

Run second container:

```bash
docker run -dit --name container2 --network my-network ubuntu
```

Enter container1:

```bash
docker exec -it container1 bash
```

Install ping:

```bash
apt update
apt install -y iputils-ping
```

Ping container2:

```bash
ping container2
```

If successful, congratulations.

Your containers are communicating.

---

# MODULE 20: DOCKER COMPOSE 🔥

Docker Compose allows us to manage multiple containers using one configuration file.

Imagine:

```text
Frontend
Backend
Database
Redis
```

Instead of running multiple commands, we use:

```text
compose.yaml
```

---

## Example

```yaml
services:

  web:
    image: nginx
    ports:
      - "8080:80"

  database:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
```

Start:

```bash
docker compose up
```

Run in background:

```bash
docker compose up -d
```

Stop:

```bash
docker compose down
```

---

# PRACTICAL LAB 12: YOUR FIRST DOCKER COMPOSE PROJECT

Create directory:

```bash
mkdir docker-compose-project
```

Enter:

```bash
cd docker-compose-project
```

Create:

```text
compose.yaml
```

Add:

```yaml
services:
  nginx:
    image: nginx
    ports:
      - "8080:80"
```

Run:

```bash
docker compose up -d
```

Check:

```bash
docker ps
```

Open:

```text
http://localhost:8080
```

Stop everything:

```bash
docker compose down
```

---

# MODULE 21: MULTI-CONTAINER APPLICATIONS

Real-world applications usually consist of multiple services.

Example:

```text
                    INTERNET
                       │
                       ↓
                  Load Balancer
                       │
                       ↓
                  Frontend
                       │
                       ↓
                   Backend API
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
       Database                  Redis
```

With Docker Compose:

```text
compose.yaml
       │
       ├── Frontend Container
       │
       ├── Backend Container
       │
       ├── Database Container
       │
       └── Redis Container
```

---

# MODULE 22: DOCKER HUB

Docker Hub is an online registry containing Docker images.

Workflow:

```text
Developer
    ↓
Build Docker Image
    ↓
Tag Image
    ↓
Push to Docker Hub
    ↓
Other Server Pulls Image
    ↓
Runs Container
```

---

## Login

```bash
docker login
```

---

## Tag Image

```bash
docker tag my-app username/my-app:v1
```

---

## Push

```bash
docker push username/my-app:v1
```

---

# PRACTICAL LAB 13: PUSH IMAGE TO DOCKER HUB

### Step 1

Login:

```bash
docker login
```

### Step 2

Tag image:

```bash
docker tag my-docker-website YOUR_USERNAME/my-docker-website:v1
```

### Step 3

Push:

```bash
docker push YOUR_USERNAME/my-docker-website:v1
```

### Step 4

Test on another machine:

```bash
docker pull YOUR_USERNAME/my-docker-website:v1
```

---

# MODULE 23: DOCKER SECURITY

Containers should not automatically be considered secure.

Important practices include:

## 1. Use Official Images

Prefer:

```dockerfile
FROM nginx:latest
```

From trusted registries.

---

## 2. Avoid Running as Root

Create a user:

```dockerfile
RUN useradd -m appuser

USER appuser
```

---

## 3. Use Small Images

Instead of:

```dockerfile
FROM ubuntu
```

Sometimes use:

```dockerfile
FROM alpine
```

Smaller images reduce the attack surface.

---

## 4. Do Not Store Secrets in Images

Avoid:

```dockerfile
ENV PASSWORD=mysecretpassword
```

Use:

- Environment variables
- Docker secrets
- AWS Secrets Manager

---

# MODULE 24: DOCKER CLEANUP

Docker can consume significant disk space.

Check Docker disk usage:

```bash
docker system df
```

Remove stopped containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

Remove unused volumes:

```bash
docker volume prune
```

Clean unused resources:

```bash
docker system prune
```

⚠️ Be careful with:

```bash
docker system prune -a
```

This can remove unused images.

---

# MODULE 25: DOCKER IN AWS

This is where Docker connects directly to your Cloud Engineering journey.

Major AWS services include:

## Amazon ECR

Amazon Elastic Container Registry.

Stores Docker images.

```text
Docker Build
     ↓
Docker Image
     ↓
Amazon ECR
```

---

## Amazon ECS

Elastic Container Service.

Used to orchestrate containers.

```text
Docker Image
      ↓
Amazon ECR
      ↓
Amazon ECS
      ↓
Running Containers
```

---

## AWS Fargate

Allows you to run containers without managing servers.

Instead of:

```text
Manage EC2
Install Docker
Configure Server
Run Container
```

You simply provide the container.

AWS handles infrastructure.

---

## Amazon EKS

Elastic Kubernetes Service.

Managed Kubernetes on AWS.

Used for large-scale container orchestration.

---

# MODULE 26: DOCKER + CI/CD

Docker is heavily used in DevOps pipelines.

Typical workflow:

```text
Developer
    ↓
Git Push
    ↓
GitHub
    ↓
CI/CD Pipeline
    ↓
Run Tests
    ↓
Build Docker Image
    ↓
Push Image to Registry
    ↓
Deploy Container
```

Tools include:

- GitHub Actions
- Jenkins
- GitLab CI/CD
- AWS CodePipeline

---

# MODULE 27: DOCKER VS KUBERNETES

Docker and Kubernetes work together.

## Docker

Docker helps you:

- Build containers
- Package applications
- Run containers

## Kubernetes

Kubernetes helps you:

- Manage many containers
- Scale containers
- Restart failed containers
- Load balance applications
- Deploy updates

Think of it like this:

```text
Docker = Creates Cars 🚗

Kubernetes = Manages Thousands of Cars 🚦
```

---

# COMPLETE DOCKER PRACTICAL ROADMAP

## LEVEL 1: BEGINNER

### Lab 1
Run Hello World.

### Lab 2
Pull and manage Docker images.

### Lab 3
Create and manage containers.

### Lab 4
Deploy Nginx.

### Lab 5
Practice port mapping.

### Lab 6
Enter containers.

### Lab 7
Check logs and inspect containers.

---

# LEVEL 2: INTERMEDIATE

### Lab 8
Create your first Dockerfile.

### Lab 9
Build a custom Nginx website.

### Lab 10
Work with environment variables.

### Lab 11
Create Docker volumes.

### Lab 12
Practice bind mounts.

### Lab 13
Create Docker networks.

### Lab 14
Connect multiple containers.

---

# LEVEL 3: ADVANCED

### Lab 15
Build multi-container applications.

### Lab 16
Use Docker Compose.

### Lab 17
Deploy a frontend + backend + database.

### Lab 18
Push images to Docker Hub.

### Lab 19
Deploy Docker containers to AWS EC2.

### Lab 20
Push Docker images to Amazon ECR.

### Lab 21
Deploy containers using Amazon ECS.

### Lab 22
Create CI/CD pipeline.

---

# FINAL CAPSTONE PROJECT 🔥

## PROJECT: CONTAINERIZED WEB APPLICATION

Your final project should combine everything.

### Architecture

```text
                    USER
                     │
                     ↓
                  INTERNET
                     │
                     ↓
                   NGINX
                Container
                     │
                     ↓
                 BACKEND
                Container
                     │
                     ↓
                  MYSQL
                Container
                     │
                     ↓
               Docker Volume
```

---

## Your Tasks

### Phase 1

Create:

- Frontend
- Backend
- Database

---

### Phase 2

Containerize each application.

Create:

```text
frontend/Dockerfile

backend/Dockerfile

compose.yaml
```

---

### Phase 3

Create Docker network.

```bash
docker network create application-network
```

---

### Phase 4

Create persistent database storage.

```bash
docker volume create database-volume
```

---

### Phase 5

Deploy using Docker Compose.

```bash
docker compose up -d
```

---

### Phase 6

Push images to Docker Hub.

---

### Phase 7

Deploy the application to AWS.

Possible architecture:

```text
Developer
    │
    ↓
GitHub
    │
    ↓
GitHub Actions
    │
    ↓
Build Docker Image
    │
    ↓
Amazon ECR
    │
    ↓
Amazon ECS
    │
    ↓
AWS Fargate
```

---

# DOCKER COMMAND CHEAT SHEET

## Docker Information

```bash
docker --version
docker info
docker help
```

---

## Images

```bash
docker images
docker image ls
docker pull nginx
docker image inspect nginx
docker image rm nginx
```

---

## Containers

```bash
docker ps
docker ps -a

docker run nginx

docker run -d nginx

docker stop <container>

docker start <container>

docker restart <container>

docker rm <container>

docker rm -f <container>
```

---

## Container Names

```bash
docker run --name my-container nginx
```

---

## Port Mapping

```bash
docker run -p 8080:80 nginx
```

---

## Interactive Containers

```bash
docker run -it ubuntu bash
```

---

## Execute Commands

```bash
docker exec -it <container> bash
```

---

## Logs

```bash
docker logs <container>

docker logs -f <container>
```

---

## Dockerfile

```bash
docker build -t my-image .
```

---

## Volumes

```bash
docker volume create myvolume

docker volume ls

docker volume inspect myvolume

docker volume rm myvolume
```

---

## Networks

```bash
docker network ls

docker network create mynetwork

docker network inspect mynetwork
```

---

## Docker Compose

```bash
docker compose up

docker compose up -d

docker compose down

docker compose ps

docker compose logs
```

---

# RECOMMENDED STUDY STRATEGY

## Week 1 — Docker Fundamentals

- Docker introduction
- Images
- Containers
- Docker commands
- Port mapping

### Practical Goal:

Deploy Nginx.

---

## Week 2 — Dockerfile

- Dockerfile instructions
- Build images
- COPY
- RUN
- CMD
- ENTRYPOINT

### Practical Goal:

Build and deploy a custom website.

---

## Week 3 — Storage and Networking

- Volumes
- Bind mounts
- Networks
- Container communication

### Practical Goal:

Create containers that communicate.

---

## Week 4 — Docker Compose

- YAML
- Multi-container applications
- Environment variables
- Database containers

### Practical Goal:

Deploy a complete multi-container application.

---

## Week 5 — Cloud Integration

- Docker Hub
- Amazon ECR
- Docker on EC2
- Amazon ECS
- AWS Fargate

### Practical Goal:

Deploy a Docker application to AWS.

---

# FINAL WORD

The goal should not be to memorize 100 Docker commands.

Your focus should be understanding this flow:

```text
APPLICATION CODE
       ↓
DOCKERFILE
       ↓
DOCKER IMAGE
       ↓
DOCKER CONTAINER
       ↓
DOCKER REGISTRY
       ↓
AWS ECR
       ↓
AWS ECS / FARGATE
       ↓
PRODUCTION APPLICATION
```

Once you understand that flow, Docker becomes much easier.

Remember:

> **Dockerfile → builds an Image**
>
> **Image → creates a Container**
>
> **Container → runs the Application**
>
> **Registry → stores the Image**
>
> **Orchestrator → manages Containers at scale**

---

# YOUR DOCKER LEARNING CHECKLIST

- [ ] Understand Containers vs Virtual Machines
- [ ] Install Docker
- [ ] Run Hello World
- [ ] Pull Images
- [ ] Manage Images
- [ ] Create Containers
- [ ] Manage Containers
- [ ] Understand Port Mapping
- [ ] Enter Containers
- [ ] View Logs
- [ ] Inspect Containers
- [ ] Create Dockerfile
- [ ] Build Custom Images
- [ ] Understand Image Layers
- [ ] Use Environment Variables
- [ ] Create Volumes
- [ ] Use Bind Mounts
- [ ] Create Networks
- [ ] Connect Containers
- [ ] Learn Docker Compose
- [ ] Build Multi-Container Applications
- [ ] Push Images to Docker Hub
- [ ] Learn Docker Security
- [ ] Deploy Docker on AWS
- [ ] Learn Amazon ECR
- [ ] Learn Amazon ECS
- [ ] Learn AWS Fargate
- [ ] Integrate Docker with CI/CD
- [ ] Prepare for Kubernetes

## END OF DOCKER STUDY NOTES