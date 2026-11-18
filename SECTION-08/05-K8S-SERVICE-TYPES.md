# Kubernetes Service Types

Kubernetes Services expose applications running on a set of Pods through a stable network endpoint. Different Service types provide different levels of network accessibility depending on the application's requirements.

## 1. ClusterIP

### Description

- Default Kubernetes Service type.
- Exposes the Service through an internal IP address within the cluster.
- Accessible by other workloads inside the cluster.
- Not directly accessible from outside the cluster.

### Use Cases

- Internal communication between microservices.
- Frontend-to-backend communication.
- Backend-to-database communication.

### How It Works

1. Kubernetes assigns a stable internal IP address to the Service.
2. The Service selects Pods using matching labels.
3. Traffic sent to the Service IP is forwarded to one of its selected backend Pods.

**Example traffic flow:**

```text
Frontend Pod
     |
     v
Backend ClusterIP Service
     |
     v
Backend Pod 1 / Backend Pod 2
```

## 2. NodePort

### Description

- Exposes a Service on a static port on each Kubernetes Node.
- Allows external clients to access the application using `<NodeIP>:<NodePort>`.
- Uses the ClusterIP Service internally as well.
- Default NodePort range: `30000–32767`.

### Use Cases

- Basic external access without provisioning a cloud load balancer.
- Development and testing environments.
- Troubleshooting and demonstrations.

### How It Works

1. Kubernetes allocates a NodePort from the configured port range.
2. The port is exposed on the cluster nodes.
3. External traffic reaches a node using its IP address and NodePort.
4. Kubernetes forwards the traffic to an available backend Pod.

**Example traffic flow:**

```text
External Client
      |
      v
Node IP : 30080
      |
      v
NodePort Service
      |
      v
Backend Pods
```

Example access:

```text
http://<NodeIP>:30080
```

## 3. LoadBalancer

### Description

- Exposes a Service externally using a cloud provider's load balancer.
- In supported cloud environments, Kubernetes integrates with the cloud provider to provision the load balancer.
- Provides an external endpoint for accessing the application.
- Commonly used for production workloads requiring external access.

### Use Cases

- Exposing applications to internet users.
- Production applications hosted on AWS EKS or other supported cloud platforms.
- Applications requiring managed external traffic distribution.

### How It Works

1. Create a Service with `type: LoadBalancer`.
2. Kubernetes requests load balancer provisioning through the cloud integration.
3. The cloud provider provisions the load balancer.
4. Incoming traffic is forwarded to the Kubernetes Service and its backend Pods.

**Example traffic flow:**

```text
Internet User
      |
      v
Cloud Load Balancer
      |
      v
Kubernetes Service
      |
      v
Backend Pods
```

In AWS EKS, the AWS Load Balancer Controller can provision an AWS Network Load Balancer for a Service when configured with the appropriate annotations and settings.

## Comparison of Kubernetes Service Types

| Feature | ClusterIP | NodePort | LoadBalancer |
|---|---|---|---|
| Accessibility | Internal cluster | Through node IP and port | External load balancer |
| External access | Not directly | Yes | Yes |
| Default type | Yes | No | No |
| Cloud load balancer | No | No | Usually |
| Common use | Internal communication | Testing and basic access | External production access |

## Conclusion

Choosing the appropriate Service type depends on the application's networking requirements.

- **ClusterIP:** Best for internal communication between application components.
- **NodePort:** Useful for basic external access through Kubernetes nodes.
- **LoadBalancer:** Suitable for exposing applications through a cloud-managed load balancer.

**Quick Revision:**

1. ClusterIP provides an internal stable endpoint.
2. NodePort exposes an application through a port on every node.
3. LoadBalancer integrates with a cloud provider to expose an application externally.
