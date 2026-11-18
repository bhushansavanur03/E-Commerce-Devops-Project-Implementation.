# Kubernetes Deployment

## What is a Kubernetes Deployment?

A Kubernetes Deployment is a Kubernetes resource that manages the lifecycle of application Pods. It allows you to declaratively define the desired state of an application, including:

- Container image to be used.
- Number of Pod replicas.
- Pod labels and selectors.
- Update strategy.

The Deployment controller continuously monitors the actual state of the application and works to maintain the desired state.

## Kubernetes Deployment vs. Docker Container

| Kubernetes Deployment | Docker Container |
|---|---|
| Declarative management of application workloads. | Containers are typically managed using Docker commands or automation tools. |
| Supports replica-based scaling. | Scaling requires manual commands or additional orchestration tools. |
| Replaces failed or deleted Pods through controllers. | Docker restart policies can restart containers, but do not provide Deployment-level orchestration. |
| Supports rolling updates and rollback. | Updating containers generally requires replacing the existing container. |
| Manages application workloads across a Kubernetes cluster. | Runs an individual container on a host. |

## Scaling and Self-Healing in Kubernetes

### 1. Scaling

Kubernetes Deployments make it easy to increase or decrease the number of application replicas.

Example Deployment YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app-container
          image: my-app-image:latest
```

**Configuration explanation:**

- `apiVersion: apps/v1` – Specifies the Kubernetes API version.
- `kind: Deployment` – Defines the resource as a Deployment.
- `metadata.name` – Sets the Deployment name.
- `replicas: 3` – Specifies that three Pod replicas should run.
- `selector.matchLabels` – Identifies the Pods managed by the Deployment.
- `template` – Defines the Pod configuration.
- `containers` – Specifies the container name and image.

**Apply the Deployment:**

```bash
kubectl apply -f deployment.yaml
```

**Check the Deployment and Pods:**

```bash
kubectl get deployments
kubectl get pods
```

**Scale the Deployment:**

```bash
kubectl scale deployment my-app --replicas=5
```

This increases the desired replica count from three to five.

### 2. Self-Healing

Kubernetes continuously monitors the desired state of a Deployment.

If a Pod is deleted or fails, the ReplicaSet associated with the Deployment detects that the actual number of replicas is below the desired count and creates replacement Pods.

**Test self-healing:**

```bash
kubectl delete pod <pod-name>
```

Check the Pods again:

```bash
kubectl get pods
```

Kubernetes automatically creates a replacement Pod to maintain the configured replica count.

### 3. Rolling Updates

A Deployment supports rolling updates, allowing an application to be updated gradually without terminating all existing Pods at once.

For example, update the container image:

```bash
kubectl set image deployment/my-app my-app-container=my-app-image:v2
```

Check the rollout status:

```bash
kubectl rollout status deployment/my-app
```

View rollout history:

```bash
kubectl rollout history deployment/my-app
```

Rollback to the previous revision if required:

```bash
kubectl rollout undo deployment/my-app
```

## Important Implementation Notes

- A Deployment manages ReplicaSets, and ReplicaSets maintain the desired number of Pods.
- A Deployment does not directly manage containers; it manages Pods containing containers.
- Self-healing maintains the desired replica count, but it does not guarantee that the application itself is functioning correctly. Readiness and liveness probes help monitor application health.
- Rolling updates can reduce downtime, but zero downtime is not guaranteed. Proper readiness probes and sufficient available replicas are important.

## Summary

A Kubernetes Deployment provides declarative application management, replica scaling, self-healing, rolling updates, and rollback capabilities.

**Quick Revision:**

1. Deployment manages ReplicaSets, which maintain the desired number of Pods.
2. `replicas` defines how many Pod instances should run.
3. Rolling updates allow gradual application updates, while rollback helps restore an earlier revision.
