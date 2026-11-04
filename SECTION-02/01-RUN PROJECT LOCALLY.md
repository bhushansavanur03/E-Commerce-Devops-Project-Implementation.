## Running the E-Commerce Application Using Docker Compose

After completing the installation of Docker, Kubectl, and Terraform, I moved on to running the E-Commerce application on my Ubuntu EC2 instance.

### 1. Cloning the Project Repository

First, I cloned the E-Commerce project source code repository into my EC2 instance using Git.

```bash
git clone <repository-url>
```

After cloning the repository, I navigated into the project directory to access the application files and Docker Compose configuration.

### 2. Running the Application Using Docker Compose

Once I entered the project directory, I executed the following command to start the application:

```bash
docker compose up
```

Docker Compose started downloading the required images and setting up the application containers. However, the process could not be completed because my EC2 instance did not have sufficient disk space.

### 3. Increasing the EC2 EBS Volume Size

To resolve the disk space issue, I went to the AWS EC2 Console and increased the attached EBS volume size from **8 GB to 30 GB**.

However, I noticed that even after increasing the EBS volume size from the AWS Console, the additional space was not yet available to the Ubuntu operating system.

I understood that increasing the EBS volume size in AWS and extending the partition and filesystem inside the operating system are two separate steps.

### 4. Extending the Partition and Filesystem

To make the additional storage available, I used the `growpart` utility to extend the partition.

```bash
sudo growpart /dev/xvda 1
```

Next, I resized the filesystem to use the newly available space.

```bash
sudo resize2fs /dev/xvda1
```

After completing these steps, I checked the available disk space using:

```bash
df -h
```

This helped me verify that the additional storage was available to the operating system.

### 5. Starting the Application in Detached Mode

After resolving the storage issue, I went back to the project directory and executed:

```bash
docker compose up -d
```

The `-d` option runs the containers in detached mode, allowing them to run in the background.

The application containers started successfully.

### 6. Browser Access Issue

After starting the application, I tried accessing it through my browser using the EC2 instance's public IP address.

However, I was unable to access the application because the required inbound traffic was not allowed through the EC2 Security Group.

I identified that the security group configuration needed to be updated to allow access to the application's port.

I will cover the Security Group configuration and resolving this browser access issue in the next section.

### Learning Outcome

Through this activity, I got my first hands-on experience running a complete E-Commerce application using Docker Compose on an AWS EC2 instance.

I also learned how to clone a project repository, start multiple containers, troubleshoot insufficient disk space, increase an EBS volume, and extend the filesystem inside Ubuntu.

One important thing I understood is that increasing the storage size in AWS Console alone does not automatically make the additional space available to the operating system. The partition and filesystem may also need to be extended.

This activity also helped me understand the role of AWS Security Groups in controlling access to applications running on EC2 instances.
