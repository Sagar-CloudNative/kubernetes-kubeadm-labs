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

## 📚 Labs Included

| Lab # | Topic | Status |
| :--- | :--- | :--- |
| **01** | Build a 2-Node Kubernetes Cluster using kubeadm | ✅ Available |
| **02** | Kubernetes CNI Networking (Flannel) | ✅ Available |
| **03** | Scale a Kubernetes Cluster by Adding a Worker Node | ✅ Available |
| **04** | Kubernetes Scheduling (Taints, Tolerations, Affinity) | ✅ Available |
| **05** | Kubernetes Persistent Storage (PVC, Access Modes, Reclaim Policy) | ✅ Available |
| **06** | Kubernetes Storage Troubleshooting (Real-World Scenarios) | ✅ Available |
| **07** | Kubernetes LoadBalancer on Bare Metal using MetalLB | ✅ Available |
| **08** | Kubernetes etcd Backup & Restore (Disaster Recovery) | ✅ Available |
| **09** | Blue-Green Deployment in Kubernetes | ✅ Available |
| **10** | Canary Deployment in Kubernetes  | ⏳ Upcoming  |

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
> **Getting Started?** Begin with [Lab 01: Cluster Setup](./01-kubeadm-cluster-setup/commands.md) to prepare your environment.
