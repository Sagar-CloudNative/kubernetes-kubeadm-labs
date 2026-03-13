# 🚀 Lab 04: Kubernetes Scheduling (Taints, Tolerations & Affinity)

In this lab we explore **how Kubernetes decides where Pods run inside a cluster**.

Kubernetes uses a component called the **Scheduler** to select the best node for a Pod.  
By default, Kubernetes automatically chooses a node, but in real production environments we often need **more control over Pod placement**.

This lab demonstrates the core mechanisms used in real clusters to influence scheduling decisions.

---

## 🎥 Video Tutorial

Watch the full explanation and demo:

TechOpsTutorials — Kubernetes Scheduling Deep Dive 
👉 [**Watch the Lab 04 Tutorial**](https://youtu.be/vSJ2Q2gE6X8?si=Cx5oG2ZwtKPjANQt)

---

## 📘 Topics Covered

In this lab we will demonstrate:

- Control Plane protection using **default taints**
- Reserving nodes using **Taints**
- Allowing pods using **Tolerations**
- Advanced scheduling using **Node Affinity**
- High availability using **Pod Anti-Affinity**

These features are commonly used in **production Kubernetes clusters**.

---

## 📂 Lab Files

| File | Description |
|-----|-------------|
| commands.md | All commands used in this demo |
| tolerant-pod.yaml | Pod with toleration |
| affinity-pod.yaml | Pod using Node Affinity |
| web-deploy.yaml | Deployment using Pod Anti-Affinity |

---

## 📋 Prerequisites

Before starting this lab you should have:

- kubeadm cluster
- 1 Control Plane Node
- 2 Worker Nodes
- CNI plugin installed (Flannel or similar)
- kubectl configured on control plane

Verify cluster:

```bash
kubectl get nodes
```

Expected example:

```
k8s-master    Ready   control-plane
k8s-worker    Ready
k8s-worker2   Ready
```

---

## 🧪 What We Demonstrate

### 1️⃣ Control Plane Protection

Kubernetes automatically taints the control plane to prevent normal workloads from running there.

---

### 2️⃣ Node Reservation Using Taints

We will taint a worker node to **reserve it for specific workloads**.

---

### 3️⃣ Tolerations

Pods with tolerations can run on tainted nodes.

---

### 4️⃣ Node Affinity

Node Affinity allows advanced scheduling rules based on **node labels**.

Example:
- SSD nodes
- GPU nodes
- High memory nodes

---

### 5️⃣ Pod Anti-Affinity

Pod Anti-Affinity ensures that replicas **run on different nodes** to improve availability.

If one node fails, other replicas continue running.

---

## 🎯 Learning Outcome

After completing this lab you will understand:

- How Kubernetes protects control plane nodes
- How to reserve nodes for special workloads
- How to control Pod placement using labels
- How to improve application availability with anti-affinity
- How Kubernetes scheduler evaluates scheduling rules

---

## 🧠 Real-World Use Cases

Production clusters commonly use these features for:

- Running **databases on dedicated nodes**
- Scheduling **GPU workloads**
- Running **storage-heavy workloads on SSD nodes**
- Spreading **application replicas across nodes**

---

| [« Lab 03: Add Worker Node](../03-add-worker-node/README.md) | [Main Directory](../../README.md) | [Lab 05: Kubernetes Deployments »](../05-kubernetes-deployments/README.md) |
| :--- | :---: | ---: |
