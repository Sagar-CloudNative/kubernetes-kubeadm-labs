# Lab 01 – Build a 2-Node Kubernetes Cluster using kubeadm

In this lab we build a **2-node Kubernetes cluster on a laptop** using:

- VirtualBox
- Rocky Linux
- kubeadm
- containerd

This setup is useful for:

- Learning Kubernetes administration
- DevOps practice labs
- CKA exam preparation
- Understanding real cluster architecture

---

## 🎥 YouTube Tutorial

Watch the full step-by-step demo here:

https://youtu.be/YOUR_VIDEO_LINK

---

# Cluster Architecture

Control Plane Node
- kube-apiserver
- controller-manager
- scheduler
- etcd

Worker Node
- kubelet
- kube-proxy
- container runtime

---

# Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VirtualBox |
| OS | Rocky Linux |
| Master Node | 2 CPU / 4GB RAM |
| Worker Node | 2 CPU / 2GB RAM |
| Container Runtime | containerd |
| Kubernetes Tool | kubeadm |

---

# Cluster Setup Flow

1 Create virtual machines  
2 Configure hostnames and networking  
3 Disable swap  
4 Install container runtime  
5 Install kubeadm / kubelet / kubectl  
6 Initialize control plane  
7 Install CNI plugin  
8 Join worker node  
9 Verify cluster  

---

# Lab Directory

| File | Description |
|---|---|
| commands.md | All commands used in this lab |
| kubeadm-init.md | kubeadm initialization details |

---

# Final Result

After completing this lab you will have:
# Final Result

After completing this lab you will have:


k8s-master Ready control-plane
k8s-worker Ready worker


Your **2-node Kubernetes cluster will be fully functional**.

---

# Next Lab

➡ Kubernetes CNI Networking (Flannel)

---

# TechOps Tutorials

Hands-on Kubernetes and DevOps labs designed for **real-world learning**.
