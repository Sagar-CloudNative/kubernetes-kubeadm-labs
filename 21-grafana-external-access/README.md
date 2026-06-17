# Lab 21 — Access Grafana Externally in Kubernetes | NodePort vs LoadBalancer vs Ingress

[![Kubernetes](https://img.shields.io/badge/Kubernetes-Labs-blue)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs)
[![License](https://img.shields.io/badge/License-MIT-green)](../LICENSE)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?logo=youtube)](https://www.youtube.com/@TechOpsTutorials)

> **Part of the [Kubernetes Real-World Labs (kubeadm)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs) series**

---

## 🎯 What You'll Learn

- Three different ways to expose Grafana externally in Kubernetes
- How NodePort, LoadBalancer (MetalLB), and Ingress work and differ
- How to install and configure NGINX Ingress Controller using Helm
- How to create an Ingress resource with hostname-based routing
- How to verify the complete traffic flow from browser to pod

---

## 🧠 Concept Overview

By default, Grafana is exposed as a `ClusterIP` service — only accessible from inside the cluster. This lab covers three methods to expose it externally.

### Comparison

| Method | How to Access | Production Ready | Use Case |
|--------|--------------|-----------------|---------|
| **Port Forward** | `localhost:3000` | ❌ | Quick testing only |
| **NodePort** | `<node-ip>:<port>` | ❌ | Dev/lab environments |
| **LoadBalancer (MetalLB)** | `<external-ip>` | ⚠️ | On-prem without DNS |
| **Ingress** | `grafana.example.local` | ✅ | Production environments |

### Traffic Flow — Ingress

```
Browser → grafana.techopstutorials.local
       → MetalLB IP (192.168.56.241)
       → NGINX Ingress Controller
       → monitoring-grafana Service
       → Grafana Pod (10.244.1.2:3000)
```

---

## 🗂️ Files in This Lab

| File | Description |
|------|-------------|
| `grafana-ingress.yaml` | Ingress resource for hostname-based Grafana access |
| `commands.md` | All commands used across all three methods |

---

## ⚙️ Prerequisites

- Prometheus + Grafana installed via Helm (Lab 19)
- MetalLB installed and configured with IP pool `192.168.56.240–192.168.56.250`
- kubeadm-based Kubernetes cluster on Rocky Linux

> If you haven't set up MetalLB, check the MetalLB lab in this series first.

---

## 🚀 Demo Overview

| Method | Steps |
|--------|-------|
| **Port Forward (recap)** | Retrieve password → port-forward → access via browser |
| **NodePort** | Patch service to NodePort → get assigned port → access via node IP |
| **LoadBalancer** | Patch service to LoadBalancer → MetalLB assigns IP → access via IP |
| **Ingress** | Install NGINX Ingress Controller → create Ingress resource → configure hosts file → access via hostname |

---

## 🌐 Hosts File Entry (for Ingress lab access)

Add this entry to your hosts file to simulate DNS resolution:

```
192.168.56.241   grafana.techopstutorials.local
```

**Linux:**
```bash
sudo vi /etc/hosts
```

**Windows** (open Notepad as Administrator):
```
C:\Windows\System32\drivers\etc\hosts
```

**Verify on Windows PowerShell:**
```powershell
Resolve-DnsName grafana.techopstutorials.local
```

---

## ▶️ Watch the Video

👉 [**Access Grafana Externally — YouTube**](http://youtube.com/watch?v=lV4u5lUZdm4)

---

## 🧹 Cleanup

```bash
# Remove Ingress resource
kubectl delete -f grafana-ingress.yaml

# Remove NGINX Ingress Controller
helm uninstall ingress-nginx -n ingress-nginx
kubectl delete namespace ingress-nginx

# Revert Grafana service to ClusterIP
kubectl patch svc monitoring-grafana \
  -n monitoring \
  -p '{"spec":{"type":"ClusterIP"}}'
```

---

## ⬅️ Previous Lab

[Lab 20 — Grafana DNS & Flannel Troubleshooting](../20-grafana-dns-flannel-troubleshooting)
