# 🚀 Lab 03: Adding Worker Nodes to a Kubernetes Cluster

In this lab, we expand our Kubernetes cluster by adding an additional worker node using the `kubeadm join` process. This is a fundamental task in production environments when your cluster requires more compute capacity to handle workloads.

---

## 🎥 Video Tutorial

Watch the step-by-step cluster expansion guide here:

👉 [**Watch the Lab 03 Tutorial**](https://youtu.be/tFtJE2BwmVI?si=ckN1OyUWs5PYZZMY)

---

## 📘 Topics Covered

* **Join Architecture:** How worker nodes communicate with the API server to register.
* **Token Management:** Understanding `kubeadm` tokens and CA cert hashes.
* **The Join Process:** Executing the bootstrap process on a fresh node.
* **Node Verification:** Ensuring the new node transitions to the `Ready` state.
* **Capacity Validation:** Checking the total cluster resources after scaling.

---

## 📁 Lab Resources

| File | Description |
| :--- | :--- |
| [**commands.md**](./commands.md) | Technical commands to prepare the node and join the cluster. |
| [**join-worker-node.md**](./join-worker-node.md) | Step-by-step guide for the worker node registration. |

---

## 📋 Prerequisites

> [!IMPORTANT]
> To follow this lab, you must have an existing control plane running from the previous labs.

* **Lab 01 & 02 Completed:** You must have a Master node and at least one Worker node active.
* **New Node Ready:** A new Linux instance (RHEL/Rocky/CentOS) with `kubeadm` and `containerd` installed.
* **Network Connectivity:** The new node must be able to reach the Master node on port `6443`.

---

## 🎯 Learning Results

After completing this lab, you will understand:
1. How nodes securely join a Kubernetes cluster.
2. How to manage scaling by adding worker nodes on-demand.
3. How to verify node health and troubleshoot common join issues.

---
| [« Lab 02: Networking](../02-kubernetes-networking-cni/README.md) | [Main Directory](../../README.md) | [Lab 04: Services »](../04-kubernetes-services/README.md) |
| :--- | :---: | ---: |
