# ArgoCD Installation

This folder contains the manifests to deploy ArgoCD itself.

## Deploy ArgoCD

```bash
kubectl apply -k argocd/install/
```

## Access ArgoCD

Get the initial admin password:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Port forward to access the UI:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then access: https://localhost:8080

## Sync Applications

After ArgoCD is running, apply the application manifests:
```bash
kubectl apply -f argocd/argocd-app.yaml
```
