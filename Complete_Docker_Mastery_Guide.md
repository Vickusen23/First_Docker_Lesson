# Complete Docker Mastery Guide
## From Fundamentals to Production-Ready Containers

---

## Table of Contents
1. [Introduction to Docker & Containerization](#1-introduction-to-docker--containerization)
2. [Docker Architecture](#2-docker-architecture)
3. [Core Concepts: Images, Containers & Registries](#3-core-concepts-images-containers--registries)
4. [The Dockerfile Deep Dive](#4-the-dockerfile-deep-dive)
5. [Essential Docker Commands](#5-essential-docker-commands)
6. [Docker Networking](#6-docker-networking)
7. [Docker Volumes & Persistent Storage](#7-docker-volumes--persistent-storage)
8. [Docker Compose: Multi-Container Applications](#8-docker-compose-multi-container-applications)
9. [Production Best Practices](#9-production-best-practices)
10. [Complete Workflow Example](#10-complete-workflow-example)

---

## 1. Introduction to Docker & Containerization

### What Problem Does Docker Solve?

**The "It Works on My Machine" Problem**

Before containers, developers faced a consistent challenge: applications that ran perfectly on a developer's laptop would fail in production due to differences in:
- Operating system versions
- Missing dependencies
- Environment variables
- Library versions
- System configurations

**Virtual Machines vs. Containers**

| Aspect | Virtual Machines | Docker Containers |
|--------|------------------|-------------------|
| **Size** | Gigabytes (full OS) | Megabytes (shared OS kernel) |
| **Boot Time** | Minutes | Seconds |
| **Performance** | Hardware emulation overhead | Near-native performance |
| **Isolation** | Strong (hardware-level) | Process-level |
| **Resource Usage** | Heavy (dedicated RAM/CPU) | Lightweight (shared resources) |
| **Portability** | Limited | Highly portable |

**Containers** package an application with all its dependencies into a single, portable unit that runs consistently across any environment.

### What is Docker?

Docker is an open-source platform that automates the deployment, scaling, and management of applications using **containerization**. It allows you to:
- Package applications with all dependencies
- Ship them as standardized units
- Run them consistently anywhere

---

## 2. Docker Architecture

Docker uses a **client-server architecture** consisting of three main components:

```
┌─────────────────────────────────────────────────────────────┐
│                      Docker Client                           │
│                   (docker commands)                          │
└───────────────────────┬─────────────────────────────────────┘
                        │ REST API
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                   Docker Daemon (dockerd)                    │
│              (manages images, containers, networks)          │
└───────────────────────┬─────────────────────────────────────┘
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│    Images    │ │  Containers  │ │   Volumes    │
│  (Templates) │ │  (Instances) │ │   (Storage)  │
└──────────────┘ └──────────────┘ └──────────────┘
```

### Key Components

**Docker Client**
- The command-line interface (`docker` CLI)
- Sends commands to the Docker daemon
- Can run on the same machine or connect remotely

**Docker Daemon (`dockerd`)**
- The background service that manages Docker objects
- Listens for API requests
- Handles building, running, and distributing containers

**Docker Registry**
- Storage and distribution system for Docker images
- **Docker Hub** is the default public registry
- Organizations often use private registries (AWS ECR, Azure ACR, GitHub Packages)

---

## 3. Core Concepts: Images, Containers & Registries

### Docker Images

An **image** is a read-only template containing:
- A stripped-down operating system
- Application code
- Runtime environment
- System libraries
- Dependencies and configurations

**Key Characteristics:**
- Images are **layered** (each instruction in a Dockerfile creates a new layer)
- Layers are cached and shared between images
- Images are immutable

**Image Naming Convention:**
```
[registry/]name[:tag]
```
Examples:
- `ubuntu:latest` — Official Ubuntu image, latest tag
- `python:3.12-slim` — Python 3.12 slim variant
- `myregistry.com/myapp:v1.0` — Custom registry image

### Docker Containers

A **container** is a runnable instance of an image. Think of it as:
- **Image** = Class (blueprint)
- **Container** = Object (running instance)

**Container Lifecycle:**
```
    CREATED
       │
       ▼
    ┌──────┐
    │ RUN  │◄──── docker start
    └──────┘
       │
       │ docker stop
       ▼
    ┌──────┐
    │EXITED│
    └──────┘
       │
       │ docker rm
       ▼
    DELETED
```

### Docker Registries

**Docker Hub** (hub.docker.com)
- Default public registry
- Contains official images (verified, secure)
- Community images (use with caution)

**Pulling Images:**
```bash
docker pull ubuntu:latest
docker pull nginx:alpine
docker pull python:3.12-slim
```

---

## 4. The Dockerfile Deep Dive

### What is a Dockerfile?

A Dockerfile is a text file containing instructions to build a Docker image. Docker reads it **sequentially from top to bottom**.

### The Complete Workflow

```
Dockerfile
    │
    │ docker build
    ▼
Docker Image
    │
    │ docker run
    ▼
Docker Container
    │
    │ docker push
    ▼
Docker Registry
```

### Essential Dockerfile Instructions

#### `FROM` — Select a Base Image

Every Dockerfile must start with `FROM`. It sets the foundation for your image.

```dockerfile
FROM ubuntu:22.04
FROM python:3.12-slim
FROM node:20-alpine
FROM nginx:alpine
```

**Best Practice:** Use specific versions, not `latest`, for reproducible builds.

#### `WORKDIR` — Set the Working Directory

Sets the current working directory inside the container. If it doesn't exist, Docker creates it.

```dockerfile
WORKDIR /app
```

**Why use WORKDIR instead of `cd`?**
- Cleaner and more explicit
- Used as the base for subsequent relative paths
- Creates the directory if missing

#### `RUN` — Execute Commands During Build

Executes commands while building the image. Results are committed to the image layer.

```dockerfile
RUN apt-get update && apt-get install -y nginx
RUN pip install -r requirements.txt
RUN npm install
```

**Critical Rule:** Chain commands with `&&` to reduce layers:
```dockerfile
# GOOD — Single layer
RUN apt-get update && apt-get install -y \
    curl \
    vim \
    git \
    && rm -rf /var/lib/apt/lists/*

# BAD — Multiple layers (bloating image)
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y vim
```

#### `COPY` vs `ADD` — Copy Files into the Image

**COPY** — Simple, predictable file copying:
```dockerfile
COPY . /app
COPY index.html /usr/share/nginx/html/
COPY ./src /app/src
```

**ADD** — Has additional capabilities (use sparingly):
```dockerfile
ADD https://example.com/file.tar.gz /app/
ADD archive.tar.gz /app/  # Auto-extracts tar files
```

**Best Practice:** Prefer `COPY` unless you specifically need `ADD`'s extra features.

#### `ENV` — Set Environment Variables

```dockerfile
ENV NODE_ENV=production
ENV PORT=8080
ENV DB_HOST=localhost
```

These variables are available both during build and at runtime.

#### `EXPOSE` — Document Listening Ports

```dockerfile
EXPOSE 80
EXPOSE 8080/tcp
EXPOSE 5432/udp
```

**Important:** `EXPOSE` only documents ports. It does NOT publish them. You still need `-p` when running.

#### `CMD` vs `ENTRYPOINT` — Container Startup Commands

**CMD** — Specifies the default command (can be overridden):
```dockerfile
CMD ["nginx", "-g", "daemon off;"]
CMD ["python", "app.py"]
```

**ENTRYPOINT** — Specifies the main executable (harder to override):
```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

**When to use which:**
- Use `CMD` for standalone applications
- Use `ENTRYPOINT` for utility containers that take arguments
- Combine both for flexible defaults

#### `.dockerignore` — Exclude Files from Build Context

Create a `.dockerignore` file to prevent unnecessary files from being sent to the daemon:

```
node_modules
.git
.env
*.log
Dockerfile
.dockerignore
```

### Complete Dockerfile Example: Python Flask App

```dockerfile
# Stage 1: Use a slim Python base image
FROM python:3.12-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Set working directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Copy and install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose the application port
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]
```

### Multi-Stage Builds (Production Optimization)

Multi-stage builds allow you to use multiple `FROM` statements, copying only what you need into the final image:

```dockerfile
# Stage 1: Build environment
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production environment (much smaller)
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**Benefits:**
- Final image contains only production artifacts
- Build tools and dependencies are excluded
- Significantly smaller image size
- Improved security (smaller attack surface)

---

## 5. Essential Docker Commands

### Image Management

```bash
# Pull an image from a registry
docker pull ubuntu:22.04

# List local images
docker images

# Build an image from a Dockerfile
docker build -t myapp:1.0 .

# Tag an image
docker tag myapp:1.0 myregistry.com/myapp:1.0

# Push an image to a registry
docker push myregistry.com/myapp:1.0

# Remove an image
docker rmi myapp:1.0

# Remove all unused images
docker image prune -a
```

### Container Lifecycle

```bash
# Run a container
docker run -d -p 8080:80 --name my-container nginx:alpine

# Run with interactive terminal
docker run -it ubuntu:22.04 /bin/bash

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Start a stopped container
docker start my-container

# Stop a running container
docker stop my-container

# Restart a container
docker restart my-container

# Remove a container
docker rm my-container

# Force remove a running container
docker rm -f my-container

# Remove all stopped containers
docker container prune
```

### Container Inspection & Debugging

```bash
# View container logs
docker logs my-container
docker logs -f my-container  # Follow mode

# Execute a command inside a running container
docker exec -it my-container /bin/bash

# Inspect container details (JSON output)
docker inspect my-container

# View resource usage statistics
docker stats my-container

# View running processes
docker top my-container

# Copy files to/from container
docker cp my-container:/app/file.txt ./file.txt
docker cp ./file.txt my-container:/app/
```

### System Maintenance

```bash
# View Docker system disk usage
docker system df

# Clean up unused data (images, containers, volumes, networks)
docker system prune -a

# View Docker version
docker --version
docker version

# View Docker system info
docker info
```

---

## 6. Docker Networking

### Default Network Drivers

| Driver | Purpose |
|--------|---------|
| **bridge** | Default. Private internal network for containers on a single host |
| **host** | Removes network isolation; container uses host's network directly |
| **none** | Disables all networking |
| **overlay** | Connects containers across multiple Docker hosts (Swarm) |

### Port Mapping

The syntax is `HOST_PORT:CONTAINER_PORT`:

```bash
docker run -p 8080:80 nginx        # Map host 8080 to container 80
docker run -p 80:80 nginx          # Map host 80 to container 80
docker run -p 127.0.0.1:8080:80 nginx  # Bind to localhost only
docker run -p 8080:80/udp nginx    # Map UDP port
docker run -P nginx                # Publish all EXPOSEd ports randomly
```

### Custom Networks

```bash
# Create a custom bridge network
docker network create my-network

# Run containers on the custom network
docker run -d --name db --network my-network postgres:15
docker run -d --name api --network my-network myapp

# Containers on the same network can communicate by name
docker exec api ping db

# List networks
docker network ls

# Inspect a network
docker network inspect my-network

# Remove a network
docker network rm my-network
```

**Why use custom networks?**
- Automatic DNS resolution between containers
- Network isolation from other containers
- Easier container communication (use names instead of IP addresses)

---

## 7. Docker Volumes & Persistent Storage

### The Problem: Container Ephemerality

By default, all data inside a container is lost when the container is removed. Containers are designed to be stateless and replaceable.

### Solution: Volumes

**Volumes** are the preferred mechanism for persisting data outside the container's filesystem.

```bash
# Create a named volume
docker volume create my-data

# Mount a volume to a container
docker run -v my-data:/app/data myapp

# Mount a host directory (bind mount)
docker run -v $(pwd)/data:/app/data myapp

# Modern syntax (more explicit)
docker run --mount source=my-data,target=/app/data myapp
```

### Volume Types

| Type | Syntax | Use Case |
|------|--------|----------|
| **Named Volume** | `-v my-volume:/data` | Managed by Docker, persistent, sharable |
| **Bind Mount** | `-v /host/path:/container/path` | Direct host filesystem access |
| **tmpfs Mount** | `--tmpfs /app/cache` | In-memory storage, non-persistent |

### Volume Commands

```bash
# List volumes
docker volume ls

# Inspect a volume
docker volume inspect my-data

# Remove a volume
docker volume rm my-data

# Remove all unused volumes
docker volume prune
```

---

## 8. Docker Compose: Multi-Container Applications

### What is Docker Compose?

Docker Compose is a tool for defining and running multi-container applications using a YAML file. It simplifies managing complex applications with multiple services.

### docker-compose.yml Example

```yaml
version: '3.8'

services:
  # Web application
  web:
    build: ./web
    ports:
      - "8080:80"
    volumes:
      - ./web/html:/usr/share/nginx/html
    depends_on:
      - api
    networks:
      - frontend

  # API service
  api:
    build: ./api
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
      - REDIS_URL=redis://cache:6379
    depends_on:
      - db
      - cache
    networks:
      - frontend
      - backend

  # Database
  db:
    image: postgres:15-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=mydb
    networks:
      - backend

  # Cache
  cache:
    image: redis:7-alpine
    networks:
      - backend

volumes:
  postgres_data:

networks:
  frontend:
  backend:
```

### Docker Compose Commands

```bash
# Start all services
docker-compose up

# Start in detached mode
docker-compose up -d

# Build and start
docker-compose up --build

# Stop all services
docker-compose down

# Stop and remove volumes
docker-compose down -v

# View logs
docker-compose logs -f

# Scale a service
docker-compose up -d --scale web=3

# Execute command in a service
docker-compose exec api python manage.py migrate

# Validate the compose file
docker-compose config
```

---

## 9. Production Best Practices

### 1. Image Size Optimization

```dockerfile
# Use Alpine or Distroless images
FROM python:3.12-alpine
FROM node:20-alpine
FROM gcr.io/distroless/python3

# Use multi-stage builds
# Clean up in the same layer
RUN apt-get update && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
```

### 2. Security Hardening

```dockerfile
# Run as non-root user
RUN useradd -m -u 1000 appuser
USER appuser

# Use specific image digests for immutability
FROM ubuntu:22.04@sha256:abcdef123...

# Scan images for vulnerabilities
docker scan myapp:1.0
```

### 3. Dockerfile Best Practices

- **Order matters:** Put frequently changing instructions at the bottom to maximize layer caching
- **Minimize layers:** Combine related `RUN` commands
- **Use `.dockerignore`:** Exclude unnecessary files
- **One process per container:** Don't run multiple services in one container
- **Health checks:** Define container health checks

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080/health || exit 1
```

### 4. Resource Limits

```bash
docker run -m 512m --memory-swap 512m --cpus=1.5 myapp
```

### 5. Logging

```bash
# Configure log driver
docker run --log-driver json-file --log-opt max-size=10m --log-opt max-file=3 myapp
```

---

## 10. Complete Workflow Example

### Project Structure

```
my-docker-project/
├── docker-compose.yml
├── .dockerignore
├── web/
│   ├── Dockerfile
│   ├── nginx.conf
│   └── html/
│       └── index.html
└── api/
    ├── Dockerfile
    ├── requirements.txt
    └── app.py
```

### Step-by-Step Deployment

**Step 1: Write the Dockerfile**

```dockerfile
# api/Dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

**Step 2: Build the Image**

```bash
cd api
docker build -t my-api:1.0 .
```

**Step 3: Test Locally**

```bash
docker run -d -p 5000:5000 --name api-test my-api:1.0
curl http://localhost:5000/health
```

**Step 4: Push to Registry**

```bash
docker tag my-api:1.0 myregistry.com/my-api:1.0
docker push myregistry.com/my-api:1.0
```

**Step 5: Deploy with Compose**

```bash
docker-compose up -d
```

**Step 6: Monitor and Maintain**

```bash
docker-compose ps
docker-compose logs -f api
docker stats
```

---

## Quick Reference Cheat Sheet

### Dockerfile Instructions

| Instruction | Purpose | Example |
|-------------|---------|---------|
| `FROM` | Base image | `FROM python:3.12-slim` |
| `WORKDIR` | Working directory | `WORKDIR /app` |
| `RUN` | Build-time command | `RUN pip install flask` |
| `COPY` | Copy files | `COPY . /app` |
| `ENV` | Environment variables | `ENV PORT=8080` |
| `EXPOSE` | Document port | `EXPOSE 8080` |
| `CMD` | Default startup | `CMD ["python", "app.py"]` |
| `ENTRYPOINT` | Main executable | `ENTRYPOINT ["python"]` |
| `USER` | Run as user | `USER appuser` |
| `VOLUME` | Mount point | `VOLUME ["/data"]` |
| `HEALTHCHECK` | Health check | `HEALTHCHECK CMD curl -f ...` |

### Common Commands

| Command | Description |
|---------|-------------|
| `docker build -t name:tag .` | Build image |
| `docker run -d -p 80:80 name` | Run container detached |
| `docker ps` | List running containers |
| `docker stop $(docker ps -q)` | Stop all containers |
| `docker system prune -a` | Clean up everything |
| `docker exec -it container bash` | Enter container |
| `docker logs -f container` | Follow logs |
| `docker-compose up -d` | Start all services |
| `docker-compose down -v` | Stop and clean up |

---

## Key Takeaways

1. **Dockerfile → Image → Container** is the fundamental workflow
2. **Containers are ephemeral** — use volumes for persistent data
3. **One process per container** — use Compose for multi-service apps
4. **Layer caching** — order Dockerfile instructions by change frequency
5. **Security first** — run as non-root, use minimal base images, scan for vulnerabilities
6. **Port mapping** (`-p`) is required to access container services from the host
7. **Custom networks** enable service discovery and isolation

---

*This guide provides a comprehensive foundation for understanding and working with Docker in development and production environments.*
