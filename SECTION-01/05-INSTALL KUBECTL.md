## Kubectl Installation on Ubuntu EC2 Instance

After installing Docker, I moved on to installing Kubectl on my Ubuntu EC2 instance.

Kubectl is a command-line tool used to interact with Kubernetes clusters.
It helps manage Kubernetes resources such as Pods, Deployments, and Services.
Since Kubernetes is an important part of this DevOps project, I installed Kubectl to prepare my environment for upcoming Kubernetes activities.

### Installation Process

**1. Download Kubectl**

First, I downloaded the latest stable version of the Kubectl binary for Linux (AMD64 architecture) using the following command:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

**2. Install Kubectl**

After downloading the binary, I moved it to `/usr/local/bin/` and assigned the required permissions so that it could be executed from any directory.

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

**3. Verify Kubectl Installation**

Finally, I checked whether Kubectl was installed successfully by running:

```bash
kubectl version --client
```

The command displayed the installed Kubectl client version, confirming that the installation was successful.

