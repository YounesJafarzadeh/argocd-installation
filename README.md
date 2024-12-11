## How to Deploy to Kubernetes using Argo CD and GitOps
Here's a concise explanation for deploying to Kubernetes using Argo CD and GitOps that you can use in your README file:

### Prerequisites
- A Kubernetes cluster (with kubectl access)
- An Ingress controller installed in the cluster (e.g., NGINX Ingress Controller)
- A DNS name pointing to your Ingress controller's external IP (e.g., argocd.test.local) 
### Step1-Installing Argo CD on Your Cluster
Install Argo CD in a new namespace called `argocd`:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
watch kubectl get pods -n argocd
```
By default, there should be five pods that eventually receive the Running status as part of a stock Argo CD installation.
### Step2-Expose Argo CD using Ingress
``` bash
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
### Step3-Login to Argo CD
Get the initial admin password:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```
You can then log into your Argo CD dashboard by going back to Your_IP Address:8080 in a browser and logging in as the admin user with your own password





