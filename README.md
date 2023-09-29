# GITOPS AT SCALE

### Step up ArgoCD with multiple clusters
- Step 1: Create the first cluster and install ArgoCD

```
// Connect with cluster
aws eks --region us-east-1 describe-cluster --name {cluster name} --query cluster.status

aws eks --region us-east-1 update-kubeconfig --name {cluster name}

// Install ArgoCD
kubectl create namespace argocd

kubectl apply -n argocd -f  https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

// Expose Load Balancer URL for ArgoCD
kubectl edit svc argocd-server -n argocd

Find and Change:
type: ClusterIP -> LoadBalancer

// Get default password to access to ArgoCD UI
kubectl  -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
```

- Step 2: Create the second cluster
```
// Deploy application with ApplicationSet
// Connect with cluster
aws eks --region us-east-1 describe-cluster --name {second cluster name} --query cluster.status

aws eks --region us-east-1 update-kubeconfig --name {second cluster name}

// Install ApplicationSet
kubectl config use-context {second cluster context name}

kubectl create namespace external-argocd

kubectl apply -n external-argocd -f https://raw.githubusercontent.com/argoproj/applicationset/v0.4.1/manifests/install.yaml
```

- Step 3: Access to ArgoCD and create a new Application