# Connect to EKS Cluster Using AWS CLI and kubectl

After provisioning the EKS cluster using Terraform, the next step is to connect to it from the local machine.

AWS CLI is used to authenticate with AWS and retrieve EKS cluster connection details, while `kubectl` is used to interact with the Kubernetes cluster.

## Step 1: Download and Install AWS CLI

Download and install AWS CLI by following the official AWS documentation:

[Install or update the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Verify the installation:

```bash
aws --version
```

This command displays the installed AWS CLI version.

## Step 2: Configure AWS CLI

Configure AWS CLI with your AWS credentials:

```bash
aws configure
```

You will be prompted to enter:

- AWS Access Key ID
- AWS Secret Access Key
- Default region name
- Default output format

Provide the credentials associated with an IAM identity that has permission to access the EKS cluster.

Verify AWS authentication:

```bash
aws sts get-caller-identity
```

This command displays the AWS account and IAM identity associated with your configured credentials.

## Step 3: Update kubeconfig with the EKS Cluster

Run the following command:

```bash
aws eks --region <your-region> update-kubeconfig --name <your-cluster-name>
```

Replace:
- `<your-region>` with the AWS region where your EKS cluster is deployed.
- `<your-cluster-name>` with the name of your EKS cluster.

**Explanation:**
- `aws eks` invokes the Amazon EKS service through AWS CLI.
- `--region` specifies the cluster's AWS region.
- `update-kubeconfig` retrieves cluster connection information and updates the local Kubernetes configuration file.
- `--name` specifies the EKS cluster name.

By default, the kubeconfig file is stored at:

```text
~/.kube/config
```

This file contains the cluster endpoint, certificate information, and authentication configuration required by kubectl.

## Step 4: Verify the EKS Cluster Connection

Run:

```bash
kubectl get nodes
```

This command retrieves the worker nodes registered with the Kubernetes cluster.

If the connection is successful, you should see the node names, statuses, roles, ages, and Kubernetes versions.

Example:

```text
NAME                          STATUS   ROLES    AGE   VERSION
ip-10-0-1-100.ec2.internal    Ready    <none>   10m   v1.xx.x
ip-10-0-2-100.ec2.internal    Ready    <none>   10m   v1.xx.x
```

The `Ready` status indicates that the nodes are healthy and available to run workloads.

## Useful Verification Commands

Check the current Kubernetes context:

```bash
kubectl config current-context
```

List available contexts:

```bash
kubectl config get-contexts
```

Check cluster information:

```bash
kubectl cluster-info
```

List all nodes:

```bash
kubectl get nodes -o wide
```

## Outcome

The local machine is successfully configured to communicate with the Amazon EKS cluster.

AWS CLI handles AWS authentication and EKS kubeconfig configuration, while kubectl provides the interface to manage Kubernetes resources.
