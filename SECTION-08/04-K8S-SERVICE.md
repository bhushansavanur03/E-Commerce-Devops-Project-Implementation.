# Kubernetes Services

## What is a Kubernetes Service?

A Kubernetes Service is an abstraction that provides a stable network endpoint for accessing a group of Pods.

Pods are temporary resources. They can be created, deleted, or replaced, and their IP addresses may change during their lifecycle.

A Service solves this problem by providing a consistent IP address and DNS name through which other applications can communicate with the Pods.

## How Does Service Discovery Work?

Kubernetes Services simplify communication between application components by providing the following capabilities:

1. **Stable Endpoint:** Provides a consistent IP address and DNS name, even when the underlying Pods are replaced.
2. **Load Balancing:** Distributes network traffic across the healthy backend Pods selected by the Service.
3. **Service Discovery:** Allows applications to discover Services using DNS names or environment variables.

For example, if a frontend application needs to communicate with a backend application, it can use the backend Service name instead of depending on individual Pod IP addresses.

## Types of Kubernetes Services

### 1. ClusterIP

- Default Kubernetes Service type.
- Exposes the Service using an internal cluster IP.
- Allows communication between applications inside the Kubernetes cluster.
- Not directly accessible from outside the cluster.

**Use case:** Frontend-to-backend or backend-to-database communication within a cluster.

### 2. NodePort

- Exposes the Service on a specific port on each Kubernetes node.
- Allows external clients to access the application using a node IP and NodePort.
- The default NodePort range is `30000–32767`.

**Use case:** Basic external access for testing or development.

### 3. LoadBalancer

- Exposes the Service through an external load balancer.
- In supported cloud environments, Kubernetes integrates with the cloud provider to provision the load balancer.
- Commonly used to expose applications to external users.

**Use case:** Exposing an application running on an Amazon EKS cluster through an AWS load balancer.

### 4. ExternalName

- Maps a Kubernetes Service to an external DNS name.
- Uses a DNS CNAME record instead of selecting Pods.
- Does not create a proxy or load balancer for the external destination.

**Use case:** Allowing applications inside a cluster to access an external service using a Kubernetes Service name.

## Example of a Kubernetes Service

The following YAML defines a ClusterIP Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
  type: ClusterIP
```

### Configuration Explanation

- `apiVersion: v1` – Specifies the Kubernetes API version.
- `kind: Service` – Defines the resource as a Service.
- `metadata.name` – Sets the Service name.
- `selector` – Identifies the Pods using matching labels.
- `protocol: TCP` – Specifies the network protocol.
- `port: 80` – Port exposed by the Service.
- `targetPort: 9376` – Port on which the application listens inside the selected Pods.
- `type: ClusterIP` – Makes the Service accessible internally within the cluster.

**Traffic flow:**

Client Pod → Service IP:80 → Selected Pod IP:9376

## Service Discovery Using DNS

Kubernetes provides an internal DNS mechanism that allows applications to communicate using Service names.

For example, a Pod can access the Service using:

```bash
curl http://my-service
```

Within the same namespace, `my-service` resolves to the Service's cluster IP.

To access a Service in another namespace:

```bash
curl http://my-service.<namespace>.svc.cluster.local
```

## Useful Kubernetes Commands

**Create or update a Service:**

```bash
kubectl apply -f service.yaml
```

**List Services:**

```bash
kubectl get services
```

**Get detailed Service information:**

```bash
kubectl describe service my-service
```

**Check the Pods selected by a Service:**

```bash
kubectl get pods --show-labels
```

**Check Service endpoints:**

```bash
kubectl get endpoints my-service
```

**Check EndpointSlices:**

```bash
kubectl get endpointslices
```

EndpointSlices provide information about the backend endpoints associated with a Service.

## Summary

Kubernetes Services provide stable networking and service discovery for applications running inside a cluster. They allow Pods to communicate without depending on changing Pod IP addresses.

**Quick Revision:**

1. ClusterIP – Internal communication within the cluster.
2. NodePort – Exposes an application through a port on each node.
3. LoadBalancer – Exposes an application through an external load balancer.
4. ExternalName – Maps a Service to an external DNS name.
