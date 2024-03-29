## Docker

### Learning Resources

- [Docker Docs](https://docs.docker.com/engine/reference/commandline/docker/)

### Docker Images

- Images are made up of app binaries, dependencies, and metadata. Don't contain a full OS.
- Images are a combination of multiple layers.
- Each Image has its unique ID and a tag for a different version.

### Dockerfile

> Commands:

- `FROM` (base image)
- `COPY` (copy files from local to the container)
- `ARG` (pass arguments)
- `ENV` (environment variable)
- `RUN` (any arbitrary shell command)
- `EXPOSE` (open port from container to virtual network)
- `CMD` (command to run when the container starts) 
- `WORKDIR` (Create a dir where all the files will be copied and used.)

To build an image from the **Dockerfile**, use this command

```bash
docker build <path> 
// docker build .
```

**Good Practice**

- Copy the dependencies 1st and then copy the rest of the files.

```Dockerfile
COPY package.json ./
RUN npm install
COPY . ./
```

###  .dockerignore

The .dockerignore file is used to specify files and directories that are not copied when using the `COPY` command.

###  Docker Network

To connect to our created containers docker provides several network drivers. The available default drivers are bridge, host and null.

- Bridge network creates a virtual network that allows containers to communicate with each other using IP addresses. We need to create custom bridge network to enable dns resolution between containers. Only containers connected to the same custom bridge network can communicate with each other directly. It doesn't work with the default bridge network.

- Host network uses the host machine's network stack inside the container. We can use this network for applications that require high network performance. We don't need to expose ports here.

- Using Null network driver disables the networking for the container.


- To Create a network, by default the created network will use bridge network driver

```bash
docker network create <network-name>
```

###  Docker Volumes

We need volume to Persist our data, like databases and user info, because containers can go up and down, and we need some way to preserve our data.

We attach volume during run time

```bash
docker run -v /path/in/container
```

**Named Volume**
We can also name the volume otherwise it will generate the ID and be hard to track

```bash
docker run -v <volume name>:</path in container> <image name>
docker run -v myvolume:/src/public nginx
```

### Bind Mounting

A file or directory on the host machine is mounted into a container, i.e it will match the condition of the file system inside a container.

```bash
docker run -v <path to your local system>:<container path>
docker run -v /app/content:/usr/share/nginx/html  nginx
docker run -v $(pwd):/user/html nginx
```
 In compose, we don't have to give the `pwd`
 
 ```yaml
     volumes:
      - ./:/usr/share/nginx/html:ro
      - ./app:/usr/share/nginx/html/app:ro 
 ```

###  Docker Compose

- Compose help us define and running multi-container Docker applications and configure relationships between containers 
- It also saves the hassle from entering the commands from the CLI.
- We have to write the configs in the YAML file, by default the file name is `docker-compose.yml`. We can run/stop by `docker compose up/down`

The Skeleton of Docker compose

```yaml
services:  # containers. same as docker run
  servicename: # a friendly name. this is also the DNS name inside the network
    image: # Optional if you use to build:
    command: # Optional, replace the default CMD specified by the image
    environment: # Optional, same as -e in docker run
    volumes: # Optional, same as -v in docker run
  servicename2:

volumes: # Optional, same as docker volume create

networks: # Optional, same as docker network create
```

Sample:

```yaml
services:
  mongo:
    container_name: mongo
    image: mongo:4.0
    volumes:
      - mongo-db:/data/db
    networks: 
      - my-net
      
volumes:
  mongo-db: # named volume
  
networks:
    my-net:
        driver: bridge
```

If any container depends on another container

```yaml
depends_on:
  - mysql-primary
```

##  Docker Swarm

Docker Swarm is an orchestration management tool that runs on Docker applications. Container orchestration automates the deployment, management, scaling, and networking of containers

- Docker Swarm is not enabled by default, we have enabled it by

```bash
docker swarm init
```

- In this, we create services, instead of creating the container directly

### Docker service

In swarm we don't create containers directly, instead, we create service and that creates a container for us. A service can run multiple nodes on several nodes.

### Docker Stack

When we have multiple services and to establish the relationship between them we use the stack, it is the same as compose file.
Here we don't use `build:` object and there is new `deploy:` specific to swarm to like replicas, and secrets.

```yaml
    deploy:
      replicas: 3
```
We deploy stack files with this command

```bash
docker stack deploy -c file.yml <stackname>
```

### Docker Secrets

Docker Swarm supports secrets. We can pass ENV variables like SSH keys, Usernames, and passwords with help of that. We can pass secrets from the file or save the Docker secret.

-  We can create Docker secrets though CLI `external:`

```bash
echo "<password text>" | docker secret create psql-pw -
```

or

- Create a file with a password and then pass the path in the stack `file:`

```yaml
services:
    postgres:
      image: postgres
      secrets:
        - post-pass
        - post-user
      environment:
          POSTGRES_PASSWORD_FILE: /run/secrets/post-pass
          POSTGRES_USER_FILE: /run/secrets/post-user
      
secrets:
    post-pass:
      external: true
    post-user:
        file: ./post-user.txt
```
##  Docker Healthcheck

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
CMD curl -f http://localhost/ || exit 1
```

##  Private Docker Registry

We can create a reg with the official [Registry image](https://hub.docker.com/_/registry)