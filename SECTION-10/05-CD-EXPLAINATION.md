# Continuous Deployment (CD) Using Argo CD

## 1. Introduction

After successfully implementing the CI pipeline, the next step was to implement Continuous Deployment (CD) to deploy the updated application into the target Kubernetes cluster.

In the CI pipeline, we successfully:
- Built the Product Catalog service.
- Performed code quality checks.
- Built and pushed the Docker image to Docker Hub.
- Updated the Kubernetes deployment manifest with the newly generated Docker image tag.

For Continuous Deployment, I used **Argo CD**, a GitOps-based continuous delivery tool for Kubernetes.

Argo CD continuously monitors the Git repository and ensures that the actual state of the Kubernetes cluster matches the desired state defined in Git.

## 2. Installing Argo CD

Initially, I installed Argo CD in my existing Kubernetes cluster running on my EC2 instance.

After installation, I verified whether the Argo CD components were running successfully by checking the pods.

Command:

```bash
kubectl get pods -n argocd
```

This helped me verify the status of the Argo CD components, including the Argo CD server.

## 3. Exposing Argo CD Server Using LoadBalancer

By default, the Argo CD server service is configured with the `ClusterIP` service type, which makes it accessible only within the Kubernetes cluster.

To access the Argo CD web interface externally, I changed the service type of the Argo CD server from `ClusterIP` to `LoadBalancer`.

I modified only the Argo CD server service.

After applying the changes, Kubernetes provisioned a LoadBalancer and provided an external DNS endpoint.

I retrieved the service details using:

```bash
kubectl get svc -n argocd
```

I copied the LoadBalancer DNS endpoint and opened it in my browser to access the Argo CD login interface.

## 4. Logging Into Argo CD

To access the Argo CD dashboard, I used the default administrator username:

- Username: `admin`
- Password: Retrieved from the Argo CD initial administrator password secret.

The initial administrator password is stored in Kubernetes Secrets in Base64-encoded format.

I followed these steps to retrieve and decode the password.

**Step 1: List all Argo CD secrets**

```bash
kubectl get secrets -n argocd
```

This command lists all the Kubernetes secrets available in the `argocd` namespace.

**Step 2: Describe the initial administrator secret**

```bash
kubectl describe secret argocd-initial-admin-secret -n argocd
```

This command displays the details of the initial administrator secret, including the Base64-encoded password under the Data section.

**Step 3: Decode the administrator password**

I copied the encoded password from the previous command and executed:

```bash
echo -n '<PASTE_ENCODED_PASSWORD>' | base64 --decode
```

Explanation:
- `echo -n`: Prints the encoded password without adding a newline.
- `|`: Passes the output of the first command to the next command.
- `base64 --decode`: Decodes the Base64-encoded password into readable text.

**Step 4: Log in to Argo CD**

After decoding the password, I used the following credentials:

- Username: `admin`
- Password: Decoded administrator password.

I successfully logged in to the Argo CD dashboard.

## 5. Creating an Application in Argo CD

After successfully logging in, I created a new application through the Argo CD dashboard.

While creating the application, I configured the following details:

| Configuration | Value |
|---|---|
| Application Name | Product Catalog |
| Repository URL | My GitHub repository URL |
| Revision | main |
| Path | Location of Kubernetes deployment and service manifests |
| Destination Cluster | Default Kubernetes cluster |
| Namespace | default |
| Sync Policy | Automatic |
| Self Heal | Enabled |

I provided the GitHub repository URL and the path containing the Kubernetes manifest files, particularly:

- `deploy.yaml`
- `service.yaml`

This allowed Argo CD to monitor the Kubernetes configuration stored in my Git repository.

## 6. Understanding GitOps and Argo CD

Argo CD follows the GitOps approach, where Git acts as the **Single Source of Truth**.

This means that the desired state of the application and its Kubernetes resources is maintained in the Git repository.

Argo CD continuously monitors the configured repository and compares two states:

1. **Desired State:** The configuration available in the Git repository.
2. **Live State:** The actual configuration running inside the Kubernetes cluster.

Whenever these states differ, Argo CD identifies the difference.

Since I enabled Automatic Sync, Argo CD automatically applies the changes from Git to the Kubernetes cluster.

I also enabled Self Heal, which allows Argo CD to correct configuration drift when the live cluster state is manually modified and differs from the desired Git state.

### How Argo CD Works in This Project

1. The CI pipeline builds a new Docker image.
2. The new image is pushed to Docker Hub.
3. The Kubernetes deployment manifest is updated with the new image tag.
4. The updated manifest is committed and pushed to the GitHub repository.
5. Argo CD detects the changes in the configured Git repository.
6. Argo CD identifies that the desired state and live state are out of sync.
7. With Automatic Sync enabled, Argo CD applies the updated Kubernetes manifests.
8. Kubernetes performs the rolling update and replaces the old application image with the new image.

## 7. Single-Cluster and Multi-Cluster Deployment

In this project, I installed Argo CD inside the Kubernetes cluster running on my EC2 instance.

Since I was working with a single Kubernetes cluster, I configured Argo CD to deploy applications into the same cluster.

However, Argo CD also supports managing multiple Kubernetes clusters from a single Argo CD installation.

This makes it possible to manage deployments across different Kubernetes environments, such as development, staging, and production.

## 8. Verifying the Deployment

After configuring the application and enabling Automatic Sync and Self Heal, I observed the application status in the Argo CD dashboard.

Initially, Argo CD identified that the desired state in Git and the live state in Kubernetes were out of sync.

Argo CD then synchronized the application and applied the updated Kubernetes manifests.

Finally, I verified that the old Docker image was replaced with the newly pushed Docker image.

This confirmed that the Continuous Deployment process was working successfully.

## 9. Final Outcome

Successfully implemented Continuous Deployment using Argo CD.

The complete workflow now works as follows:

- GitHub Actions handles Continuous Integration.
- Docker Hub stores the newly built Docker images.
- GitHub maintains the updated Kubernetes deployment manifests.
- Argo CD monitors the Git repository and synchronizes changes.
- Kubernetes deploys the updated application.

With this implementation, whenever a new application image is built and its tag is updated in the Git repository by the CI pipeline, Argo CD automatically detects the change and deploys the updated application into the Kubernetes cluster.

**Result:** Successfully implemented an automated CI/CD workflow using GitHub Actions, Docker Hub, Kubernetes, and Argo CD.
