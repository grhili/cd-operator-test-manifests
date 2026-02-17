# CD Operator Test Manifests

This repository contains test Kubernetes manifests for validating the cd-operator with ArgoCD.

## Structure

### Standard Component Layout (20 components)
```
components/
├── api-gateway/
│   ├── dev/
│   │   └── manifest.yaml      # Single file: Namespace + Deployment + Service
│   ├── staging/
│   │   └── manifest.yaml
│   └── prod/
│       └── manifest.yaml
└── ... (19 more components)
```

**Pattern**: `components/<component>/<environment>/manifest.yaml`

**Environments**: dev, staging, prod

**Components**: 20 microservices (api-gateway, auth-service, user-service, etc.)

### TMC Component Layout (20 components)
```
components-tmc/
├── data-processor/
│   ├── cluster-us-east-1/
│   │   └── manifest.yaml      # Single file: Namespace + Deployment + Service
│   ├── cluster-us-west-2/
│   │   └── manifest.yaml
│   └── cluster-eu-west-1/
│       └── manifest.yaml
└── ... (19 more components)
```

**Pattern**: `components-tmc/<component>/<cluster>/manifest.yaml`

**Clusters**: cluster-us-east-1, cluster-us-west-2, cluster-eu-west-1

**Components**: 20 ML/data services (data-processor, ml-inference, feature-store, etc.)

## ArgoCD Integration

### Directory Structure for ArgoCD Sync
ArgoCD Applications should point to:
- **Standard**: `components/<component>/<environment>/` (syncs manifest.yaml)
- **TMC**: `components-tmc/<component>/<cluster>/` (syncs manifest.yaml)

### Pre-configured Applications
See `argocd-apps/` directory for 120 pre-configured ArgoCD Applications:
- 60 standard component apps (20 components × 3 environments)
- 60 TMC component apps (20 components × 3 clusters)

**Deploy all applications**:
```bash
kubectl apply -f argocd-apps/
```

**Deploy specific component**:
```bash
kubectl apply -f argocd-apps/api-gateway-dev.yaml
kubectl apply -f argocd-apps/data-processor-cluster-us-east-1.yaml
```

## Resource Requirements

All deployments use minimal resources:
- **CPU Request**: 100m
- **Memory Request**: 128Mi
- **CPU Limit**: 200m
- **Memory Limit**: 256Mi
- **Image**: nginx:1.25-alpine (~10MB)

**Replicas**:
- Standard dev/staging: 1 replica
- Standard prod: 3 replicas
- TMC: 2 replicas per cluster

**Total Resources (if all deployed)**:
- Standard: 20 components × (1+1+3) = 100 pods
- TMC: 20 components × 3 clusters × 2 = 120 pods
- **Total**: 220 pods × ~20MB RAM = ~4.4GB RAM

## cd-operator Testing

### Layout Detection Tests
1. **Standard Layout**: Modify files in `components/<name>/<env>/manifest.yaml`
2. **TMC Layout**: Modify files in `components-tmc/<name>/<cluster>/manifest.yaml`

### Test Scenarios
1. **PR Discovery**: Create PR modifying a component's manifest.yaml
2. **Layout Validation**: Operator detects standard vs TMC pattern
3. **File Pattern Check**: Validates changed files match pattern
4. **Mergeable Check**: Verifies PR is ready to merge
5. **Auto-merge**: Qualified PRs are automatically merged
6. **Drift Detection**: Post-merge, monitor ArgoCD sync status

### Example PR Workflow
```bash
# 1. Create branch
git checkout -b test/update-api-gateway

# 2. Modify component (e.g., update image tag)
sed -i '' 's/nginx:1.25-alpine/nginx:1.26-alpine/' components/api-gateway/dev/manifest.yaml

# 3. Commit and push
git add components/api-gateway/dev/manifest.yaml
git commit -m "Update api-gateway dev to nginx 1.26"
git push origin test/update-api-gateway

# 4. Create PR on GitHub
gh pr create --title "Update api-gateway to nginx 1.26" --body "Testing cd-operator"

# 5. Observe cd-operator:
#    - PR gets cd/state:discovered label
#    - Layout validation: standard pattern detected
#    - File count validation: 1 file changed (within limit)
#    - Mergeable check: passes (if no conflicts)
#    - Auto-merge: PR merged automatically
#    - Drift detection: monitors ArgoCD sync status
```

## Statistics

| Metric | Standard | TMC | Total |
|--------|----------|-----|-------|
| Components | 20 | 20 | **40** |
| Environments/Clusters | 3 | 3 | **6** |
| Manifest Files | 60 | 60 | **120** |
| ArgoCD Apps | 60 | 60 | **120** |
| Total Pods (if all deployed) | 100 | 120 | **220** |

## Component List

### Standard Components (20)
- api-gateway, auth-service, user-service, billing-service
- notification-service, analytics-service, search-service, storage-service
- cache-service, queue-service, logging-service, monitoring-service
- metrics-service, tracing-service, admin-portal, customer-portal
- payment-gateway, email-service, sms-service, webhook-service

### TMC Components (20)
- data-processor, ml-inference, feature-store, model-registry
- experiment-tracker, pipeline-orchestrator, data-validator, model-monitor
- drift-detector, ab-testing, recommendation-engine, fraud-detection
- anomaly-detection, time-series-forecast, nlp-service, image-recognition
- speech-to-text, text-to-speech, translation-service, sentiment-analysis

## Notes

- Each manifest.yaml is self-contained (Namespace + Deployment + Service)
- ArgoCD syncs entire directory (only 1 file per directory)
- Namespaces: `dev`, `staging`, `prod` (standard), `tmc` (TMC layout)
- All deployments use nginx as a lightweight test workload
