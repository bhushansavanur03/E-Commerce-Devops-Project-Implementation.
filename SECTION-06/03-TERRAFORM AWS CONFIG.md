# Connect Terraform with AWS to Create AWS Resources

Before provisioning AWS infrastructure using Terraform, the AWS CLI must be installed and configured. It allows Terraform to authenticate with AWS and interact with AWS services through the configured credentials.

## 1. Install AWS CLI

AWS Command Line Interface (AWS CLI) is a tool that allows users to interact with AWS services through terminal commands.

### On Linux

Execute the following commands to download and install AWS CLI:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install
```

Verify the installation:

```bash
aws --version
```

### On Windows

- Download the AWS CLI installer from the official AWS documentation.
- Run the installer and follow the installation instructions.
- Open Command Prompt or PowerShell and verify the installation.

```bash
aws --version
```

Official installation guide: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

### On macOS

Install AWS CLI using Homebrew:

```bash
brew install awscli
```

Verify the installation:

```bash
aws --version
```

## 2. Configure AWS CLI

After installing AWS CLI, configure it with AWS credentials.

Execute the following command:

```bash
aws configure
```

AWS CLI prompts for the following details:

- **AWS Access Key ID:** Access key associated with the IAM user or credentials being used.
- **AWS Secret Access Key:** Corresponding secret access key.
- **Default Region Name:** AWS region where resources will be provisioned (for example, `us-east-1`).
- **Default Output Format:** Specifies the format in which AWS CLI displays command output (for example, `json` or `text`).

### Verify AWS Configuration

Execute:

```bash
aws sts get-caller-identity
```

This command verifies the currently configured AWS identity and displays details such as the AWS account ID, user ID and ARN.

## 3. How Terraform Uses AWS Credentials

- AWS CLI stores credentials configured through `aws configure` in the local AWS credentials file, generally located at `~/.aws/credentials`.
- The default AWS profile can be used by the Terraform AWS Provider to authenticate with AWS.
- Terraform uses these credentials to communicate with AWS APIs and provision resources such as VPCs, EC2 instances, S3 buckets and EKS clusters.

**Note:** AWS credentials should never be hardcoded into Terraform configuration files or committed to GitHub.

## Summary

- Installed AWS CLI to interact with AWS services through terminal commands.
- Configured AWS authentication using `aws configure`.
- Verified AWS identity using `aws sts get-caller-identity`.
- Established AWS authentication for Terraform infrastructure provisioning.
