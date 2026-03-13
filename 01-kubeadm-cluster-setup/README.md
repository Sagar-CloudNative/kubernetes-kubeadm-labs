![YouTube](https://img.shields.io/badge/YouTube-TechOpsTutorials-red)
# Lab 01 – Build a 2-Node Kubernetes Cluster using kubeadm

In this lab we build a **2-node Kubernetes cluster on a laptop** using:

- VirtualBox
- Rocky Linux
- kubeadm
- containerd
- Flannel CNI

This lab simulates a **real Kubernetes multi-node environment** instead of a single-node setup like Minikube.

---

# 🎥 Full Video Tutorial

Watch the complete step-by-step demo here:

https://youtu.be/ir01Umpmefc

---

# Lab Architecture

Control Plane Node (k8s-master)

Components:
- kube-apiserver
- scheduler
- controller-manager
- etcd

Worker Node (k8s-worker)

Components:
- kubelet
- kube-proxy
- container runtime
- application pods

---

# Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VirtualBox |
| OS | Rocky Linux |
| Control Plane | 2 vCPU / 4GB RAM |
| Worker Node | 2 vCPU / 2GB RAM |
| Container Runtime | containerd |
| Kubernetes Installer | kubeadm |
| Network Plugin | Flannel |

---

# Cluster Setup Flow

Step 1 – Create Virtual Machines  
Step 2 – Configure hostnames and networking  
Step 3 – Disable swap  
Step 4 – Install container runtime (containerd)  
Step 5 – Install kubeadm, kubelet, kubectl  
Step 6 – Initialize control plane  
Step 7 – Install CNI networking  
Step 8 – Join worker node  
Step 9 – Verify cluster

---

# Expected Result

Run:

```bash
kubectl get nodes
```

Example output:

```text
NAME         STATUS   ROLES           AGE   VERSION
k8s-master   Ready    control-plane   10m   v1.29.x
k8s-worker   Ready    <none>          8m    v1.29.x
```

---

Your **multi-node Kubernetes cluster is now running locally.**

---

# Repository Structure

```
01-kubeadm-cluster-setup
├── README.md
├── commands.md
└── kubeadm-init.md
```

---

# Next Lab

➡ Kubernetes CNI Networking Explained (Flannel)

---

# TechOps Tutorials

Hands-on Kubernetes and DevOps labs designed for **real-world learning**.
