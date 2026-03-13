# 🌐 Lab 04: Kubernetes Services

In this lab we will learn how **Kubernetes Services** provide stable networking and load balancing for pods.

Pods in Kubernetes are **ephemeral**, meaning they can be recreated at any time with a new IP address.  
A **Service** solves this problem by providing a **stable virtual IP and DNS name** for accessing pods.

---

## 🎥 Video Tutorial

Watch the step-by-step cluster expansion guide here:

👉 [**Watch the Lab 04 Tutorial**](https://youtu.be/vSJ2Q2gE6X8?si=jjO-UAycs666LN4Z)
---

## 📘 Topics Covered

- Why Kubernetes Services are required
- Creating a Service for a Pod
- Understanding ClusterIP
- Verifying service networking
- Accessing pods through a Service

---

## 📂 Lab Files

| File | Description |
|-----|-------------|
| commands.md | All commands used in this lab |
| service-demo.md | Step-by-step Service creation demo |

---

## 📋 Prerequisites

You must complete the following labs:

1. **Lab 01 — Cluster Setup**
2. **Lab 02 — Kubernetes Networking (CNI)**
3. **Lab 03 — Adding Worker Nodes**

---

## 🎯 Result

After completing this lab you will understand:

- How Kubernetes Services provide stable networking
- How traffic is load balanced to pods
- How to access applications using a Service

---

| [« Lab 03: Add Worker Node](../03-add-worker-node/README.md) | [Main Directory](../../README.md) | [Lab 05: Deployments »](../05-kubernetes-deployments/README.md) |
| :--- | :---: | ---: |
