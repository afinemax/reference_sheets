## Docker Basics
```sh
docker --version             # Check Docker version
docker info                  # Display system-wide information
docker ps                    # List running containers
docker ps -a                 # List all containers (including stopped ones)
docker images                # List available images
docker pull <image>          # Download an image from Docker Hub
docker run <image>           # Run a container from an image
docker run -d <image>        # Run container in detached mode
docker run -it <image> bash  # Start a container and open an interactive shell
docker exec <container> <cmd>  # Run a command in a running container
docker exec -it <container> bash     # Open an interactive shell inside a running container
docker start <container>     # Start a stopped container
docker stop <container>      # Stop a running container
docker restart <container>   # Restart a container
docker rm <container>        # Remove a container
docker rmi <image>           # Remove an image
```

## Docker Compose
```sh
docker-compose up            # Start all services defined in docker-compose.yml
docker-compose up -d         # Start services in detached mode
docker-compose down          # Stop and remove containers, networks, and volumes
docker-compose ps            # List running services
docker-compose logs          # View logs for all services
docker-compose logs -f       # View real-time logs
docker-compose build         # Build images based on the compose file
docker-compose -f <file>.yml up -d  # Start services from a specific compose file
```

## Checking the Status of Containers
```sh
docker ps                    # Show running containers
docker ps -a                 # Show all containers (running and stopped)
docker inspect <container>   # Detailed information about a container
docker logs <container>      # View logs of a container
docker stats                 # Display real-time resource usage
```

## Stopping a Container
```sh
docker stop <container>      # Gracefully stop a running container
docker kill <container>      # Force stop a container
docker-compose stop          # Stop all services in docker-compose.yml
docker-compose down          # Stop and remove all services
```

## Connecting to a Running Container
```sh
docker exec -it <container> /bin/bash  # Open an interactive shell
docker attach <container>              # Attach to a running container
docker logs -f <container>             # View logs in real time
```

## Mounting Files and Volumes
```sh
docker run -v /host/path:/container/path <image>  # Mount a directory
docker run --mount type=bind,source=/host/path,target=/container/path <image> # Bind mount
docker volume create <volume_name>               # Create a named volume
docker run -v <volume_name>:/container/path <image>  # Use a named volume
docker volume ls                                # List all volumes
docker volume inspect <volume_name>            # Inspect a volume
docker volume rm <volume_name>                 # Remove a volume
```

## Running a Minecraft Server with Docker

### Docker Compose File Example
Create a `docker-compose.yml` file with the following content:
```yaml
version: '3.8'
services:
  minecraft:
    image: itzg/minecraft-server
    container_name: minecraft_server
    ports:
      - "25565:25565"
    environment:
      EULA: "TRUE"
      MEMORY: "2G"
    volumes:
      - ./minecraft_data:/data
    restart: unless-stopped
```

### Running the Server
```sh
docker-compose -f docker-compose.yml up -d  # Start the Minecraft server from a specific compose file
docker-compose logs -f minecraft  # View server logs
docker-compose down  # Stop and remove the Minecraft server container
```

This setup ensures that Minecraft server data is persisted and the server automatically restarts unless manually stopped.

