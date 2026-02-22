# Dev Cluster - Sample App

A simple Helm chart for testing ArgoCD synchronization on the dev cluster.

## Chart Contents

- **Deployment**: Runs 2 replicas of nginx
- **Service**: ClusterIP service on port 80
- **ConfigMap**: Configuration data for the application

## Local Testing

```bash
# Template the chart
helm template sample-app dev/sample-app/

# Install directly (without ArgoCD)
helm install sample-app dev/sample-app/ -n dev --create-namespace
```

## ArgoCD Sync

This application is synced by ArgoCD. See `argocd/dev-sample-app.yaml` for the Application manifest.
