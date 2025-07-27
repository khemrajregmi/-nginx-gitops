# Nginx GitOps Demo

A complete GitOps workflow demonstration using Nginx, Docker, Kubernetes, and ArgoCD.

## 🎯 Project Overview

This project demonstrates a GitOps-driven deployment pipeline for a custom Nginx web application running on Kubernetes. It showcases:

- **Containerization**: Custom Docker image with Nginx
- **Kubernetes Deployment**: Declarative manifests for deployment and service
- **GitOps**: Automated synchronization using ArgoCD
- **Local Development**: Minikube for local Kubernetes cluster

## 📁 Project Structure

```
nginx-gitops/
├── Dockerfile                 # Docker image configuration
├── index.html                 # Custom HTML content
├── nginx-app.yaml            # ArgoCD application definition
├── manifests/                # Kubernetes manifests
│   ├── deployment.yaml       # Nginx deployment configuration
│   ├── service.yaml          # Service configuration
│   └── kustomization.yaml    # Kustomize configuration
└── README.md                 # This file
```

## 🚀 Quick Start

### Prerequisites

- Docker installed and running
- Minikube installed
- kubectl configured
- Git repository (this repo) accessible

### Step 1: Build and Test Docker Image

```bash
# Build the custom nginx image
docker build -t custom-nginx-gitops .

# Test locally
docker run -d -p 8080:80 custom-nginx-gitops

# Access at http://localhost:8080
```

### Step 2: Push to Docker Hub

```bash
# Tag the image
docker tag custom-nginx-gitops khemrajregmi/custom-nginx-gitops:v1

# Push to Docker Hub
docker push khemrajregmi/custom-nginx-gitops:v1
```

### Step 3: Set Up Local Kubernetes

```bash
# Start Minikube
minikube start

# Verify cluster
kubectl get nodes
```

### Step 4: Install ArgoCD

```bash
# Create ArgoCD namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for pods to be ready
kubectl wait --for=condition=available --timeout=300s deployment/argocd-server -n argocd
```

### Step 5: Access ArgoCD UI

```bash
# Port forward ArgoCD server
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Access ArgoCD at: https://localhost:8080
- Username: `admin`
- Password: (from the command above)

### Step 6: Deploy Application with ArgoCD

```bash
# Apply the ArgoCD application
kubectl apply -f nginx-app.yaml

# Check application status
kubectl get applications -n argocd
```

### Step 7: Access Your Application

```bash
# Get service details
kubectl get svc custom-nginx-svc

# Access via Minikube
minikube service custom-nginx-svc
```

## 🔧 Configuration Details

### Docker Configuration

- **Base Image**: `nginx:alpine` (lightweight Alpine Linux)
- **Custom Content**: Custom HTML file copied to nginx document root
- **Port**: Exposes port 80

### Kubernetes Manifests

- **Deployment**: 2 replicas of the nginx container
- **Service**: NodePort service for external access
- **Kustomization**: Manages the deployment and service resources

### ArgoCD Application

- **Source**: This GitHub repository (`manifests/` directory)
- **Destination**: Default namespace in local Kubernetes cluster
- **Sync Policy**: Automated with pruning and self-healing enabled

## 🔄 GitOps Workflow

1. **Code Changes**: Update application code or Kubernetes manifests
2. **Git Push**: Push changes to this repository
3. **ArgoCD Sync**: ArgoCD automatically detects changes and syncs
4. **Deployment**: New version deployed to Kubernetes cluster
5. **Verification**: Check application status in ArgoCD UI

## 📝 Customization

### Update HTML Content

1. Modify `index.html`
2. Rebuild Docker image with new tag
3. Update image tag in `manifests/deployment.yaml`
4. Commit and push changes
5. ArgoCD will automatically deploy the new version

### Scale Application

Update replicas in `manifests/deployment.yaml`:

```yaml
spec:
  replicas: 3  # Change from 2 to 3
```

## 🛠️ Troubleshooting

### Common Issues

1. **ArgoCD Application Not Syncing**
   ```bash
   # Check application status
   kubectl describe application custom-nginx-app -n argocd
   ```

2. **Pods Not Starting**
   ```bash
   # Check pod logs
   kubectl logs -l app=custom-nginx
   ```

3. **Service Not Accessible**
   ```bash
   # Check service and endpoints
   kubectl get svc,endpoints
   ```

### Useful Commands

```bash
# Check all resources
kubectl get all

# Check ArgoCD applications
kubectl get applications -n argocd

# Check pod logs
kubectl logs -f deployment/custom-nginx

# Restart deployment
kubectl rollout restart deployment/custom-nginx
```

## 🧹 Cleanup

```bash
# Delete ArgoCD application
kubectl delete -f nginx-app.yaml

# Delete ArgoCD
kubectl delete namespace argocd

# Stop Minikube
minikube stop

# Delete Minikube cluster
minikube delete
```

## 📚 Technologies Used

- **Docker**: Container platform
- **Kubernetes**: Container orchestration
- **Minikube**: Local Kubernetes development
- **ArgoCD**: GitOps continuous delivery
- **Nginx**: Web server
- **Kustomize**: Kubernetes configuration management

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make changes
4. Test locally
5. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

**Happy GitOps-ing! 🚀**
