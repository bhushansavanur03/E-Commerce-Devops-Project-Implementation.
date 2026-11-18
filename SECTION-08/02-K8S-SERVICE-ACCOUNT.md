# Kubernetes Service Account

## What Is a Service Account in Kubernetes?

A Service Account is an identity provided by Kubernetes that allows applications running inside pods to authenticate with the Kubernetes API server.

It enables pods to interact with Kubernetes resources without requiring individual user credentials.

For example, an application running inside a pod may need permission to:
- Retrieve information about Kubernetes pods.
- Read ConfigMaps or Secrets.
- List services within a namespace.
- Perform specific operations through the Kubernetes API.

A Service Account provides the identity, while RBAC (Role-Based Access Control) defines the permissions associated with that identity.

## Why Are Service Accounts Important?

### 1. Security
Service Accounts provide an application identity without requiring developers to store personal user credentials inside containers.

### 2. Granular Access Control
Service Accounts can be associated with Kubernetes Roles or ClusterRoles through RoleBindings or ClusterRoleBindings.

This allows administrators to control exactly which resources an application can access and which operations it can perform.

### 3. Isolation
Different applications can use separate Service Accounts with different permissions.

For example, one application may only need permission to read ConfigMaps, while another may need permission to list pods.

This follows the principle of least privilege.

## Default Service Account

Kubernetes automatically creates a `default` Service Account in every namespace.

If a pod does not specify a Service Account, Kubernetes assigns the default Service Account from its namespace.

The default Service Account does not automatically have broad permissions. Its effective permissions depend on the RBAC configuration.

For better security, applications should use dedicated Service Accounts with only the permissions they require.

## Creating and Using a Custom Service Account

The following example demonstrates how to assign a custom Service Account to a pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  serviceAccountName: custom-service-account
  containers:
    - name: example-container
      image: example-image
```

**Explanation:**
- `apiVersion: v1` specifies the Kubernetes API version.
- `kind: Pod` defines the Kubernetes resource type.
- `metadata.name` specifies the pod name.
- `serviceAccountName` specifies the Service Account assigned to the pod.
- `containers` defines the application container.
- `image` specifies the container image.

**Note:** The `custom-service-account` must already exist in the same namespace as the pod.

## Useful Kubernetes Commands

Create a Service Account:

```bash
kubectl create serviceaccount custom-service-account
```

List Service Accounts:

```bash
kubectl get serviceaccounts
```

Describe a Service Account:

```bash
kubectl describe serviceaccount custom-service-account
```

Verify the Service Account assigned to a pod:

```bash
kubectl get pod example-pod -o jsonpath='{.spec.serviceAccountName}'
```

## Service Account and RBAC

A Service Account provides an identity, but it does not automatically grant application-specific permissions.

RBAC is used to assign permissions to that identity.

The main RBAC components are:

| Component | Purpose |
|---|---|
| Service Account | Provides an identity to a pod |
| Role | Defines permissions within a namespace |
| ClusterRole | Defines permissions at cluster scope or reusable permissions |
| RoleBinding | Assigns a Role or ClusterRole to an identity within a namespace |
| ClusterRoleBinding | Assign
