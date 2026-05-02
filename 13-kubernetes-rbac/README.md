# 🔐 Lab 13: Kubernetes RBAC Deep Dive

RBAC (Role-Based Access Control) is the standard for regulating access to computer or network resources based on the roles of individual users within an enterprise. In Kubernetes, RBAC determines **who** can do **what** in **which** namespace.

---

## 🎥 Video Tutorial

Watch the full practical guide on Kubernetes RBAC:

👉 [**Kubernetes RBAC Deep Dive | Roles & ClusterRoles Explained**](https://youtu.be/kE2rtuQV4W0?si=mHUl2LVbQhB_VU8z)

---

## 📘 Core Concepts

### 1. Authentication (AuthN) vs Authorization (AuthZ)
* **Authentication:** "Who are you?" (Certificates, Tokens).
* **Authorization:** "What can you do?" (RBAC).

### 2. Scope of Permissions
| Component | Scope | Use Case |
| :--- | :--- | :--- |
| **Role** | Namespace | Pods, Services, Secrets within one namespace. |
| **ClusterRole** | Cluster | Nodes, PVs, or resources across all namespaces. |

---

## 🚀 Lab Objectives
1. Create `dev` and `prod` namespaces.
2. Create a `ServiceAccount` for a developer.
3. Define a **Role** (Namespace-level) and bind it.
4. Test permissions using `kubectl auth can-i`.
5. Define a **ClusterRole** (Cluster-level) and bind it.
6. Verify access across the entire cluster.

---

## 📂 Lab Files

| File | Description |
| :--- | :--- |
| [**commands.md**](./commands.md) | Step-by-step terminal commands |
| [**role.yaml**](./role.yaml) | Defines `get/list` permissions for Pods |
| [**rolebinding.yaml**](./rolebinding.yaml) | Assigns the Role to the ServiceAccount |
| [**clusterrole.yaml**](./clusterrole.yaml) | Defines `get/list` permissions for Nodes |
| [**clusterrolebinding.yaml**](./clusterrolebinding.yaml) | Assigns ClusterRole globally |

---

## ⭐ Best Practices
* **Principle of Least Privilege:** Grant only the minimum permissions required.
* **Avoid `*` (Wildcards):** Do not give "all" permissions unless absolutely necessary.
* **Namespace Isolation:** Use Roles over ClusterRoles whenever possible to limit the "blast radius" of a compromised account.

---
| [« Lab 12: Production Workflow](../12-production-deployment-workflow/README.md) | | [Main Directory](../../README.md) | [Next: HPA »](../../) |
| :--- | :---: | ---: |
