## Docker Lifecycle

As part of this project, I applied the Docker concepts and commands to understand and manage the application containerization process. Docker provides a consistent way to package and run applications across different environments.

The Docker lifecycle involves three major stages:

### 1. Dockerfile Creation

I used Dockerfile instructions to define the application environment, including the base image, required dependencies, application files, and startup commands. The Dockerfile acts as the blueprint for building the application image.

### 2. Docker Image Build

Using the Dockerfile, I built Docker images with the `docker build` command. These images contain the application code, dependencies, libraries, and other required components needed to run the application.

### 3. Running Docker Containers

After building the images, I used them to create and run Docker containers. Containers provide isolated environments for executing applications and help maintain consistency across different deployment environments.

### Implementation Flow

The Docker workflow followed in this project is:

**Dockerfile → Docker Image → Docker Container**

This process forms an important part of the application containerization and deployment workflow.
