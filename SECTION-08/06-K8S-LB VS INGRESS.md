# Kubernetes: LoadBalancer Service Type vs. Ingress

In Kubernetes, both LoadBalancer Services and Ingress are used to expose applications to external traffic. However, they work differently and serve different networking requirements.

## 1. LoadBalancer Service Type

### Overview

The LoadBalancer Service type exposes a Kubernetes Service externally by requesting a load balancer from a supported cloud provider.

It is commonly used when an application needs its own external load balancer.

### Characteristics

- **Automatic Provisioning:** In supported cloud environments, Kubernetes integrates with the cloud provider to provision a load balancer.
- **Single Service Exposure:** Each LoadBalancer Service exposes one Kubernetes Service.
- **Cloud Provider Integration:** Its implementation and features depend on the cloud provider and its Kubernetes integration.
- **External Endpoint:** Provides an external IP address or hostname through which clients can access the application.
- **TLS Support:** TLS termination can be configured at the load balancer, depending on the provider and configuration.

### Use Cases

- Exposing a single application to the internet.
- Applications that need their own external load balancer.
- Simple external access without advanced HTTP routing requirements.

### Traffic Flow

```text
Internet User
      |
      v
Cloud Load Balancer
      |
      v
Kubernetes LoadBalancer Service
      |
      v
Application Pods
```

## 2. Ingress

### Overview

Ingress is a Kubernetes API resource that manages external HTTP and HTTPS access to Services inside a cluster.

It supports advanced routing rules, allowing multiple applications to be exposed through a common entry point.

**Important:** Creating an Ingress resource alone does not expose an application. An Ingress Controller must be installed and configured to implement the routing rules.

### Characteristics

- **Advanced Routing:** Routes traffic using hostnames and URL paths.
- **Single Entry Point:** Multiple applications can share an external entry point.
- **TLS Termination:** Supports HTTPS configuration and TLS termination through the Ingress Controller.
- **Ingress Controller Required:** Requires a controller such as NGINX Ingress Controller or AWS Load Balancer Controller.
- **Centralized Routing:** Allows multiple application routes to be managed through Ingress resources.

### Use Cases

- Exposing multiple microservices through a common endpoint.
- Host-based routing, such as `api.example.com` and `shop.example.com`.
- Path-based routing, such as `/api` and `/products`.
- Managing HTTPS access and TLS certificates.

### Traffic Flow

```text
                 Internet User
                       |
                       v
              External Load Balancer
                       |
                       v
                Ingress Controller
                       |
              +--------+--------+
              |                 |
              v                 v
        /api Service      /shop Service
              |                 |
              v                 v
          API Pods          Shop Pods
```

## 3. Comparison: LoadBalancer vs. Ingress

| Feature | LoadBalancer Service | Ingress |
|---|---|---|
| Kubernetes resource | Service | Ingress |
| Main purpose | Expose a Service externally | Route HTTP/HTTPS traffic to Services |
| Controller | Cloud integration/controller | Ingress Controller required |
| Multiple applications | Usually separate LoadBalancer Services | Can route multiple Services through one entry point |
| Routing | Primarily Service-level traffic forwarding | Host-based and path-based routing |
| TLS termination | Supported depending on provider | Supported through Ingress Controller |
| Cloud dependency | Depends on cloud integration | Ingress API is cloud-neutral; controller implementation may be provider-specific |
| Common use | External access to one Service | Centralized HTTP/HTTPS routing |

## 4. Practical Example

Suppose an e-commerce application has three microservices:

- Frontend
- Product Catalog
- Recommendation Service

### Using LoadBalancer Services

You could create three separate LoadBalancer Services.

Each Service may provision its own external load balancer, resulting in additional infrastructure and cost.

### Using Ingress

You can expose the applications through a common entry point using routing rules:

| URL path | Backend Service |
|---|---|
| `/` | Frontend Service |
| `/products` | Product Catalog Service |
| `/recommendations` | Recommendation Service |

This allows multiple applications to share an external entry point, subject to the Ingress Controller and its configuration.

## 5. AWS EKS Implementation Note

In Amazon EKS, you may use the AWS Load Balancer Controller to integrate Kubernetes networking resources with AWS load balancers.

Common configurations include:

- **Service type LoadBalancer:** Can provision an AWS Network Load Balancer (NLB) when configured appropriately.
- **Ingress resource:** The AWS Load Balancer Controller can provision an AWS Application Load Balancer (ALB) for HTTP/HTTPS routing.

An ALB can route traffic to multiple Kubernetes Services using host-based and path-based rules.

## Conclusion

Both LoadBalancer Services and Ingress help expose Kubernetes applications externally, but their purposes differ.

- **LoadBalancer:** Provides external access to an individual Kubernetes Service.
- **Ingress:** Provides centralized HTTP/HTTPS routing to multiple Kubernetes Services through an Ingress Controller.

For microservices-based applications, Ingress can reduce the need for multiple external load balancers by sharing an entry point across services.
