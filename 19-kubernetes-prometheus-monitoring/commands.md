# Commands — Lab 19: Monitor Kubernetes with Prometheus Using Helm

---

## 🟦 Step 1 — Verify Kubernetes Cluster

```bash
kubectl get nodes
kubectl get pods -A
```

---

## 🟦 Step 2 — Verify Helm Installation

```bash
helm version
```

---

## 🟦 Step 3 — Add Prometheus Helm Repository

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update
```

---

## 🟦 Step 4 — Create Monitoring Namespace

```bash
kubectl create namespace monitoring

# Verify
kubectl get ns
```

---

## 🟦 Step 5 — Install Prometheus Monitoring Stack

```bash
helm install monitoring prometheus-community/kube-prometheus-stack \
  -n monitoring \
  --set alertmanager.enabled=false \
  --timeout 15m
```

> **Note:** Timeout is set to 15 minutes because monitoring images and Kubernetes resources may take longer to initialize in lab environments. Alertmanager is disabled to keep the setup lighter for learning purposes.

### Watch pods initialize in real time (separate terminal)
```bash
kubectl get pods -n monitoring -w
```

---

## 🟦 Step 6 — Verify Helm Release and Monitoring Components

```bash
# Verify Helm release
helm list -n monitoring

# Verify monitoring pods
kubectl get pods -n monitoring

# Verify monitoring services
kubectl get svc -n monitoring
```

---

## 🟦 Step 7 — Access Prometheus UI

```bash
# Port-forward Prometheus service (keep this terminal active)
kubectl port-forward --address 0.0.0.0 \
  svc/monitoring-kube-prometheus-prometheus \
  9091:9090 -n monitoring

# Get master node IP (run in a separate terminal)
kubectl get nodes -o wide
```

**Browser URL:**
```
http://<MASTER_NODE_IP>:9091
```

> Example: `http://192.168.56.115:9091`

---

## 🟦 Step 8 — Check Prometheus Targets

In the Prometheus browser UI:
```
Status → Targets
```

> **Note:** Some targets may show DOWN in kubeadm clusters — this is expected. Core targets like node-exporter, kube-state-metrics, Prometheus, and Grafana should show UP.

---

## 🟦 Step 9 — Run PromQL Queries

In the Prometheus browser UI, go to the **Query** section and run queries from [`promql-queries.md`](./promql-queries.md).

---

## 🟦 Step 10 — Access Grafana Dashboard

### Get Grafana admin password
```bash
kubectl get secret monitoring-grafana -n monitoring \
  -o jsonpath="{.data.admin-password}" | base64 -d; echo ""
```

### Port-forward Grafana service (keep this terminal active)
```bash
kubectl port-forward --address 0.0.0.0 \
  svc/monitoring-grafana \
  3000:80 -n monitoring
```

**Browser URL:**
```
http://<MASTER_NODE_IP>:3000
```

> Example: `http://192.168.56.115:3000`

**Login credentials:**
- Username: `admin`
- Password: retrieved from the command above

### Explore dashboards
```
Dashboards → Browse
```

Recommended dashboards to explore:
- `Kubernetes / Compute Resources / Cluster`
- `Kubernetes / Compute Resources / Node (Pods)`

---

## 🟦 Step 11 — Verify Node-Exporter DaemonSet

```bash
kubectl get daemonsets -n monitoring
```

> node-exporter runs as a DaemonSet — one pod per node. DESIRED, CURRENT, and READY should all match the number of cluster nodes.

---

## 🟦 Step 12 — Cleanup

```bash
helm uninstall monitoring -n monitoring
kubectl delete namespace monitoring
```
