# Containerization of Python-Based Microservice

The Recommendation microservice was containerized using Docker to create a lightweight and consistent runtime environment.

## Stage 1: Base Image Setup

This stage prepares the Python environment, installs application dependencies and configures the microservice.

1. **Use a Minimal Python Base Image**
   - Used `python:3.12-slim-bookworm` as the base image.
   - The Slim variant provides a lightweight Python environment with essential packages, helping reduce the image size.

2. **Set the Working Directory**
   - Configured `/usr/src/app/` as the working directory inside the container.

3. **Copy Dependency File**
   - Copied `requirements.txt` into the container.
   - This file contains the Python packages required by the application.

4. **Upgrade Pip**
   - Executed `pip install --upgrade pip` to upgrade the Python package installer.

5. **Install Application Dependencies**
   - Executed `pip install -r requirements.txt` to install the required Python packages.

6. **Copy Application Source Code**
   - Copied the complete application source code into the container using `COPY . .`.

7. **Install OpenTelemetry Dependencies**
   - Executed `opentelemetry-bootstrap -a install` to install the appropriate OpenTelemetry instrumentation packages.
   - This prepares the application for automatic instrumentation and distributed tracing.

8. **Configure Environment Variables**
   - Set `RECOMMENDATION_PORT=1010` to configure the port used by the Recommendation microservice.

9. **Define the Container Entry Point**
   - Configured `ENTRYPOINT ["python", "recommendation_server.py"]`.
   - This command starts the Recommendation microservice whenever the container is launched.
  
10. ## Building and Running the Python Docker Image

### 1. Build the Docker Image

Executed the following command:

```bash
docker build --no-cache -t bhushan2525/recommendation:v1 .
```

- `docker build`: Builds the Docker image using the Dockerfile.
- `--no-cache`: Ensures that Docker does not reuse previously cached build layers.
- `-t`: Assigns a name and version tag to the image.
- `bhushan2525`: Docker Hub username.
- `recommendation`: Image repository name.
- `v1`: Image version.
- `.`: Specifies the current directory as the Docker build context.

The Docker image was successfully built with the tag `bhushan2525/recommendation:v1`.

### 2. Run the Docker Container

Executed the following command:

```bash
docker run -d -p 1010:1010 bhushan2525/recommendation:v1
```

- `docker run`: Creates and starts a container from the specified image.
- `-d`: Runs the container in detached mode (background).
- `-p 1010:1010`: Maps port 1010 on the host machine to port 1010 inside the container.
- `bhushan2525/recommendation:v1`: Specifies the Docker image used to create the container.

The Recommendation microservice was started in detached mode with the port mapping configured.

### Outcome

- Successfully built the Python microservice Docker image without using cached layers.
- Started the container in detached mode.
- Configured host-to-container port mapping on port 1010.
- Prepared the container for application testing and verification.

## Summary

- Containerized the Python-based Recommendation microservice.
- Used Python 3.12 Slim Bookworm as the base image.
- Installed application dependencies using pip and requirements.txt.
- Integrated OpenTelemetry auto-instrumentation dependencies.
- Configured the application port using environment variables.
- Defined the application startup command using ENTRYPOINT.
- Prepared the Python microservice for container execution.
