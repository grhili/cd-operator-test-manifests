# Prometheus Monitoring Stack

Prometheus stack for collecting logs and metrics from the dev cluster.

## Components

- **Prometheus Server**: Metrics and log collection, storage (10Gi, 7 day retention)
- **Alertmanager**: Alert management (5Gi)
- **Node Exporter**: Node-level metrics and logs
- **Kube State Metrics**: Kubernetes cluster metrics
- **Prometheus Operator**: Manages Prometheus instances

## Configuration

Edit `values.yaml` to customize:
- Retention period (default: 7 days)
- Storage size (default: 10Gi)
- Resource limits
- Monitoring rules

## Access

Prometheus is accessible internally at:
```
http://prometheus-kube-prometheus-prometheus.dev.svc:9090
```

For external access, use port-forward:
```bash
kubectl port-forward -n dev svc/prometheus-kube-prometheus-prometheus 9090:9090
```

Then access the UI at: http://localhost:9090

## Query Examples

View logs and metrics:
- Container CPU usage: `rate(container_cpu_usage_seconds_total[5m])`
- Container memory: `container_memory_working_set_bytes`
- Pod status: `kube_pod_status_phase`
- Node logs: Query via node exporter metrics
