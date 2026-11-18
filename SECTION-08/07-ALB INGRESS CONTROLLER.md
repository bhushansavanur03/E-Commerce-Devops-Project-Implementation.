# How to Set Up AWS Load Balancer Controller on EKS

## 1. Overview

The AWS Load Balancer Controller is a Kubernetes controller that integrates an Amazon EKS cluster with AWS Elastic Load Balancing services.

It automatically provisions and manages AWS load balancers based on Kubernetes resources.

For example:

- Kubernetes Ingress resources can be used to provision AWS Application Load Balancers (ALBs).
- Kubernetes Services of type `LoadBalancer` can be used to provision AWS Network Load Balancers (NLBs), depending on the configuration.

## 2. Prerequisites

Before installing the controller, ensure that the following tools and resources are available:

- A running Amazon EKS cluster.
- AWS CLI installed and configured.
- `kubectl` configured to access the EKS cluster.
- `eksctl` installed.
- Helm installed.
- Appropriate AWS IAM permissions.

Set the cluster name:

```bash
export cluster_name=demo-cluster
```

Retrieve your AWS account ID:

```bash
aws sts get-caller-identity --query Account --output text
```

## 3. Configure the IAM OIDC Provider

### What is OIDC?

OIDC (OpenID Connect) allows Kubernetes workloads to obtain AWS IAM permissions through an associated IAM role without storing long-term AWS access keys inside containers.

The AWS Load Balancer Controller uses this mechanism through a Kubernetes ServiceAccount and IAM role.

### Step 1: Retrieve the OIDC ID

```bash
oidc_id=$(aws eks describe-cluster \
  --name $cluster_name \
  --query "cluster.identity.oidc.issuer" \
  --output text | cut -d '/' -f 5)
```

**Command explanation:**

- `aws eks describe-cluster` – Retrieves EKS cluster information.
- `--name` – Specifies the cluster name.
- `--query` – Extracts the OIDC issuer URL.
- `--output text` – Displays the result as plain text.
- `cut -d '/' -f 5` – Extracts the OIDC ID from the URL.

### Step 2: Check whether an IAM OIDC provider already exists

```bash
aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4
```

If the command returns the OIDC ID, an IAM OIDC provider is already configured.

If no matching provider exists, associate one with the cluster.

### Step 3: Associate the OIDC provider

```bash
eksctl utils associate-iam-oidc-provider \
  --cluster $cluster_name \
  --approve
```

This associates an IAM OIDC provider with the EKS cluster, enabling IAM roles to trust identities from Kubernetes ServiceAccounts.

## 4. Download the IAM Policy

The AWS Load Balancer Controller requires specific IAM permissions to manage AWS networking resources.

Download the IAM policy:

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

This downloads the IAM policy JSON file from the AWS Load Balancer Controller GitHub repository.

**Important:** Use the policy corresponding to the controller release you intend to install. Keep the policy and controller versions compatible.

## 5. Create the IAM Policy

Create an IAM policy using the downloaded JSON file:

```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

**Command explanation:**

- `create-policy` – Creates a new IAM policy.
- `--policy-name` – Specifies the policy name.
- `--policy-document` – Specifies the JSON policy document.
- `file://iam_policy.json` – Reads the policy from the local file.

The policy provides the permissions required by the AWS Load Balancer Controller to manage load balancers and related AWS resources.

## 6. Create an IAM Role and Kubernetes ServiceAccount

Use `eksctl` to create an IAM role and associate it with a Kubernetes ServiceAccount.

```bash
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

**Command explanation:**

- `--cluster` – Specifies the EKS cluster.
- `--namespace` – Creates the ServiceAccount in the `kube-system` namespace.
- `--name` – Specifies the Kubernetes ServiceAccount name.
- `--role-name` – Specifies the IAM role name.
- `--attach-policy-arn` – Attaches the IAM policy to the role.
- `--approve` – Approves the creation and association.

The IAM role allows the controller to access the required AWS services through its Kubernetes ServiceAccount.

## 7. Install AWS Load Balancer Controller Using Helm

Helm is a Kubernetes package manager that simplifies the installation and management of Kubernetes applications.

### Step 1: Add the AWS EKS Helm Repository

```bash
helm repo add eks https://aws.github.io/eks-charts
```

This adds the AWS EKS Helm chart repository.

### Step 2: Update the Helm Repository

```bash
helm repo update eks
```

This downloads the latest chart information from the repository.

### Step 3: Install the Controller

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>
```

**Command explanation:**

- `helm install` – Installs a Helm chart.
- `aws-load-balancer-controller` – Specifies the Helm release name.
- `eks/aws-load-balancer-controller` – Specifies the chart.
- `-n kube-system` – Installs the controller in the `kube-system` namespace.
- `clusterName` – Specifies the EKS cluster name.
- `serviceAccount.create=false` – Prevents Helm from creating another ServiceAccount.
- `serviceAccount.name` – Uses the ServiceAccount created earlier.
- `region` – Specifies the AWS region.
- `vpcId` – Specifies the VPC associated with the EKS cluster.

## 8. Verify the Controller Installation

Check the controller Deployment:

```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

Check the controller Pods:

```bash
kubectl get pods -n kube-system
```

Check the controller logs:

```bash
kubectl logs -n kube-system deployment/aws-load-balancer-controller
```

The controller should be running successfully before you create an Ingress resource that depends on it.

## 9. Troubleshooting: ALB Address Not Appearing in Ingress

### Problem

After creating an Ingress resource, running:

```bash
kubectl get ingress -n robot-shop
```

may show an empty `ADDRESS` field.

One possible cause is that the AWS Load Balancer Controller does not have all the required IAM permissions.

For example, the IAM policy may be missing:

```text
elasticloadbalancing:DescribeListenerAttributes
```

### Step 1: Inspect the Current IAM Policy

Retrieve the default policy version:

```bash
aws iam get-policy-version \
  --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --version-id $(aws iam get-policy \
    --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --query 'Policy.DefaultVersionId' \
    --output text)
```

Check whether the policy includes:

```text
elasticloadbalancing:DescribeListenerAttributes
```

### Step 2: Download the Current Policy Document

```bash
aws iam get-policy-version \
  --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --version-id $(aws iam get-policy \
    --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --query 'Policy.DefaultVersionId' \
    --output text) \
  --query 'PolicyVersion.Document' \
  --output json > policy.json
```

This saves the current default IAM policy document into `policy.json`.

### Step 3: Edit the IAM Policy

Open `policy.json` and add the missing permission inside its existing `Statement` array:

```json
{
  "Effect": "Allow",
  "Action": "elasticloadbalancing:DescribeListenerAttributes",
  "Resource": "*"
}
```

**Important:** Add this statement to the existing policy document. Do not replace the entire policy with this single statement.

### Step 4: Create a New IAM Policy Version

```bash
aws iam create-policy-version \
  --policy-arn arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://policy.json \
  --set-as-default
```

This creates a new version of the IAM policy and makes it the default version.

### Step 5: Verify the Ingress Resource

Check the Ingress:

```bash
kubectl get ingress -n robot-shop
```

Check its detailed events:

```bash
kubectl describe ingress <ingress-name> -n robot-shop
```

If the ALB address is still missing, check the controller logs and Kubernetes events for other errors, including subnet discovery, IAM permissions, and Ingress configuration issues.

## Important Implementation Notes

- The AWS Load Balancer Controller is installed inside the Kubernetes cluster as a Deployment.
- Its Kubernetes ServiceAccount is associated with an AWS IAM role using OIDC.
- The IAM policy defines which AWS operations the controller can perform.
- The Ingress resource defines how incoming HTTP/HTTPS traffic should be routed.
- An empty Ingress address is not always an IAM issue; subnet tags, permissions, controller logs, and Ingress configuration should also be checked.
- IAM customer-managed policies support a maximum of five versions. Delete an old non-default version if you reach this limit before creating another version.
- Pin compatible Helm chart and controller versions for reproducible deployments.

## Summary

The AWS Load Balancer Controller connects Kubernetes Ingress resources with AWS load balancing infrastructure.

**Quick Revision:**

1. OIDC connects Kubernetes ServiceAccounts with AWS IAM roles.
2. Helm installs the AWS Load Balancer Controller into the EKS cluster.
3. The controller uses IAM permissions to provision and manage AWS load balancers.
4. Ingress resources define the routing rules used by the ALB.
