# 🌐 Lab 02: Kubernetes Networking (CNI)

In this lab, we explore the fundamentals of **Kubernetes Networking** and how a **CNI (Container Network Interface) plugin** enables seamless communication between Pods across different nodes.

This lab builds directly upon the cluster we created in **Lab 01**.

---

## 🎥 Video Tutorial

Watch the full deep-dive into Kubernetes Networking here:

👉 [**Watch the Lab 02 Tutorial**](https://youtu.be/Yr2t8c2b2bk?si=vGXkHYa-MQR0ZbZb)

---

## 📘 Topics Covered

* **What is CNI?** Understanding the Container Network Interface.
* **The "NotReady" Mystery:** Why nodes require a plugin to become healthy.
* **Flannel Overview:** A lightweight, L3-based network fabric.
* **Pod-to-Pod Communication:** How traffic moves across the cluster.
* **Validation & Troubleshooting:** Tools to verify the networking layer.

---

## 📁 Lab Resources

| File | Description |
| :--- | :--- |
| [**commands.md**](./commands.md) | Technical commands to install and verify the CNI. |
| [**networking-demo.md**](./networking-demo.md) | Step-by-step validation of cross-node communication. |

---

## 📋 Prerequisites

> [!IMPORTANT]
> You must have a functional cluster ready. If you haven't set one up yet, complete **[Lab 01: 2-Node Cluster Setup](../01-kubeadm-cluster-setup/README.md)** first.

* 1 Master Node (k8s-master)
* 1 Worker Node (k8s-worker)
* `kubectl` access configured

---

## 🎯 Learning Results

After completing this lab, you will be able to:
1. Identify why a cluster stays in `NotReady` status without a CNI.
2. Deploy the **Flannel** network fabric.
3. Validate that Pods can communicate across different physical/virtual hosts.
4. Use `ip route` and `ping` to trace internal Kubernetes traffic.

---

> [!TIP]
> **Pro-Tip:** Once networking is up, all your core Kubernetes system pods (like CoreDNS) will finally move into a `Running` state!
