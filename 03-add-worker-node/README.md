# 🚀 Lab 03: Adding a Worker Node to a Kubernetes Cluster

In this lab we expand our Kubernetes cluster by **adding a new worker node using kubeadm** and demonstrate how Kubernetes scales workloads across multiple nodes.

In real production environments, cluster capacity must increase as applications grow. Kubernetes solves this using **horizontal scaling**, where we add more nodes instead of upgrading a single machine.

This lab shows the **complete real-world process** of expanding a Kubernetes cluster.

---

## 🎥 Video Tutorial

Watch the step-by-step cluster expansion guide here:

👉 [**Watch the Lab 03 Tutorial**](https://youtu.be/tFtJE2BwmVI?si=ckN1OyUWs5PYZZMY)

---

## 📘 Topics Covered

In this lab you will learn:

- How to inspect **node capacity**
- How workloads behave when a cluster has limited nodes
- The difference between **vertical scaling vs horizontal scaling**
- How to prepare a **new worker node**
- How to securely join a node using `kubeadm join`
- How Kubernetes automatically schedules pods across nodes

---

## 📂 Lab Files

| File | Description |
|-----|-------------|
| commands.md | All commands used in this lab |

---

## 📋 Prerequisites

You must complete the previous labs before starting this one:

1. **[Lab 01: Kubernetes Cluster Setup](../01-kubeadm-cluster-setup/README.md)**
2. **[Lab 02: Kubernetes Networking (CNI)](../02-kubernetes-networking-cni/README.md)**

Your cluster should currently have:

- 1 Control Plane Node
- 1 Worker Node
- CNI networking installed

---

## 🧪 What We Will Demonstrate

In this lab we simulate a **real Kubernetes capacity problem**.

1️⃣ Deploy multiple pods to create workload pressure.

2️⃣ Because the cluster has only **one worker node**, all pods run on the same machine.

3️⃣ Add a **new worker node** to increase cluster capacity.

4️⃣ Scale the application again and observe Kubernetes **automatically distributing pods across nodes**.

---

## 🎯 Result

After completing this lab you will understand:

- How Kubernetes clusters scale using **additional nodes**
- How new worker nodes join a cluster securely
- How Kubernetes **automatically schedules pods across nodes**
- Why horizontal scaling is a core Kubernetes design principle

---

## 🧠 Real-World Note

In cloud platforms like **AWS, Azure, or GCP**, worker nodes are usually part of **node pools or auto scaling groups**.

When workload increases, new nodes can be added automatically.

In this lab environment we use **kubeadm**, so we manually add a worker node to understand how the cluster infrastructure works.

---

| [« Lab 02: Networking](../02-kubernetes-networking-cni/README.md) | [Main Directory](../../README.md) | [Lab 04: Kubernetes Services »](../04-kubernetes-services/README.md) |
| :--- | :---: | ---: |
