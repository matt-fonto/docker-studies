# Docker

The container works as an operating system onto itself with all the specified packages and their versions

## Key Concepts

- Docker automates the deployment, scaling, and management of applications through lightweight, portable containers
- It allows the shipping of applications into standardized units, which work across environments

#### Advantages of Docker

1. Consistency a cross environments
2. Isolation
3. Portability
4. Version control
5. Scalability
6. DevOps integration

### Containers: Runnable instance of a Docker Image

- Lightweight, isolated environments for running applications
- It's the manifestation of the image
- Share the host OS kernel, making them faster and more resource-efficient than virtual machines

### Images: A recipe for our application

- Define the application code, dependencies, libraries, and runtime environment
- Built using a `Dockerfile`
- Immutable templates used to create containers
- Includes everything needed to run a piece of software -- code, runtime, libraries, system tools, os.

### Commands

- FROM: Specifies the base image for the new image: `FROM image[:tag] [AS name]`

  ```dockerfile
  # FROM image[:tag]

  FROM node:16-alpine
  ```

- RUN: Executes commands during the image build process (e.g., installing software)

  ```dockerfile
  # RUN <command>

  RUN apt-get update && apt-get install -y curl
  ```

- CMD: Specifies the default command to run when a container starts. Overrides can be passed during `docker run`

  - Default executable

  ```dockerfile
  # CMD ["executable", "arg1", "arg2"]

  CMD ["node", "app.js"]
  ```

- ENTRYPOINT: Sets the main executable for the container. Works like `CMD` but is harder to override

  - Fixed starting point

  ```dockerfile
  # ENTRYPOINT ["executable", "arg1", "arg2"]

  ENTRYPOINT ["ngix", "-g", "deamon off;"]
  ```

- COPY: Copies files/directories from the host to the image

  ```dockerfile
  # COPY <source> <destination>

  COPY ./app /usr/src/app
  ```

- ADD: Similar to `COPY`, but also supports extracting archives and downloading files from URLs

  ```dockerfile
  # ADD <source> <destination>

  ADD ./app /usr/src/app
  ```

- WORKDIR: Sets the working directory for subsequent instructions like `RUN`, `CMD`, or `ENTRYPOINT`

  ```dockerfile
  # WORKDIR <path>

  WORKDIR /usr/src/app
  ```

- EXPOSE: Documents the port on which the containerized app listens

  ```dockerfile
  # EXPOSE <port>

  EXPOSE 3000
  ```

- ENV: Sets the environment variables inside the container

  ```dockerfile
  # ENV <key> <value>

  ENV NODE_ENV production
  ```

- ARG: Defines build-time variables, usable in `RUN` or other commands

  ```dockerfile
  # ARG <name>[=<default>]

  ARG APP_VERSION=1.0
  ```

### Dockerfile

- A script containing instructions to build a Docker image
- Defines steps like setting a base image, copying files, installing dependencies, and specifying the command to run

### Docker engine

- The core software enabling Docker functionality
- Manages the lifecycle of containers, including creation, execution, and removal

### [Docker Hub](https://hub.docker.com/)

- A cloud-based registry for sharing Docker images
- Provides official images for common software like databases, web servers, and programming languages
- Access the desired image and run the command `docker pull <image>`
  - Once we do this, we can go to Docker Desktop > Images and see the image there

### Volumes: Persistent data storage

- Persistent storage solutions for containers
- Allow data to persist across container restarts and updates

### Networking: Allows different Docker containers to talk to each other or external world

- Facilitates communication between containers, and between containers and the host system
- Provides various modes (e.g., bridge, host, overlay) for differetn networking setups

### Orchestration

- Tools like Docker compose or Docker Swarm manage multi-container applications and scaling

## Docker Workflow

- Docker client: The UI to interact with Docker
- Docker Host | Docker Daemon: The background process that manages containers on the system
- Docker Registry | [Docker Hub](https://hub.docker.com/): A repo of different Docker images

### CLI Commands

```bash
# Image commands
docker pull [image] # pulls an image from the registry
docker images # lists images
docker rmi [image_id] # removes an image
docker built -r [image_name] [path_to_dockerfile] # builds an image from a Dockerfile

# Container commands
docker run -it [image] # runs a container interactively
docker run -d [image] # runs a container in detached mode
```
