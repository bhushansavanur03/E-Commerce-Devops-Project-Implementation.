## Docker Installation on Ubuntu EC2 Instance

After successfully creating and connecting to my Ubuntu EC2 instance, I moved on to installing Docker.

Docker is a containerization platform that helps package applications and their dependencies into containers. It makes it easier to run applications consistently across different environments.

Since Docker is one of the important tools used in this DevOps project, I installed it on my Ubuntu EC2 instance by following the official Docker installation steps.

### Installation Process

**1. Update the package list and install required dependencies**

First, I updated the existing package list and installed the required packages such as `ca-certificates` and `curl`.

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
```

**2. Add Docker's official GPG key**

Next, I added Docker's official GPG key to verify the authenticity of the Docker packages.

```bash
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

**3. Add the Docker repository**

I added Docker's official repository to the APT sources so that Ubuntu could download and install Docker packages from the official source.

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

**4. Install Docker**

After adding the repository, I installed Docker Engine, Docker CLI, containerd, Docker Buildx, and the Docker Compose plugin.

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**5. Verify Docker Installation**

Finally, I verified the Docker installation by running the following command:

```bash
sudo docker run hello-world
```

Docker downloaded and executed the Hello World test image and displayed a confirmation message, indicating that Docker was installed and working successfully.

### Learning Outcome

Through this activity, I learned how to install Docker on an Ubuntu EC2 instance using the official Docker repository. I also understood the importance of package repositories, Docker Engine, and Docker CLI.

This installation prepared my EC2 environment for the upcoming containerization activities in this project.
