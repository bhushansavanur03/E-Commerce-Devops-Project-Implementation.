## Code Explanation: S3 Bucket and DynamoDB Terraform Configuration

This Terraform configuration provisions the AWS resources required for remote Terraform state management. It creates an S3 bucket for storing the statefile and a DynamoDB table for state locking.

### 1. AWS Provider Configuration

```hcl
provider "aws" {
  region = "us-west-2"
}
```

**Explanation:**
- `provider "aws"` specifies that Terraform will use the AWS provider.
- `region = "us-west-2"` specifies the AWS region where resources will be provisioned.
- AWS credentials are obtained through the configured AWS authentication mechanism, such as the AWS CLI profile.

### 2. Creating an S3 Bucket for Terraform State Storage

```hcl
resource "aws_s3_bucket" "terraform_state" {
  bucket = "demo-terrafom-eks-bucket-258"

  lifecycle {
    prevent_destroy = false
  }
}
```

**Explanation:**
- `aws_s3_bucket` is the Terraform resource type used to create an S3 bucket.
- `terraform_state` is the local resource name used to reference this bucket within Terraform.
- `bucket` specifies the globally unique S3 bucket name.
- `prevent_destroy = false` allows Terraform to destroy the bucket when requested.

### 3. Enabling S3 Bucket Versioning

```hcl
resource "aws_s3_bucket_versioning" "terraform_state" {
  bucket = demo-terrafom-eks-bucket-258.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

**Explanation:**
- `aws_s3_bucket_versioning` enables versioning for the S3 bucket.
- `aws_s3_bucket.terraform_state.id` references the bucket created in the previous resource.
- `status = "Enabled"` activates versioning.

**Purpose:** Maintains previous versions of the statefile, helping recover from accidental modifications or deletions.

### 4. Enabling Server-Side Encryption

```hcl
resource "aws_s3_bucket_server_side_encryption_configuration" "terraform_state" {
  bucket = demo-terrafom-eks-bucket-258.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}
```

**Explanation:**
- `aws_s3_bucket_server_side_encryption_configuration` configures encryption for objects stored in the bucket.
- `bucket` references the previously created S3 bucket.
- `sse_algorithm = "AES256"` enables server-side encryption using Amazon S3-managed keys (SSE-S3).

**Purpose:** Protects Terraform statefile data at rest.

### 5. Creating a DynamoDB Table for State Locking

```hcl
resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-eks-state-locks-258"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}
```

**Explanation:**
- `aws_dynamodb_table` creates a DynamoDB table.
- `name` specifies the table name.
- `billing_mode = "PAY_PER_REQUEST"` uses on-demand billing based on actual read and write requests.
- `hash_key = "LockID"` defines the partition key.
- `type = "S"` specifies that `LockID` is a String attribute.

**Purpose:** Terraform uses this table to coordinate state locking and prevent concurrent operations on the same state when using the traditional DynamoDB locking mechanism.

### Summary

| AWS Resource | Purpose |
|---|---|
| AWS Provider | Connects Terraform to AWS |
| S3 Bucket | Stores the remote Terraform statefile |
| S3 Versioning | Maintains previous statefile versions |
| Server-Side Encryption | Protects state data at rest |
| DynamoDB Table | Coordinates Terraform state locking |

**Expected Result:** The Terraform configuration provisions an S3 bucket with versioning and encryption enabled, along with a DynamoDB table for state locking.
