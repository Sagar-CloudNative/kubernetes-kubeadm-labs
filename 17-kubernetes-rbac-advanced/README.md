# Lab 17 — Kubernetes RBAC: Advanced Real-World Scenarios

[![Kubernetes](https://img.shields.io/badge/Kubernetes-Labs-blue)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs)
[![License](https://img.shields.io/badge/License-MIT-green)](../LICENSE)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?logo=youtube)](https://www.youtube.com/@TechOpsTutorials)

> **Part of the [Kubernetes Real-World Labs (kubeadm)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs) series**

---

## 🎯 What You'll Learn

- How RBAC misconfigurations silently expand access in production
- How to design reusable RBAC roles for multiple teams at scale
- What RBAC does **not** protect you from
- How to debug RBAC failures like a production engineer

---

## 🧠 Concept Overview

This is Part 2 of RBAC. Instead of definitions and basics, this lab focuses on real production behavior — how access leaks happen, how RBAC is designed at scale, and how to debug when something fails.

### 4 Real-World Scenarios Covered

| Scenario | Topic |
|----------|-------|
| **1** | Accidental Over-Permission — ClusterRoleBinding misconfiguration |
| **2** | RBAC Design at Scale — reusable roles across multiple teams |
| **3** | What RBAC does NOT protect — container-level access |
| **4** | Real Debugging Workflow — `auth can-i` and role inspection |

---

## 🗂️ Files in This Lab

| File | Description |
|------|-------------|
| `commands.md` | All commands used across all 4 scenarios |

> This lab is command-driven. No YAML manifests are needed — all resources are created using `kubectl` imperative commands directly.

---

## 🚀 Demo Overview

### Scenario 1 — Accidental Over-Permission
- Create namespaces: `dev`, `qa`, `prod`
- Bind `view` ClusterRole to `dev` ServiceAccount cluster-wide
- Test — dev ServiceAccount can access `prod` namespace pods
- Fix — delete the ClusterRoleBinding
- Validate — access is correctly blocked again

### Scenario 2 — RBAC Design at Scale
- Create one reusable ClusterRole `read-only-access`
- Bind it to `dev` and `qa` teams using separate RoleBindings
- Validate — each team accesses only its own namespace
- Validate — cross-namespace access is blocked

### Scenario 3 — What RBAC Does NOT Protect
- Exec into a running pod in `dev`
- Show that once inside, RBAC has no control over container-level behavior
- Key point — RBAC controls Kubernetes API access, not application internals

### Scenario 4 — Real Debugging Workflow
- Trigger a Forbidden error
- Use `kubectl auth can-i` to verify permissions
- Inspect the role to find what's missing
- Understand why the request was denied

---

## ⚠️ Key Concepts

### Bad vs Good RBAC Design

| ❌ Bad Approach | ✅ Good Approach |
|----------------|-----------------|
| Create roles per user | Create reusable roles |
| Too many roles, hard to maintain | One role = one permission model |
| No reusability | Multiple bindings per team |
| Difficult to manage at scale | Clean and scalable |

### What RBAC Controls vs What It Doesn't

| RBAC Controls | RBAC Does NOT Control |
|--------------|----------------------|
| Who can access Kubernetes API | What happens inside a container |
| Which namespaces a user can access | Application-level behavior |
| What actions are allowed on resources | Files inside the container |
| Pod creation, deletion, listing | Data the application processes |

---

## ▶️ Watch the Video

👉 [**Kubernetes RBAC Advanced — YouTube**](https://youtu.be/Wd4H3pGDew0?si=K-nDOfgzcVl0DAGX)

---

## 🧹 Cleanup

```bash
kubectl delete rolebinding dev-binding -n dev
kubectl delete rolebinding qa-binding -n qa
kubectl delete clusterrole read-only-access
kubectl delete deployment nginx -n dev
kubectl delete deployment nginx -n qa
kubectl delete deployment nginx -n prod
kubectl delete ns dev qa prod

# Verify
kubectl get ns | grep -E 'dev|qa|prod'
```

---

## ⬅️ Previous Lab

[Lab 13 — Kubernetes RBAC Basics](../13-kubernetes-rbac)
