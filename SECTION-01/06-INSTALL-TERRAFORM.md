## Terraform Installation on Ubuntu EC2 Instance

After installing Docker and Kubectl, I moved on to installing Terraform on my Ubuntu EC2 instance.

Terraform is an Infrastructure as Code (IaC) tool developed by HashiCorp. It helps create and manage cloud infrastructure using configuration files instead of setting up resources manually.

Since Terraform will be used in this project to provision AWS infrastructure, I installed it on my EC2 instance.

### Installation Process

**1. Install the required dependencies**

First, I updated the package list and installed the required packages such as `gnupg` and `software-properties-common`.

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
```

**2. Add HashiCorp's GPG key**

Next, I downloaded and added HashiCorp's official GPG key to verify the authenticity of the Terraform packages.

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
```

**3. Add the HashiCorp repository**

I added the official HashiCorp repository to the Ubuntu package sources so that Terraform could be installed using the APT package manager.

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
```

**4. Install Terraform**

After adding the repository, I installed Terraform using the following command:

```bash
sudo apt-get install terraform
```

**5. Verify Terraform Installation**

Finally, I checked whether Terraform was installed correctly by running:

```bash
terraform -help
```

The command displayed Terraform's available commands and usage information, confirming that Terraform was accessible from the terminal.

