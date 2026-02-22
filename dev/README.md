# Dev Cluster Applications

This folder contains 8 lightweight applications for testing ArgoCD synchronization on the dev cluster (kaas-ole-dev).

## Applications

| App | Description | Image | Resources (CPU/Memory) |
|-----|-------------|-------|------------------------|
| **sample-app** | Simple nginx web server | nginx:1.25-alpine | 50m/128Mi limit, 10m/64Mi request |
| **web-frontend** | Hello world web frontend | nginxdemos/hello | 50m/64Mi limit, 10m/32Mi request |
| **api-backend** | Simple HTTP echo API | hashicorp/http-echo | 30m/32Mi limit, 5m/16Mi request |
| **cache-service** | Redis cache with 32MB limit | redis:7-alpine | 50m/64Mi limit, 10m/32Mi request |
| **message-queue** | Mock message queue | busybox:1.36 | 20m/32Mi limit, 5m/16Mi request |
| **config-service** | HTTP testing service | kennethreitz/httpbin | 40m/48Mi limit, 10m/24Mi request |
| **metrics-collector** | Node exporter metrics | prom/node-exporter:v1.7.0 | 30m/32Mi limit, 5m/16Mi request |
| **health-checker** | Simple health check loop | alpine:3.19 | 20m/32Mi limit, 5m/16Mi request |
| **database-mock** | PostgreSQL test database | postgres:16-alpine | 100m/128Mi limit, 20m/64Mi request |
| **worker-service** | Background worker mock | alpine:3.19 | 20m/32Mi limit, 5m/16Mi request |
| **log-aggregator** | Fluent Bit log collector | fluent/fluent-bit:2.2 | 50m/64Mi limit, 10m/32Mi request |

**Total Resources (all apps):**
- CPU: ~460m limit, ~95m request
- Memory: ~624Mi limit, ~316Mi request

## ArgoCD Applications

Each app has a corresponding ArgoCD Application manifest in `argocd/dev-*.yaml`

## Local Testing

Test any Helm chart locally:
```bash
helm template <app-name> dev/<app-name>/
helm install <app-name> dev/<app-name>/ -n dev --create-namespace
```

## ArgoCD Sync

After pushing to git, ArgoCD will automatically sync all applications to the dev namespace.

Check sync status:
```bash
kubectl get applications -n argocd | grep dev-
kubectl get pods -n dev
```
