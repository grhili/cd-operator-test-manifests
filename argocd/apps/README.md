# Dev Applications - Individual ArgoCD Applications

This directory contains individual ArgoCD Application manifests for each dev service.

Each service uses:
- **Helm Chart**: Located in `dev/<service-name>/`
- **Kustomize**: Can be added for overlays (optional)

## Structure

```
argocd/apps/
├── sample-app.yaml
├── web-frontend.yaml
├── api-backend.yaml
... (one file per service)
```

## Architecture

**Helm + Kustomize Approach:**
1. Each service has a Helm chart in `dev/<service-name>/`
2. ArgoCD Application references the Helm chart
3. Kustomize can be added later for environment-specific overlays

## Deploy All Applications

```bash
kubectl apply -f argocd/apps/
```
