# Docker Compose Overview

Docker Compose is a tool used to define, configure and manage multi-container applications using a single YAML configuration file, typically named `docker-compose.yaml` or `compose.yaml`.

It simplifies the process of running multiple interconnected services by allowing them to be managed through a single command.

## 1. Services

- Services define the individual containers that make up an application.
- Each service represents a specific component, such as a frontend, backend, database or microservice.
- Docker Compose uses the service configuration to create and manage the corresponding containers.

### Common Service Configurations

- **Image:** Specifies the Docker image used to create the container.
- **Build:** Defines the location of the Dockerfile and build context when building an image.
- **Ports:** Maps ports between the host machine and the container.
- **Environment:** Specifies environment variables required by the application.
- **Depends_on:** Defines startup and dependency relationships between services.

## 2. Networks

- Networks enable communication between containers.
- Containers connected to the same Docker network can communicate with each other using their service names.
- This simplifies communication between different components of a multi-container application.

### Common Network Configurations

- **Driver:** Specifies the network driver, such as `bridge` or `overlay`.
- **Attachable:** Allows standalone containers to connect to an overlay network when supported by the network driver.

## 3. Volumes

- Volumes provide persistent storage for container data.
- They ensure that important data remains available even when containers are stopped, removed or recreated.
- Volumes are commonly used for databases and applications that require persistent storage.

### Types of Storage

- **Named Volumes:** Docker-managed storage volumes that can be reused by multiple containers.
- **Bind Mounts:** Map a specific file or directory from the host machine into a container.


# Start all services
docker compose up -d

# List running services
docker compose ps

# View service logs
docker compose logs -f

# Stop and remove containers
docker compose down


## Summary

- **Services:** Define the containers and their configurations.
- **Networks:** Enable communication between containers.
- **Volumes:** Provide persistent storage for application data.
- **Docker Compose:** Simplifies the management of multi-container applications through a single YAML configuration file.
