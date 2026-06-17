# Lab 19 — Monitor Kubernetes with Prometheus Using Helm

[![Kubernetes](https://img.shields.io/badge/Kubernetes-Labs-blue)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs)
[![License](https://img.shields.io/badge/License-MIT-green)](../LICENSE)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?logo=youtube)](https://www.youtube.com/@TechOpsTutorials)

> **Part of the [Kubernetes Real-World Labs (kubeadm)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs) series**

---

## 🎯 What You'll Learn

- Why monitoring is critical in Kubernetes environments
- What Prometheus is and how it collects metrics
- How Grafana visualizes Kubernetes metrics using dashboards
- How to deploy the full monitoring stack using Helm
- How to run basic PromQL queries to analyze cluster metrics

---

## 🧠 Concept Overview

### Why Monitoring Matters
In real Kubernetes environments, pods crash, applications slow down, and nodes fail — often in the background without any visible warning. Without monitoring, troubleshooting these issues becomes extremely difficult.

### Metrics vs Logs vs Traces

| Type | Description | Tool |
|------|-------------|------|
| **Metrics** | Numeric values over time — CPU, memory, network | Prometheus |
| **Logs** | Records from applications and systems | Loki, ELK |
| **Traces** | Request flow across services | Jaeger, Tempo |

> This lab focuses on **metrics monitoring** using Prometheus.

### Monitoring Stack Components

| Component | Role |
|-----------|------|
| **Prometheus** | Collects and stores metrics using pull-based approach |
| **Grafana** | Visualizes metrics using dashboards |
| **node-exporter** | Provides node-level metrics — CPU, memory, disk |
| **kube-state-metrics** | Provides Kubernetes object info — pod and deployment status |
| **Prometheus Operator** | Manages Prometheus configuration inside Kubernetes |

### Why Helm for Prometheus?

Without Helm, setting up Prometheus requires creating and managing multiple Kubernetes YAML files manually. The `kube-prometheus-stack` Helm chart deploys the complete monitoring stack — Prometheus, Grafana, node-exporter, kube-state-metrics, and Prometheus Operator — in a single command.

---

## 🗂️ Files in This Lab

| File | Description |
|------|-------------|
| `commands.md` | All commands used in the demo step by step |
| `promql-queries.md` | PromQL queries used during the demo with explanations |

---

## ⚙️ Cluster Setup Used

| Node | CPU | RAM |
|------|-----|-----|
| Master | 4 CPU | 4 GB |
| Worker 1 | 2 CPU | 2 GB |
| Worker 2 | 2 CPU | 2 GB |

OS: Rocky Linux | Tool: Helm + kubectl

---

## 🚀 Demo Overview

| Step | Action |
|------|--------|
| 1 | Verify Kubernetes cluster health |
| 2 | Verify Helm installation |
| 3 | Add Prometheus Community Helm repository |
| 4 | Create `monitoring` namespace |
| 5 | Install `kube-prometheus-stack` using Helm |
| 6 | Verify Helm release, pods, and services |
| 7 | Access Prometheus UI via port-forward |
| 8 | Check Prometheus scrape targets |
| 9 | Run basic PromQL queries |
| 10 | Access Grafana and explore dashboards |
| 11 | Verify node-exporter DaemonSet across nodes |
| 12 | Cleanup |

---

## 📊 PromQL Queries Used

See [`promql-queries.md`](./promql-queries.md) for all queries with explanations.

---

## ⚠️ Important Notes

- **Targets showing DOWN** in Prometheus is common in kubeadm-based clusters. Some control-plane components do not expose metrics by default. Core targets like node-exporter, kube-state-metrics, Prometheus, and Grafana should show UP.
- **Port-forwarding** is used for demo access. In production, use NodePort, Ingress, or LoadBalancer.
- **Alertmanager is disabled** in this setup to keep the stack lighter for learning purposes.

---

## ▶️ Watch the Video

👉 [**Monitor Kubernetes with Prometheus — YouTube**](https://youtu.be/heLmnidDLAI?si=E4ZMPLmjTY65HGCy)

---

## 🧹 Cleanup

```bash
helm uninstall monitoring -n monitoring
kubectl delete namespace monitoring
```

---

## ⬅️ Previous Lab

[Lab 18 — Helm in Kubernetes](../18-helm-kubernetes)
