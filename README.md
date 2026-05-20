


# 1. Create a Git Repository Structure

* Example:
```
hello-world/
├── deployment.yaml
├── service.yaml
└── argocd-app.yaml
```
# 2. Kubernetes Deployment

* deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: nginx:latest
        ports:
        - containerPort: 80
# 3. Kubernetes Service

* service.yaml

apiVersion: v1
kind: Service
metadata:
  name: hello-world-service
spec:
  selector:
    app: hello-world
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
# 4. ArgoCD Application Manifest

* argocd-app.yaml

Replace:

YOUR_GIT_REPO_URL
branch if needed

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hello-world
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/YOUR_GIT_REPO_URL.git
    targetRevision: HEAD
    path: .

  destination:
    server: https://kubernetes.default.svc
    namespace: default

  syncPolicy:
    automated:
      prune: true
      selfHeal: true
# 5. Install ArgoCD

* Official docs:

* Argo CD Getting Started

* Quick install:
```
kubectl create namespace argocd
```
```
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```  
# 6. Apply the ArgoCD Application
```
kubectl apply -f argocd-app.yaml
```
# 7. Check Application Status
```
kubectl get applications -n argocd
```
# 8. Access ArgoCD UI

* Port forward:
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
* Open:
```
https://localhost:8080
```
* Get admin password:
```
kubectl get secret argocd-initial-admin-secret \
  -n argocd \
  -o jsonpath="{.data.password}" | base64 -d
```
What Happens

# ArgoCD will:

* Watch your Git repository
* Detect Kubernetes YAML files
* Sync them automatically to the cluster
* Keep cluster state aligned with Git

# This is the core GitOps workflow.

* Example Workflow
* Update replicas:
* replicas: 3
* Commit + push to Git:
```
git add .
git commit -m "scale app"
git push
```
* ArgoCD automatically syncs the cluster.

# Recommended Next Steps

* After this hello-world setup, usually people move to:

* Helm-based apps
* Kustomize overlays
* Multiple environments (dev/stage/prod)
* App of Apps pattern
* ArgoCD Image Updater
* Private Git repos
* RBAC & SSO