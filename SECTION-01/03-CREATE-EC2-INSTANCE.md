## AWS EC2 Instance Creation and Setup

After completing the IAM user creation, I moved on to launching an EC2 (Elastic Compute Cloud) instance in AWS. Amazon EC2 provides virtual servers in the cloud that can be used to run applications, install software, and perform various DevOps activities.

I logged in to the AWS Management Console using my IAM user and navigated to the EC2 service. I selected the **Launch Instance** option and provided a suitable name for my instance.

During the instance configuration, I selected an appropriate Amazon Machine Image (AMI) based on the operating system required for the project. I then selected the required instance type based on the project's resource needs.

For the network configuration, I used the default VPC and subnet settings and enabled the public IP address to access the instance over the internet.

I configured the storage to **30 GB** because I would be installing DevOps tools and downloading Docker images, which require additional disk space.

I also created a security group and configured an inbound rule to allow SSH access through port 22. This would allow me to connect to my Linux EC2 instance remotely.

Next, I created a new RSA key pair and downloaded the `.pem` file. I stored this file securely because it is required to establish an SSH connection to the EC2 instance.

After reviewing all the configurations, I launched the instance and verified that it was running successfully.

### Connecting to the EC2 Instance

To access my EC2 instance, I used SSH from my local terminal.

```bash
ssh -i /path/to/your-key.pem ec2-user@your-instance-public-ip
```

Before connecting, I ensured that the private key had the required permissions:

```bash
chmod 400 your-key.pem
```

After establishing the SSH connection, I was able to access the EC2 instance and execute commands remotely.

### Learning Outcome

Through this activity, I learned how to launch and configure an EC2 instance, select an appropriate AMI and instance type, configure storage and security groups, and connect to a cloud-based Linux server using SSH.

I also understood the importance of securely managing key pairs, controlling network access, and monitoring AWS resource usage to avoid unnecessary costs.
