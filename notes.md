# Docker

The container works as an operating system onto itself with all the specified packages and their versions

## Key Concepts

- Docker automates the deployment, scaling, and management of applications through lightweight, portable containers
- It allows the shipping of applications into standardized units, which work across environments

### Workflow

1. Write a Dockerfile: describe how to build the image (base image, app code, deps, commands etc.)
2. Build an image: use `docker build` for it
3. Run container: use the image to start a container. Isolated instance of the image

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
  s
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

## Docker concepts

- Docker client: The UI to interact with Docker
- Docker Host | Docker Daemon: The background process that manages containers on the system
- Docker Registry | [Docker Hub](https://hub.docker.com/): A repo of different Docker images

### CLI Commands

```bash
# Image commands
docker pull [image] # pulls an image from the registry
docker images # lists images
docker rmi [image_id] # removes an image
docker built -t [image_name] [path_to_dockerfile] # builds an image from a Dockerfile

# Container commands
docker run -it [image] # runs a container interactively
  docker run -it [image] sh # open in shell mode
  exit # type on the terminal to leave the container

docker run -d [image] # runs a container in detached mode
docker ps # lists running containers
docker ps -a # lists all containers (including stopped)
docker stop [container_id]
docker start [container_id]
docker restart [container_id]
docker rm [container_id] # removes a stopped container
docker exec -it [container_id] [command] # executes a command inside a running container

# Volume commends
docker volume create [volume_name]
docker volume ls
docker volume inspect [volume_name]
docker volume rm [volume_name]

# Network commands
docker network ls
docker network inspect [network_name]
docker network create [network_name]
docker network connect [network_name] [container_id] # connects a container to a network
docker network disconnect [networ_name] [container_id]

# Prunning commands: Removes all unused elements
docker container prune # removes all stopped containers
docker image prune
docker volume prune
docker network prune
docker system prune # removes everything (volumes, containers, images, networks)

# Other useful commands
docker inspect [container_id | image_id] # inspects a container or image
docker logs [container_id]
docker cp [container_id]:[path_in_container] [path_on_host] # copy from container to host
docker cp [path_on_host] [container_id]:[path_in_container] # copy from host to container
docker stats # monitors container resource usage
docker logs -f [container_id] # follows real-time logs of a container
```

### Examples

#### Running ubuntu

- With this command, we're running a completely different OS inside a container

```bash
docker pull ubuntu
docker run -it ubuntu
```

#### Creating an image and running the container

```bash
# create a simple js file inside a folder
/hello-docker
|- hello.js => simple js file

# enter the folder
cd hello-docker

# build from the folder
docker build -t hello-docker .
docker images # check all images
docker run hello-docker # run container based on image
```

## Dockerfile

### Dockerfile React

```dockerfile
# set the base image to create the image
FROM node:20-alpine

# creates a user with permissions to run the app
# -S: Creates a system user
# -G: Adds the user to the group
# This is done to avoid running the app as root
# If the app is run as root, any vulnerabilities in the app can be exploited to gain access to the host system
# It's a good practice to run the app as a non-root user
RUN addgroup app && adduser -S -G app app

# set the user to run the app
USER app

WORKDIR /app

# copy package.json and package-lock.json to the working directory
# This is done before copying the rest of the files to take advantage of Docker's cache
# If the package.json and package-lock.json files haven't changed, Docker will use cached deps
COPY package*.json ./

# to void error EACCES: permission denied, we change the ownership of the files to the root user
USER root

# change the ownership of the /app directory to the app user
# chown -R <user>:<group> <directory>
# chown command changes the user and/or group ownership of a given file
RUN chown -R app:app .

# change the user back to the app user
USER app

# install dependencies
RUN npm install

# copy the rest of the files to the working directory
COPY . .

# expose port 5173 to tell Docker that the container listens on the specified network ports at run time
EXPOSE 5173

CMD npm run dev
```

- We don't run the container as the root, but as as the USER app
- However, certain operations require elevated permissions, which a non-root user cannot perform. That's why we ensure the /app directory are owned by the `app` user. Without this. the `app` user might encounter permission issues when accessing or modifying files
- Only `root` has the necessary permissions to adjust ownership of files and directories
- After we perform the privileged operations, we switch back to `app` user to ensure the container doesn't run as `root`, which is a security best practice

1. Start with `USER app`: Default user for running the application
2. Temporarily switch to `USER root`: Perform privileged tasks, such as changing file ownership
3. Switch Back to `USER app`: Ensures the application runs securely as a non-root user

## Port Mapping

- Since the container is separated from the OS, we need to map the ports.
- Not only do we need to expose the port in the Dockerfile so the container listens to it, but also on the OS

```bash
docker run -p port_container:port_machine
docker run -p 5173:5173
```

## Running Docker with React

1. Create the dockerfile
2. Build the image
3. Run container mapping the ports `docker run -p 5173:5173`
4. How can we run a container that the changes are updated automatically?

```bash
docker run -p 5173:5173 -v "$(pwd):/app" -v /app/node_modules react-docker
```

## Publishing an image

```bash
docker login
docker tag [image_name] [username]/[image_name]
docker push [username]/[image_name]
```

## Docker Compose

- Used to define and manage multi-container applications using a single YAML file
- Simplifies the process of orchestrating multiple services that work together (e.g., backend, frontend, database)
- `docker-compose.yml`
- It also allows us to specify granulary the commands for each container

```yml
version: "3.8" # Specify Docker Compose file format version

services: # Define services (containers)
  backend:
    image: node:16
    working_dir: /app
    volumes:
      - ./backend:/app
    ports:
      - "5000:5000"
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - db_data:/var/lib/postgresql/data

  frontend:
    build: ./frontend # Build from Dockerfile in 'frontend'
    ports:
      - "3000:3000"
    depends_on:
      - backend

volumes: # Named volumes
  db_data:
```

### Docker init

- It can be very helpful to initialize our app with all the needed files
- Run `docker init`, and it will prompt you with many questions
