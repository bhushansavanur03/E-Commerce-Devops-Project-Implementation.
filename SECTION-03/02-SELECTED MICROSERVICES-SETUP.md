## Working with Selected Microservices

The application consists of 21 microservices. Since this project focuses on implementing and working with OpenTelemetry, I selected three microservices instead of deploying the complete set.

I selected the following services, each developed using a different programming language:

| Microservice | Programming Language |
|---|---|
| Product Catalog | Go (Golang) |
| Recommendation Service | Python |
| Ad Service | Java |

### Setting Up the Product Catalog Service

I started my EC2 server and prepared the environment required to work with the Product Catalog microservice.

My first step was to install and configure the Go development environment so that I could run the Go-based service.

Next, I navigated to the application's microservices directory and opened the `product-catalog` folder. I reviewed its `README.md` file to understand the service-specific setup instructions, dependencies, and execution steps.

Following the instructions provided in the README, I installed the required dependencies and executed the commands to run the Product Catalog microservice locally.

After completing the setup, I verified that the service was running and producing the expected result.

### Implementation Approach

I followed the same approach of reviewing the service documentation, preparing its language-specific environment, and running the service for the remaining selected microservices:

- Product Catalog – Go
- Recommendation Service – Python
- Ad Service – Java

This allowed me to work with three microservices developed using different programming languages while keeping the project scope focused on OpenTelemetry implementation.

## Setting Up the Selected Microservices

The application consists of 21 microservices. Since this project focuses on OpenTelemetry implementation, I selected three microservices to work with: Product Catalog developed using Go (Golang), Recommendation Service developed using Python, and Ad Service developed using Java.

### Product Catalog Microservice (Go)

I started my EC2 server and opened the cloned application repository. From the repository directory, I navigated to `src` and then entered the `product-catalog` directory.

Before running the service, I updated and upgraded the system packages and verified that Go was installed. I installed Go using the following command:

```bash
sudo apt install golang-go
```

Next, I referred to the `README.md` file inside the Product Catalog directory and followed the documented build and execution instructions.

First, I configured the service port by exporting the required environment variable:

```bash
export PORT=8088
```

I then built the Product Catalog application using:

```bash
go build product_catalog.go
```

This generated the `product_catalog` executable file in the current directory. I executed the generated file to start the microservice:

```bash
./product_catalog
```

After executing the application, I received the expected output as mentioned in the service's README file. This confirmed that the Product Catalog microservice was running successfully on my EC2 instance.





---------------------------------------------------------------------------------------------------------------------------------------------------------------
### Ad Service Microservice (Java with Gradle)

After setting up the Product Catalog microservice, I moved on to the Ad Service, which is developed using Java and Gradle.

I navigated to the Ad Service directory in the cloned repository on my EC2 instance and installed Java 21 using the following command:

```bash
sudo apt install openjdk-21-jdk-headless
```

After installing Java, I provided execution permission to the Gradle wrapper file:

```bash
chmod +x gradlew
```

Next, I executed the Gradle installation task:

```bash
./gradlew installDist
```

Once the build process was completed, I used the following command to list the generated files and locate the Ad Service executable:

```bash
ls -ltr ./build/install/opentelemetry-demo-ad/bin/
```

The build generated the Ad Service executable inside the `build/install/opentelemetry-demo-ad/bin/` directory.

Before starting the service, I configured the application port:

```bash
export PORT=9099
```

Finally, I executed the generated Ad Service file using its path:

```bash
./build/install/opentelemetry-demo-ad/bin/ad
```

The Ad Service started successfully and produced the expected output.
