# 🚀 Canary Deployment in Kubernetes

This lab demonstrates **Canary Deployment in Kubernetes** using a simple and practical approach.

We simulate traffic split using replicas and understand how gradual rollout and rollback works in real environments.

---

## 🎯 Architecture Overview

![Canary Deployment](./assets/canary-deployment-thumbnail.png)

---

## 📌 What You Will Learn

✔ What is Canary Deployment  
✔ How traffic is distributed across pods  
✔ How to simulate 90/10 traffic split  
✔ How to gradually increase traffic  
✔ How to safely rollback  

---

## 🧪 Lab Steps (High-Level)

1️⃣ Deploy v1 (stable version)  
2️⃣ Create Service  
3️⃣ Deploy v2 (canary version)  
4️⃣ Observe traffic behavior  
5️⃣ Increase traffic to v2  
6️⃣ Perform full rollout  
7️⃣ Rollback if needed  

---

## 📂 Commands & YAML Files

👉 Full step-by-step lab guide:

📄 [commands.md](./commands.md)

---

## 📁 Folder Structure

```bash
09-kubernetes-canary-deployment/
 ├── README.md
 ├── commands.md
 ├── v1-deployment.yaml
 ├── v2-deployment.yaml
 ├── service.yaml
 └── assets/
      └── canary-deployment-thumbnail.png
```

---

## 🧠 Key Concept

Kubernetes does **NOT** split traffic by percentage.  
Traffic is distributed based on **number of pods**, so results are approximate.

---

## 📌 Summary

Canary Deployment helps you:

- Release changes safely  
- Test in real conditions  
- Reduce production risk  

---

⭐ If you find this helpful, consider starring the repo!
