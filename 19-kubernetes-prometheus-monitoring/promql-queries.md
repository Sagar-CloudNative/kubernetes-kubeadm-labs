# PromQL Queries — Lab 19: Kubernetes Prometheus Monitoring

---

## Query 1 — Node CPU Usage

### Raw metric
```promql
rate(node_cpu_seconds_total[5m])
```
Shows raw CPU usage across all modes — idle, system, iowait, user.

### Optimized — CPU utilization percentage
```promql
100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```
Calculates approximate CPU usage percentage per node. Switch to the **Graph** tab to visualize over time.

---

## Query 2 — Container Memory Usage

### Raw metric
```promql
container_memory_usage_bytes
```
Shows memory usage per container in bytes. Output includes pod names, namespaces, container IDs, and node details.

### Optimized — Node memory utilization percentage
```promql
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100
```
Calculates approximate memory utilization percentage at the node level — cleaner and more meaningful for monitoring.

---

## Query 3 — Pod Status

### Raw metric
```promql
kube_pod_status_phase
```
Shows current phase of all Kubernetes pods. A value of `1` means the pod is in that state.

### Filtered — Running pods only
```promql
kube_pod_status_phase{phase="Running"}
```
Filters output to show only running pods.

### Optimized — Pod count by namespace and phase
```promql
sum by(namespace, phase) (kube_pod_status_phase)
```
Groups pod counts by namespace and phase. Clean and professional output.

**Example output:**
```
namespace="default"    phase="Running"  5
namespace="monitoring" phase="Running"  7
```
