# CD Operator Test Manifests

This repository contains test Kubernetes manifests for validating the cd-operator.

## Structure

### Standard Component Layout (20 components)
```
components/
├── api-gateway/
│   ├── cd.yaml
│   ├── dev/
│   │   ├── conf.yaml
│   │   ├── kustomization.yaml
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── staging/
│   └── prod/
└── ... (19 more components)
```

**Pattern**: `components/<component-name>/<environment>/`

**Environments**: dev, staging, prod

**Components**: Lightweight nginx deployments simulating microservices

### TMC Component Layout (20 components)
```
components-tmc/
├── data-processor/
│   ├── cd.yaml
│   ├── cluster-us-east-1/
│   │   ├── conf.yaml
│   │   ├── kustomization.yaml
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── cluster-us-west-2/
│   └── cluster-eu-west-1/
└── ... (19 more components)
```

**Pattern**: `components-tmc/<component-name>/<cluster>/`

**Clusters**: cluster-us-east-1, cluster-us-west-2, cluster-eu-west-1

**Components**: Lightweight nginx deployments for TMC testing

## Testing with cd-operator

### Standard Layout Qualification
PRs modifying files in `components/<component>/<env>/` will be detected as "standard" layout.

### TMC Layout Qualification
PRs modifying files in `components-tmc/<component>/<cluster>/` will be detected as "TMC" layout.

## Resource Requirements

All deployments use minimal resources:
- CPU Request: 100m
- Memory Request: 128Mi
- CPU Limit: 200m
- Memory Limit: 256Mi

**Total Resources (all 40 components, all environments)**:
- Standard: 20 components × 3 environments × 1-3 replicas = ~80 pods
- TMC: 20 components × 3 clusters × 2 replicas = 120 pods
- **Total**: ~200 pods (if all deployed)

Each pod: nginx:1.25-alpine (~10MB image, ~20MB RAM actual usage)

## cd-operator Validation Scenarios

1. **Layout Detection**: Verify standard vs TMC pattern recognition
2. **PR Qualification**: Test file pattern validation
3. **Mergeable Check**: Validate GitHub mergeable state
4. **Auto-merge**: Test successful merge of qualified PRs
5. **Drift Detection**: Monitor ArgoCD sync status after merge
