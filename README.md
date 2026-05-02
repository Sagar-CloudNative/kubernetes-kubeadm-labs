# ☸️ Kubernetes Real-World Labs (kubeadm)

![Kubernetes](https://img.shields.io/badge/Kubernetes-Labs-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Platform](https://img.shields.io/badge/Platform-RHEL%20%7C%20Rocky%20Linux-red)
![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?logo=youtube)

This lab series uses a simple multi-node Kubernetes cluster built with **kubeadm**.

```
        +----------------------+
        |   Control Plane      |
        |      k8s-master      |
        |  API Server / etcd   |
        +----------+-----------+
                   |
        +----------+-----------+
        |                      |
+-------+--------+     +-------+--------+
|   Worker Node  |     |   Worker Node  |
|   k8s-worker   |     |  k8s-worker2   |
| Pods / CNI     |     | Pods / CNI     |
+----------------+     +----------------+
```

Hands-on Kubernetes labs designed for **System Administrators and Developers**.  
This repository contains all commands, YAML files, and instructions used in the video tutorials.

---

## 🎥 Full Video Playlist

Watch the complete **Kubernetes Real-World Labs (kubeadm)** tutorial series:

👉 [**Watch the Playlist on YouTube**](https://youtube.com/playlist?list=PL4y5PiOmBnA8-pXEE3Xwz28_QF-geOurS)

---

### 📚 Labs Included

| Lab # | Topic | Status |
| :--- | :--- | :--- |
| 01 | [Build a 2-Node Kubernetes Cluster using kubeadm](./01-kubeadm-cluster-setup) | ✅ Available |
| 02 | [Kubernetes CNI Networking (Flannel)](./02-kubernetes-networking-cni) | ✅ Available |
| 03 | [Scale a Kubernetes Cluster by Adding a Worker Node](./03-add-worker-node) | ✅ Available |
| 04 | [Kubernetes Scheduling (Taints, Tolerations, Affinity)](./04-kubernetes-scheduling) | ✅ Available |
| 05 | [Kubernetes Persistent Storage (PVC, Access Modes)](./05-kubernetes-persistent-storage) | ✅ Available |
| 06 | [Kubernetes Storage Troubleshooting](./06-kubernetes-storage-troubleshooting) | ✅ Available |
| 07 | [Kubernetes LoadBalancer on Bare Metal (MetalLB)](./07-kubernetes-loadbalancer-metallb) | ✅ Available |
| 08 | [Kubernetes etcd Backup & Restore](./08-kubernetes-etcd-backup-restore) | ✅ Available |
| 09 | [Blue-Green Deployment in Kubernetes](./09-blue-green-kubernetes-demo) | ✅ Available |
| 10 | [Canary Deployment in Kubernetes](./10-kubernetes-canary-deployment) | ✅ Available |
| 11 | [Deployment Strategies Comparison](./11-deployment-strategies-comparison) | ✅ Available |
| 12 | [Production Deployment Workflow](./12-production-deployment-workflow) | ✅ Available |
| 13 | [Kubernetes RBAC (Roles & RoleBindings)](./13-kubernetes-rbac) | ✅ Available |
| 14 | [HPA Autoscaling (Horizontal Pod Autoscaler)](./14-hpa-autoscaling) | ✅ Available |
| 15 | [Control Plane Internals & Troubleshooting](./15-control-plane-internals) | ✅ Available |
| 16 | Namespace Governance & Cluster Control | ⏳ Upcoming |

---

## 🚀 Quick Start

Clone the repository:

```bash
git clone https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs.git
cd kubernetes-kubeadm-labs
```

## 📂 Repository Structure

```text
kubernetes-kubeadm-labs/
├── 01-kubeadm-cluster-setup/
│   ├── README.md           # Detailed lab instructions
│   ├── commands.md         # Preparation & installation commands
│   ├── kubeadm-init.md     # Cluster initialization steps
│   └── images/             # Lab screenshots and diagrams
├── 02-kubernetes-networking/
└── ...
```

**Each lab folder includes:**
* 📖 **Step-by-step instructions**
* 💻 **Raw commands** for easy copy-pasting
* 📄 **YAML manifests** for deployments
* 📸 **Supporting screenshots**

---

## 👨‍💻 Who This Is For

* **System Administrators** looking to modernize their infrastructure skills.
* **DevOps Engineers** preparing for CKA/CKAD certifications.
* **Developers** who want to understand how their code runs in a cluster.

---

## 🤝 Contributing & Support

If you find these labs helpful:
1. **Star** this repository to show your support!
2. **Subscribe** to the YouTube channel for new lab updates.
3. **Open an Issue** if you run into any setup problems.

---

> [!TIP]
> **Getting Started?** Begin with [Lab 01: Cluster Setup](./01-kubeadm-cluster-setup/README.md) to prepare your environment.
