# Lab 16 — Namespace Governance & Cluster Control

[![Kubernetes](https://img.shields.io/badge/Kubernetes-Labs-blue)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs)
[![License](https://img.shields.io/badge/License-MIT-green)](../LICENSE)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?logo=youtube)](https://www.youtube.com/@TechOpsTutorials)

> **Part of the [Kubernetes Real-World Labs (kubeadm)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs) series**

---

## 🎯 What You'll Learn

- What is the **Noisy Neighbor problem** in shared clusters
- How **ResourceQuota** acts as a budget for a namespace
- How **LimitRange** auto-injects default CPU & memory values
- How **Object Count Quota** controls resource creation (e.g. LoadBalancers)
- The key difference between container-level and namespace-level control

---

## 🧠 Concept Overview

### The Problem — Noisy Neighbor
In a shared Kubernetes cluster, one team can deploy workloads without setting any limits. This causes that workload to consume a large portion of cluster resources, slowing down or crashing other teams' applications.

### The Solution — Two Tools

| Tool | Scope | Who Configures |
|------|-------|----------------|
| **ResourceQuota** | Namespace level — total CPU, memory, object count | Cluster Admin |
| **LimitRange** | Container level — default + min/max values | Cluster Admin |

### Key Difference

| | Requests & Limits | ResourceQuota & LimitRange |
|---|---|---|
| **Works at** | Container level | Namespace / Team level |
| **Set by** | Developer | Cluster Admin |
| **If exceeded** | Pod gets killed or stays pending | Request is blocked completely |

---

## 🗂️ Files in This Lab

| File | Description |
|------|-------------|
| `resourcequota.yaml` | Applies CPU & memory budget to `team-alpha` |
| `deploy-ok.yaml` | Deployment within quota limits |
| `deploy-fail.yaml` | Deployment that tries to exceed quota |
| `limitrange.yaml` | Applies default CPU & memory to containers in `team-beta` |
| `no-resource.yaml` | Pod created without any resource section |
| `object-quota.yaml` | Limits number of LoadBalancers in `team-beta` |
| `commands.md` | All commands used in the demo |

---

## 🚀 Demo Overview

### Demo 1 — ResourceQuota (Hard Limit)
- Create namespace `team-alpha`
- Apply a quota: 2 CPU, 4Gi memory
- Deploy within limit → succeeds
- Try to exceed limit → Kubernetes blocks it
- Fill quota fully → complete block, zero pods created

### Demo 2 — LimitRange (Default Injection)
- Create namespace `team-beta`
- Apply LimitRange with default CPU & memory values
- Deploy a pod **without** any resource section
- Kubernetes **automatically injects** the values — this is the wow moment

### Demo 3 — Object Count Quota
- Limit `team-beta` to only **1 LoadBalancer**
- Create first LoadBalancer → succeeds
- Try second LoadBalancer → blocked with Forbidden error

---

## ▶️ Watch the Video

👉 [**Namespace Governance & Cluster Control — YouTube**](https://www.youtube.com/watch?v=fhYWxlbyNaA)

---

## 🧹 Cleanup

```bash
kubectl delete namespace team-alpha
kubectl delete namespace team-beta
kubectl get ns
```

---

## ⬅️ Previous Lab

[Lab 15 — Control Plane Internals & Troubleshooting](../15-control-plane-internals)
