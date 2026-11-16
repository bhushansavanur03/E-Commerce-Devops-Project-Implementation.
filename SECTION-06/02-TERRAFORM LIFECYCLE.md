# Terraform Lifecycle

Terraform follows a structured lifecycle to manage infrastructure using Infrastructure as Code (IaC). It provides a systematic approach to initializing configurations, reviewing infrastructure changes, applying modifications and managing resources.

The primary stages of the Terraform lifecycle are:

## 1. Initialization (`terraform init`)

- Initializes the Terraform working directory.
- Downloads the required provider plugins specified in the Terraform configuration.
- Downloads and initializes the required Terraform modules.
- Configures the backend responsible for storing Terraform state files.
- Prepares the working directory for subsequent Terraform operations.

## 2. Planning (`terraform plan`)

- Compares the desired infrastructure configuration with the existing Terraform state and actual infrastructure.
- Generates an execution plan showing the resources that will be created, modified or destroyed.
- Helps identify potential infrastructure changes before execution.
- Allows changes to be reviewed before applying them.

## 3. Application (`terraform apply`)

- Executes the proposed infrastructure changes.
- Creates, updates or destroys AWS resources according to the Terraform configuration.
- Updates the Terraform state file to reflect the infrastructure changes.
- Stores the updated state in the configured remote backend when remote state storage is enabled.

## 4. Destruction (`terraform destroy`)

- Identifies the infrastructure resources managed by the Terraform configuration.
- Generates a destruction plan for the resources that will be removed.
- Upon confirmation, destroys the managed infrastructure.
- Updates the Terraform state to reflect the removal of resources.


# 1. Initialize Terraform
terraform init

# 2. Validate configuration
terraform validate

# 3. Review infrastructure changes
terraform plan

# 4. Provision AWS infrastructure
terraform apply

# 5. Destroy infrastructure when no longer required
terraform destroy



## Summary

- **Initialize:** Prepares the Terraform working directory.
- **Plan:** Reviews proposed infrastructure changes.
- **Apply:** Provisions or modifies infrastructure.
- **Destroy:** Removes Terraform-managed infrastructure.

These lifecycle commands provide a controlled workflow for provisioning and managing AWS infrastructure through Terraform.
