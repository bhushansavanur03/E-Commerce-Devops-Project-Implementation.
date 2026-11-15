## Containerization of Go-Based Product Catalog Microservice

### Stage 1: Build Stage (Builder Image)

This stage is responsible for compiling the Go application and generating an executable binary.

1. **Use a Go Base Image**
   - The `golang:1.22-alpine` image is used as the builder image.
   - Alpine provides a lightweight Linux environment with the Go compiler and required build tools.

2. **Define the Working Directory**
   - `WORKDIR /usr/src/app/` establishes the working directory inside the container.
   - All subsequent build operations are performed from this directory.

3. **Copy Go Dependency Files**
   - `go.mod` and `go.sum` are copied into the builder image.
   - These files define the application's dependencies and their respective versions.

4. **Download Dependencies**
   - `go mod download` downloads the dependencies specified in the Go module files.
   - Downloading dependencies before copying the application source code helps improve Docker layer caching.

5. **Copy Application Source Code**
   - `COPY . .` copies the remaining application source code into the working directory.
   - This includes the Go source files and other required project files.

6. **Build the Go Application**
   - `go build -o product-catalog .` compiles the Go application and generates an executable binary named `product-catalog`.
   - This binary is used to run the microservice in the runtime stage.

### Stage 2: Runtime Stage (Final Image)

This stage creates the final lightweight image required to execute the compiled Go application.

1. **Use a Minimal Base Image**
   - The `alpine` image is used as the runtime base image.
   - Since the application has already been compiled, the Go compiler and build tools are not required in this stage.

2. **Define the Working Directory**
   - `WORKDIR /usr/src/app/` establishes the application directory inside the runtime container.

3. **Copy Required Application Files**
   - The `./products/` directory is copied from the build context into the runtime image.
   - This directory contains application resources required by the Product Catalog service.
   - `COPY --from=builder /usr/src/app/product-catalog .` copies the compiled executable from the builder stage into the runtime image.

4. **Configure Environment Variables**
   - `PRODUCT_CATALOG_PORT=8088` defines the port configuration used by the Product Catalog microservice.

5. **Define the Container Entry Point**
   - `ENTRYPOINT ["./product-catalog"]` specifies the executable that starts when the container launches.
   - This starts the Product Catalog microservice.
  
   - ## Building, Running and Pushing the Docker Image

After creating the Dockerfile, the next step was to build the Docker image, run the container, verify the application and publish the image to Docker Hub.

### 1. Build the Docker Image

Executed the following command:

```bash
docker build -t bushan2525/product-catalog:v1 .
```

- `docker build`: Builds a Docker image using the Dockerfile.
- `-t`: Assigns a name and tag to the image.
- `bushan2525`: Docker Hub username.
- `product-catalog`: Image repository name.
- `v1`: Image version tag.
- `.`: Specifies the current directory as the Docker build context.

The Docker image was successfully built and tagged as `bushan2525/product-catalog:v1`.

### 2. Run the Docker Container

Executed the following command:

```bash
docker run bushan2525/product-catalog:v1
```

This command created and started a container using the newly built Docker image.

The Product Catalog microservice started successfully, and the expected application output was observed, confirming that the container was running as expected.

### 3. Push the Docker Image to Docker Hub

After successfully testing the container, the image was pushed to my Docker Hub repository.

Executed the following command:

```bash
docker push bushan2525/product-catalog:v1
```

Docker uploaded the image layers to the specified Docker Hub repository.

The image was successfully published and is now available in my Docker Hub account.

### Outcome

- Successfully built the Docker image from the Go microservice Dockerfile.
- Created and executed a container using the generated image.
- Verified the application execution through the expected output.
- Published the versioned Docker image to my Docker Hub repository.

**Docker Hub Image:** `bushan2525/product-catalog:v1`

### Summary

- Implemented a multi-stage Docker build to separate application compilation from runtime execution.
- Used a Go-based builder image to compile the microservice.
- Used a lightweight Alpine runtime image to minimize the final image size.
- Copied only the compiled binary and required application resources into the final image.
- Created an optimized container image for deploying the Product Catalog microservice.
