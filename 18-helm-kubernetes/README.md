# Lab 18 — Helm in Kubernetes: Install, Use & Manage Applications

[![Kubernetes](https://img.shields.io/badge/Kubernetes-Labs-blue)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs)
[![License](https://img.shields.io/badge/License-MIT-green)](../LICENSE)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?logo=youtube)](https://www.youtube.com/@TechOpsTutorials)

> **Part of the [Kubernetes Real-World Labs (kubeadm)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs) series**

---

## 🎯 What You'll Learn

- Why Helm is needed and what problems it solves
- Key Helm concepts — Charts, Releases, Repositories
- How to install Helm and deploy applications using OCI-based charts
- How to customize deployments using `values.yaml`
- How to upgrade, rollback, and uninstall Helm releases

---

## 🧠 Concept Overview

### The Problem — Managing YAML at Scale

In Kubernetes, even a simple application requires multiple YAML files — deployments, services, configmaps, ingress, and more. As applications grow across environments (dev, QA, prod), managing and reusing these files manually becomes error-prone and time-consuming.

### The Solution — Helm

Helm is the **package manager for Kubernetes**. Just like `apt` on Ubuntu or `dnf` on RHEL manages software packages, Helm manages Kubernetes applications using reusable **Charts**.

### Key Concepts

| Concept | Description |
|---------|-------------|
| **Chart** | A package containing Kubernetes templates and configuration files |
| **Release** | An installed instance of a Helm chart in the cluster |
| **Repository** | A location where Helm charts are stored and shared |
| **values.yaml** | Central configuration file — override defaults without editing templates |

### Helm vs Raw YAML

| | Raw YAML | Helm |
|---|---|---|
| **Deployment** | Manual, multiple files | Single command |
| **Reusability** | Copy-paste across environments | Reusable templates with variables |
| **Upgrades** | Manual, risky | Built-in, safe |
| **Rollback** | Not simple | One command |
| **Version tracking** | Extra effort | Built-in release history |

---

## 🗂️ Files in This Lab

| File | Description |
|------|-------------|
| `custom-values.yaml` | Custom Helm values — 2 replicas, NodePort 30080 |
| `commands.md` | All commands used in the demo step by step |

---

## 🚀 Demo Overview

| Step | Action |
|------|--------|
| 1 | Verify Kubernetes cluster health |
| 2 | Install Helm using the official script |
| 3 | Deploy NGINX using OCI-based Bitnami Helm chart |
| 4 | Verify pods, services, and Helm release |
| 5 | View default chart values |
| 6 | Create custom `values.yaml` — 2 replicas, NodePort |
| 7 | Upgrade the release using custom values |
| 8 | Access NGINX from browser via NodePort |
| 9 | View Helm release history |
| 10 | Perform rollback to revision 1 |
| 11 | Uninstall the release and clean up |

---

## ⚙️ Cluster Setup Used

- 1 Master Node
- 2 Worker Nodes
- kubeadm-based Kubernetes cluster

---

## ▶️ Watch the Video

👉 [**Helm in Kubernetes — YouTube**](https://www.youtube.com/@TechOpsTutorials)

---

## 🧹 Cleanup

```bash
helm uninstall my-nginx -n helm-demo
kubectl delete namespace helm-demo

# Verify
kubectl get ns
```

---

## ⬅️ Previous Lab

[Lab 17 — Kubernetes RBAC Advanced](../17-kubernetes-rbac-advanced)
