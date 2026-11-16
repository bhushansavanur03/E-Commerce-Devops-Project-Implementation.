# Prerequisites

Before proceeding with the Kubernetes deployment, the AWS infrastructure needs to be provisioned using Terraform.

The Terraform configuration files required to create an Amazon EKS cluster inside an AWS VPC are organized within the `eks-install` directory of this repository. The implementation also includes an S3 remote backend and state-locking configuration.

## 1. Terraform Project Structure

The following files and directories contain the Terraform configurations:

- **`eks-install/`**: Contains the complete Terraform HCL configuration files required for provisioning the EKS infrastructure.

- **`backend/`**: Contains Terraform configuration files for creating the S3 bucket used for remote state storage and the DynamoDB table used for state locking.

- **`modules/`**: Contains reusable Terraform modules for provisioning the AWS VPC and EKS cluster.

- **`main.tf`**: Main Terraform configuration file that invokes the VPC and EKS modules to provision the infrastructure.

- **`variables.tf`**: Defines the input variables required by the main Terraform configuration.

- **`outputs.tf`**: Defines the output values displayed after Terraform execution, such as the VPC ID and other infrastructure details.

## 2. Useful VS Code Extensions

The following extensions are useful when writing and managing Terraform HCL files and Kubernetes YAML configurations:

- **Terraform:** Provides syntax highlighting, formatting and language support for Terraform configuration files.
- **YAML:** Provides YAML syntax highlighting and validation.
- **GitHub Copilot:** Assists with code suggestions and Terraform configuration development.

## 3. Official Documentation

For additional information and configuration references, the official Terraform AWS Provider documentation is recommended.

- Terraform AWS Provider: https://registry.terraform.io/providers/hashicorp/aws/latest/docs
- Terraform Documentation: https://developer.hashicorp.com/terraform/docs
- Amazon EKS Documentation: https://docs.aws.amazon.com/eks/

## Summary

- Organized Terraform configurations for AWS infrastructure provisioning.
- Implemented remote state storage using an Amazon S3 backend.
- Included state-locking configuration.
- Used reusable Terraform modules for VPC and EKS provisioning.
- Identified the required development tools and official documentation.
