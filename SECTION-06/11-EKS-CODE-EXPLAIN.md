# Code Explanation: Terraform EKS Module

The EKS module provisions an Amazon Elastic Kubernetes Service (EKS) cluster and its worker nodes. It creates the required IAM roles, attaches AWS-managed policies, provisions the EKS control plane, and configures managed node groups.

## 1. Creating an IAM Role for the EKS Cluster

```hcl
resource "aws_iam_role" "cluster" {
  name = "${var.cluster_name}-cluster-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Principal = {
        Service = "eks.amazonaws.com"
      }
    }]
  })
}
```

**Explanation:**
- `aws_iam_role` creates an IAM role for the EKS cluster.
- `name` dynamically generates the role name using the cluster name.
- `assume_role_policy` defines the trust relationship that determines who can assume this role.
- `jsonencode()` converts the Terraform configuration into JSON format.
- `Action = "sts:AssumeRole"` allows the trusted service to assume the role.
- `Principal` specifies `eks.amazonaws.com` as the trusted AWS service.

**Purpose:** Allows Amazon EKS to assume the IAM role and perform the AWS operations required to manage the Kubernetes control plane.

## 2. Attaching an IAM Policy to the Cluster Role

```hcl
resource "aws_iam_role_policy_attachment" "cluster_policy" {
  policy_arn = "arn:aws:iam::aws:policy/AmazonEKSClusterPolicy"
  role       = aws_iam_role.cluster.name
}
```

**Explanation:**
- `aws_iam_role_policy_attachment` attaches an IAM policy to the cluster role.
- `policy_arn` specifies the AWS-managed `AmazonEKSClusterPolicy`.
- `role` references the IAM role created in the previous step.

**Purpose:** Grants the EKS cluster role the permissions required for cluster management operations.

## 3. Creating the EKS Cluster

```hcl
resource "aws_eks_cluster" "main" {
  name     = var.cluster_name
  version  = var.cluster_version
  role_arn = aws_iam_role.cluster.arn

  vpc_config {
    subnet_ids = var.subnet_ids
  }

  depends_on = [
    aws_iam_role_policy_attachment.cluster_policy
  ]
}
```

**Explanation:**
- `aws_eks_cluster` provisions the Amazon EKS cluster.
- `name` specifies the cluster name.
- `version` specifies the Kubernetes version.
- `role_arn` assigns the IAM role created for the EKS control plane.
- `vpc_config` defines the networking configuration.
- `subnet_ids` specifies the VPC subnets in which EKS can place its network interfaces.
- `depends_on` ensures the IAM policy attachment is completed before Terraform creates the cluster.

**Purpose:** Creates the Kubernetes control plane responsible for managing workloads, scheduling pods, and maintaining the cluster.

## 4. Creating an IAM Role for the Node Group

```hcl
resource "aws_iam_role" "node" {
  name = "${var.cluster_name}-node-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Principal = {
        Service = "ec2.amazonaws.com"
      }
    }]
  })
}
```

**Explanation:**
- Creates an IAM role for the EKS worker nodes.
- `name` dynamically generates the node role name using the cluster name.
- `assume_role_policy` defines the trust relationship.
- `Action = "sts:AssumeRole"` allows the trusted service to assume the role.
- `Principal` specifies `ec2.amazonaws.com` as the trusted service.

**Purpose:** Allows EC2 instances running as EKS worker nodes to assume this IAM role and obtain the permissions required to operate within the cluster.

## 5. Attaching IAM Policies to the Node Role

```hcl
resource "aws_iam_role_policy_attachment" "node_policy" {
  for_each = toset([
    "arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy",
    "arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy",
    "arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly"
  ])

  policy_arn = each.value
  role       = aws_iam_role.node.name
}
```

**Explanation:**
- Attaches multiple AWS-managed IAM policies to the worker node role.
- `toset()` converts the list of policy ARNs into a set of unique values.
- `for_each` creates one policy attachment for each policy in the set.
- `each.value` references the current policy ARN.
- `role` specifies the worker node IAM role.

**Purpose of Each Policy:**

1. **AmazonEKSWorkerNodePolicy:** Provides worker nodes with permissions needed to communicate with and register with the EKS cluster.
2. **AmazonEKS_CNI_Policy:** Provides permissions for the Amazon VPC CNI plugin to manage networking resources, when this policy is assigned to the node role.
3. **AmazonEC2ContainerRegistryReadOnly:** Allows worker nodes to retrieve container images from Amazon ECR.

## 6. Creating an EKS Managed Node Group

```hcl
resource "aws_eks_node_group" "main" {
  for_each = var.node_groups

  cluster_name    = aws_eks_cluster.main.name
  node_group_name = each.key
  node_role_arn   = aws_iam_role.node.arn
  subnet_ids
