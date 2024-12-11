## How to Deploy to Kubernetes Using Argo CD and GitOps

Argo CD is a declarative, GitOps-based continuous delivery tool that ensures your Kubernetes cluster matches the desired state defined in Git repositories. This guide will help you install and configure Argo CD in your cluster.

---
### Prerequisites
- A Kubernetes cluster (with `kubectl` access)
- An Ingress controller installed in the cluster (e.g., NGINX Ingress Controller)
- A DNS name pointing to your Ingress controller's external IP (e.g., `argocd.test.local`)
---
### Step 1: Installing Argo CD on Your Cluster
Install Argo CD in a new namespace called `argocd`:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
watch kubectl get pods -n argocd
```
By default, five pods will be created, and their status should eventually change to `Running`

---
### Step 2: Expose Argo CD Using Ingress
To expose the Argo CD UI, create an Ingress resource with the following configuration. Replace argocd.test.local with your domain name and your-certificate-name with a valid TLS certificate's Secret name:
```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: argocd
  name: argocd-server
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
spec:
  rules:
  - host: argocd.test.local  # Replace with your domain name
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server
            port:
              name: 443
  tls:
  - hosts:
    - argocd.test.local
    secretName: your-certificate-name
```
Apply the Ingress configuration:

```bash
kubectl apply -f ingress.yaml
```
---
### Step 3: Login to Argo CD
Retrieve the initial admin password:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```
Access the Argo CD dashboard:
- URL: https://argocd.test.local (replace with your configured domain)
- Username: `admin`
- Password: The password retrieved in the previous step.

You can now start using Argo CD to manage your Kubernetes deployments via GitOps!





