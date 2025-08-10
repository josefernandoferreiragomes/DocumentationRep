# Common Docker Commands

## Docker Basics

### Version and Info
```bash
# Check Docker version
docker --version

# Display Docker system information
docker info
```

### Help
```bash
# Get help for Docker commands
docker --help
docker <command> --help
```

## Working with Containers

### Run and Manage Containers
```bash
# Run a container from an image
docker run <image_name>

# Run a container with an interactive terminal
docker run -it <image_name>

# Run a container in detached mode
docker run -d <image_name>

# List all running containers
docker ps

# List all containers (including stopped ones)
docker ps -a

# Stop a running container
docker stop <container_id>

# Start a stopped container
docker start <container_id>

# Restart a container
docker restart <container_id>

# Remove a stopped container
docker rm <container_id>
```

### Inspect Containers
```bash
# View container logs
docker logs <container_id>

# View container logs and filter
docker logs <container_id> 2>&1 | grep <keyword>

# Inspect container details
docker inspect <container_id>

# Inspect container details and find a keyword
docker inspect <container_id> | findstr <keyword>

# Access a running container's shell
docker exec -it <container_id> /bin/bash
```

## Working with Images

### Manage Images
```bash
# List all images
docker images

# Pull an image from Docker Hub
docker pull <image_name>

# Remove an image
docker rmi <image_name>
```

### Build Images
```bash
# Build an image from a Dockerfile
docker build -t <image_name> <path_to_dockerfile>

# Tag an image
docker tag <image_name>:<tag> <new_image_name>:<new_tag>
```

## Networking
```bash
# List all networks
docker network ls

# Create a network
docker network create <network_name>

# Inspect a network
docker network inspect <network_name>

# Connect a container to a network
docker network connect <network_name> <container_id>

# Disconnect a container from a network
docker network disconnect <network_name> <container_id>
```

## Docker Volumes
```bash
# List all volumes
docker volume ls

# Create a volume
docker volume create <volume_name>

# Inspect a volume
docker volume inspect <volume_name>

# Remove a volume
docker volume rm <volume_name>
```

## Cleaning Up
```bash
# Remove all stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove unused volumes
docker volume prune

# Remove unused networks
docker network prune

# Remove all unused data
docker system prune
```

## Docker Compose

### Common Commands
```bash
# Start services defined in docker-compose.yml
docker-compose up

# Start services in detached mode
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs

# View logs for a specific service
docker-compose logs <service_name>

# Rebuild services
docker-compose up --build

# Run a one-off command in a service
docker-compose run <service_name> <command>

# Build one of the services defined in docker-compose.yml
docker-compose build <app>

# Start one of the services defined in docker-compose.yml
docker-compose up -d <app>
```

## Useful Tips

- Use `docker ps -q` to get only container IDs.
- Use `docker images -q` to get only image IDs.
- Combine commands with `-f` (force) for batch operations.
